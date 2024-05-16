# SVN2GH-migration-tool

Action runner has to be the machine that has a copy of the svn repository to migrate to GH (o bajar uno por uno, checkout en otro svn server)


**PRE REQUISITOS:**
(requisitos para maquina vm/runner in actions/local compu)
- `brew install git`
- `brew install git-svn`
- `brew install git-lfs`

check installations:

- `git-svn --version`
- `git --version`
- `svn --help`

1.**Checkout repo from svn**
 
- create folder svn-test
- cd svn-test

example svn repo:
`svn checkout https://svn.code.sf.net/p/circuit/code . `
(works like git clone, clones repo from server svn to runner)

2.**authors file - mapping of users**

get a list of authors in your Subversion project: 
```js
svn log -q | grep -e '^r' | awk 'BEGIN { FS = "|" } ; { print $2" = "$2 }' | sed 's/^[ \t]*//' | sort | uniq > authors.txt
```

 
4.**Update your authors file to be like this format**: 
`svnuser = name surname <email@github>`

example:
`vhamra = Virginia Hamra <vhamra@github.com>`


5.**Git SNV Clone**
If your Subversion repository has a standard format, with “trunk”, “branches”, and “tags” folders, run
 git svn clone -s https://svn.code.sf.net/p/circuit/code svn-migration-poc --authors-file ./svn-test/authors.txt
 
6.**Branches & Tags**
BEWARE If your Subversion repository is non-standard, you can customize git svn to handle your repository. For more information, see git-svn in the Git documentation.

Handle branches and tags manually or add a script to convert them from remote svn objects to git actual branches/tags (*)

https://git-scm.com/docs/git-svn  
git clone does not clone branches under the refs/remotes/ hierarchy or any git svn metadata, or config. 

7.**In case migration freezes**
Git will check out each SVN revision and turn the revision into a Git commit.
If your repository has many files or a lot of history, this process will take a long time.
_The command may freeze. if so, you can begin where you ended by terminating the command with Ctrl+C, moving to your new directory, and then running **git svn fetch**_
 
8.Move into the directory for the newly-created Git repository.
 
9.**Push to GitHub Remote Repository**

To add your GitHub repository as a remote, run `git remote add origin URL`

`git push --mirror origin`

10.**GIT LFS for big files**
get info:

```
git lfs migrate info --everything --above=99MB
```

migrate (if any files):

```git lfs migrate import --everything --above=99MB```
    
If your repository contains any files that are larger than GitHub Enterprise Cloud's file size limit, your push may fail. Move the large files to Git LFS by running git lfs import, then try again.
 

