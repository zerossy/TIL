# Git Cheat Sheet

# Set up

## Git Config

**See all the config** 

```bash
git config --list #show all the settings in gitconfig
```

**Open config to edit**

```bash
git config --global -e #open .gitconfig file
```

**Set default editor**

```bash
git config --global core.editor "code --wait" # set default text editor for git 
```

**User settings**

```bash
git config --global user.name "name" #set user.name
git config --global user.email "email" #set user.email
git config user.name #check user.name
git config user.email #check user.email
```

**Set Auto CRLF**

```bash
git config --global core.autocrlf true #for Windows
git config --global core.autocrlf input #for Mac
```

**Git Aliases**

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

## Help

**Git official site**

[https://git-scm.com/docs](https://git-scm.com/docs)

**See help** 

```bash
git config --help #detail
git config -h #short
```

# Basic

## Git init

```bash
git init #initialise git 
rm -rf .git #delete .git 
```

## Show the working tree status

```bash
git status #full status
git status -s #short status
```

## Ignoring Files

Add files that should be ignored in **.gitignore** in project directory

For example: 

```bash
# ignore all .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in any directory named build
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory and any of its subdirectories
doc/**/*.pdf
```

## Staging files

```bash
git add a.txt #stage a.txt file
git add a.txt b.txt #stage a.txt, b.txt files
git add *.txt #stage all files ends with .txt 
git add * #stage all files except deleted files and files that begin with a dot
git add . #stage everything 
```

## Modifying files

**Removing files**

```bash
rm file.txt #delete file
git add file.txt #add to staging area
git rm file.txt # removes file from working directory and staging area
git rm --cached file.txt #removes from staging area only
git clean -fd #removes all untracked files
```

**Moving files**

```bash
git mv from.txt to.txt
git mv from.text /logs/from.text
```

## Viewing the Staged/Unstaged changes

```bash
git status #full status
git status -s #short status
git diff #changes in working directory
git diff --staged #changes in staging area
git diff --cached #same as --staged
```

### Visual Diff Tool

**Open .gitconfig and add below** 

```bash
[diff]
    tool = vscode
[difftool "vscode"]
    cmd = code --wait --diff $LOCAL $REMOTE
```

**Run Git Diff tool**

```bash
git difftool
```

## Commit

```bash
git commit #commit stagged files
git commit -m "Commit message" #commit stagged files with commit message
git commit -am "Commit message" #commit all files with commit message
```

## Log · History

### See history

```bash
git log #list of commits 
git log --patch #shows the difference introduced in each commit
git log -p #same as --patch
git log --state #abbreviated states for each commit
git log --oneline #oneline
git log --oneline --reverse #oneline, from the oldest to the newest 
```

### Formatting

```bash
git log --pretty=oneline #same as --oneline
git log --pretty=format:"%h - %an %ar %s" #formatting
git log --pretty=format:"%h %s" --graph #show graph
#Ellie's favorite
git log --graph --all --pretty=format:'%C(yellow)[%ad]%C(reset) %C(green)[%h]%C(reset) | %C(white)%s %C(bold red){{%an}}%C(reset) %C(blue)%d%C(reset)' --date=short
```

### Filtering

```bash
git log -2 #shows only the last n commits
git log --author="ellie" 
git log --before="2020-09-29"
git log --after="one week ago"
git log --grep="message" #finds in commit messages
git log -S="code" #finds in the code
git log -S="code" -p #same as above but with detail
git log file.txt #logs only for file.txt
git log -p file.txt # same as above + detail
```

### History of a file

```bash
git log file.txt #history of file.txt
git log --state file.txt #shows statistics
git log --patch file.txt #show the changes
```

### HEAD · Hash code

```bash
git log HEAD
git log HEAD~1
git log hash
```

### Viewing a commit

```bash
git show HEAD #shows the last commit
git show hash #shows the given commit
git show hash:file.txt #shows contents about corresponding file in the given commit
```

### Comparing

```bash
git diff hash1 hash2 #all changes between two commits
git diff hash1 hash2 file.txt #changes to file.txt only
```

## Tagging (like bookmarking)

### Creating

```bash
git tag v1.0.0 #lightweight tag on latest commit
git tag v1.0.0 hash #lightweight tag on the given commit
git show v.0.0 #shows the tag
git tag -a v.1.0.0 -m "message" #annotated tag
git tag -am v1.0.1 "message" #same above
```

### Listing

```bash
git tag #all the tags
git tag -l "v1.0.*" #search certain tags
```

```bash
+ checkout
git checkout v.1.0.1 #make head point stated version / switch to the corresponding commit
```

### Deleting

```bash
git tag -d v1.0.0 #delete the given tag
```

### Syncing with Remote

```bash
git push origin v1.0.0 #sharing the given tag with remote
git push origin --tags #sharing all the tags
git push origin --delete v1.0.0 #delete a remote tag 
```

### Checking out Tags

```bash
git checkout v1.0.0 #checkout certain tag
git checkout -b branchName v1.0.0 #create a new bracnh with the given tag 

```

# Branch
---
## _**Prerequisutes**_
> Git이 브랜치를 다루는 과정을 이해하려면 우선 Git이 데이터를 어떻게 저장하는지 알아야 한다.

> Git은 데이터를 Change Set이나 변경사항(Diff)으로 기록하지 않고 일련의 스냅샷으로 기록한다. Commit하면 Git은 _**현 Staging Area에 있는 데이터의 스냅샷에 대한 포인터, 저자나 커밋 메시지 같은 메타데이터, 이전 커밋에 대한 포인터 등을 포함하는 커밋 개체(커밋 Object)**_ 를 저장한다. 이전 커밋 포인터가 있어서 현재 커밋이 무엇을 기준으로 바뀌었는지를 알 수 있다. 

> 최초의 커밋을 제외한 나머지 커밋은 이전 커밋 포인터가 적어도 하나씩 있고 브랜치를 합친 Merge 커밋 같은 경우에는 이전 커밋 포인터가 여러 개 있다.
<br> 파일이 3개 있는 디렉토리가 하나 있고 이 파일을 Staging Area에 저장하고 커밋하는 경우를 생각해보자. 파일을 Stage 하면 Git 저장소에 파일을 저장하고(Git은 이것을 **Blob**이라 부른다) Staging Area에 해당 파일의 Checksum을 저장한다 _(SHA-1 사용)_.

> _**git commit**_ 으로 커밋하면 먼저 루트 디렉토리와 각 하위 디렉토리의 트리 개체 _(tree object)_ 를 Checksum과 함께 저장소에 저장한다. 그다음에 커밋 개체를 만들고 메타데이터와 루트 디렉토리 트리 개체를 가리키는 포인터 정보를 커밋 개체에 넣어 저장한다. 그래서 필요하면 언제든지 스냅샷을 다시 만들 수 있다.

> 이 작업을 마치고 나면 Git 저장소에는 다섯 개의 데이터 개체가 생긴다. 각 파일에 대한 Blob 세 개, 파일과 디렉토리 구조가 들어 있는 트리 개체 하나, 메타데이터와 루트 트리를 가리키는 포인터가 담긴 커밋 개체 하나이다.


_* 사진 첨부_

---
<br>

> 개발을 하다보면 코드를 통째로 복사하고 나서 원래 코드와는 상관없이 독립적으로 개발을 진행하는 경우가 종종 있는데, 이렇게 독립적으로 개발하는 것이 브랜치이다. <br>
_cf. Pro Git p. 55~, especially p.56!!_

* Git의 브랜치는 커밋 사이를 가볍게 이동할 수 있는 어떤 포인터 같은 것이다. 

* Git은 지금 작업하는 로컬 브랜치를 가리키는 _**HEAD**_ 라는 특수한 포인터를 통해 지금 작업 중인 브랜치가 무언인지 파악한다. 

* _**브랜치를 이동하면 (당연히) 워킹 디렉토리의 파일이 변경된다.**_

* 실제로 Git의 브랜치는 어떤 한 커밋을 가리키는 40글자의 SHA-1 checksum 파일에 불과하기 때문에 만들기도 쉽고 지우기도 쉽다. <br>(새로 브랜치를 하나 만드는 것은 41바이트 크기의 파일을(40자와 줄 바꿈 문자) 하나 만드는 것에 불과하다.)

> _**Git의 무결성** : Git 께서는 데이터를 어떻게 관리하는가?_ <br> 
Git은 데이터를 저장하기 전에 항상 Checksum을 구하고 그 Checksum으로 데이터를 관리한다. Checksum은 Git에서 사용하는 가장 기본적인 (Atomic) 데이터 단위이자 Git의 기본 철학이다. <br> Git은 _SHA-1 Hash_ 를 사용하여 Checksum을 만든다. (만든 Checksum은 16진수 문자열이다.) Git은 모든 것을 Hash로 식별하기 때문에 그러한 문자열 값들을 여기저기서 볼 수 있으며, 실제로 Git은 파일을 이름으로 저장하지 않고 해당 파일의 Hash로 저장한다. 

## Creating branch

```bash
git branch testing #create a new branch called testing

# existing files + contents of file also changed!!
git checkout testing #switches to testing branch
git switch testing #same as the above

git checkout -b testing #create and switch to testing 
git switch -C testing #same as the above
```

### Managing Branch

```bash
git branch #simple listing of all branches
git branch -r #sees the remote branches
git branch --all #list including remote branches
git branch -v #sees the last commit on each branch
git branch --merged #sees merged branches
git branch --no-merged #sees not merged branches
git branch -d testing #deletes the branch
git push origin --delete testing
git branch --move wrong correct #rename
git push --set-upstream origin correct #push new name
```

### Git branch comprehension

```jsx
> git hist
* [2020-10-28] [3345766] | feature a {{Ellie}}  (HEAD -> master, newBranch, feature-a)
* [2020-10-28] [d643a6e] | Update Welcome page {{Ellie}} 
| * [2020-10-28] [c38c4c4] | Fix light theme {{Ellie}}  (fix)
|/  
* [2020-10-28] [b8e485f] | Add light theme {{Ellie}}
```

 위의 output of "git hist"에 대해서 해석을 하자면, 생성 날짜와 함께 생성된 commit들이 쭉 출력이 됨 + 현재 Head는 commit [3345766]을 가리키고 있으며 ***branch*** master, newBranch, feature-a 역시 해당 commit을 가리키고 있다고 보면 된다. 즉, 새로운 ***branch*** newBranch는 무언가 새로운 것이 생성된 것이 아니라, 단순히 newBranch 라는 **새로운 포인터**가 동일한 commit을 가리키고 있는 것으로 볼 수 있다. 

_**❖ newly created ***branch*** 가 새로운 commit들을 통해 update 되어갈 때에는, 어떤 정보들을 어떻게 저장하는가...? ►►► 상당히 core한 질문인 듯!**_ _(cf. Pro Git)_

<br>

> _**예제**_ <br>
브랜치를 이동할 때에 유의할 점이 있다. 아직 커밋하지 않은 파일이 Checkout 할 브랜치와 충돌 나면 브랜치를 변경할 수 없다. 즉, 브랜치를 변경할 때는 워킹 디렉토리 (* Staging Area 또한?)를 정리하는 것이 좋다. <br> Git_Project 디렉토리 내의 git-branch_exercise 로 가서 이러한 상황을 직접 테스트 해보고 이에 관해 report 해보자. 

### Comparing

```bash
git log branch1..branch2 #all the commits between branch1 and branch2
git hist branch1..branch2 #all the commits between branch1 and branch2 with (ellie's) hist alias
git diff branch1..branch2 #all the changes between branch1 and branch2
```

## Merge

### _**Fast forward Merge**_
> 특정 브랜치 B가 가리키는 커밋이 다른 브랜치 A의 가장 최신 커밋에서 fork된 브랜치인 경우, 브랜치 포인터는 별도의 Merge 과정 없이 그냥 최신 커밋으로 이동하게 된다. <br> 다시 말해 A 브랜치에서 다른 B 브랜치를 Merge 할 때 B 브랜치가 A 브랜치 이후의 커밋을 가리키고 있으면, _(git merge branchB #on branchA 를 시행하였을 때)_ 그저 A 브랜치가 B 브랜치와 동일한 커밋을 가리키도록 이동시킨다. 그게 다임. (아래의 그림 2개 참조할 것.)

### _**3-way Merge**_

```bash
# featureA 에서 작업한 내용을 master branch로 가지고 온다! (ff merge)
git merge featureA #merges featureA branch into the current one

git merge --squash featureA #suqash merge, only one commit

# fast forward merge 하지 않고 기록을 남길 수 있다. (merge after extra commit with commit message)
git merge --no-ff featureA #creates a merge commit 

# merge conflict 해결 후에 다시 merge 해주는 cmd
git merge --continue 

# merge 명령 취소
git merge --abort
git mergetool #opens merge tool 
```

### Merge Conflict
Merge 하려고 하는 두 브랜치에서 같은 파일의 한 부분을 동시에 수정한 상황에서 Merge 를 시도하면 Git 은 해당 부분을 Merge 하지 못한다. <br> _※같은 파일의 다른 부분을 수정한 경우에는 상관없다. 괜찮음_
<br> 이 때, 변경사항의 출동을 개발자가 해결하지 않는 한 Merge 과정을 진행할 수 없다. <br>
<br> _**※ Merge Conflict이 발생하였을 때 어떤 파일을 Merge 할 수 없었는지 살펴보려면 git status 명령을 이용하면 된다.**_

> _**Merge conflict 발생시 주의사항**_
<br> 충돌이 일어난 부분을 해결하는 수정/변경만 해주어야 한다. 
<br> **부가적인 코드 추가는 하면 안된다!!** 

<br>

**Merge tool Config** 

```bash
[merge]
    tool = vscode
[mergetool]
	keepBackup = false
[mergetool "vscode"]
    cmd = code --wait $MERGED
[mergetool "p4merge"]
    path = "/Applications/p4merge.app/Contents/MacOS/p4merge"
```

## Rebasing
> **Rebase**를 이용하면 *FF-merge*가 가능하지 않은 *3-way merge*와 같은 상황에서 불필요한 commit history를 남기지 않으면서 merge _(fast forward)_ 를 시행할 수 있다. 이전 버젼의 master 브랜치를 가리키고 있는 특정 브랜치의 파생 commit이 최신 버젼의 master 브랜치를 가리키도록 _rebase_ 해주면 fast-forward merge가 가능하다. <br>
=> 보다 꺠끗한 히스토리를 만들 수 있다. 

**_Rebase_ 를 하든, _Merge_ 를 하든 그 결과물은 같지만 _Commit History_ 만 다르다는 사실을 숙지할 것!**

> ❖ _**혼자 로컬에서 작업하는 경우가 아닌 다른 개발자와 협업하고 있는 경우에는 주의가 필요하다.**_ <br> 파생 브랜치 commit들의 포인터 정보를 변경하게 되면 기존 commit을 유지하는 것이 아니라 새로운 commit들이 생성된다. 즉, 겉으로는 동일하지만 실제로는 다른 파생 브랜치 commit들이 생성되는 것이다. <br> 따라서, rebase 후에 push를 통해 서버에 이를 업데이트하게 되면 다른 개발자가 가지고 있는 파생 브랜치 commit들과는 전혀 다른 파생 브랜치 commit으로 업데이트되는 것이므로 차후에 merge conflict를 유발할 수 있다. <br> 따라서 이미 history가 서버에 업로드 되어있는 경우라면, 업로드된 history는 절대 rebase 하면 안된다. 서버에 업로드 되지 않은, 로컬에 있는 commit에 한해서 (자유롭게) rebase 해주면 된다. 

**_git rebase --onto_:** 여러가지의 브랜치가 존재할 때, 특히 브랜치 위에 또 다른 브랜치가 만들어져 있는 경우, 즉 여러가지를 체이닝해서 만드는 경우 굉장히 유용하게 사용할 수 있다. 

### **실제로는 어떤일이 일어나는가?**
* 우선 두 브랜치가 나뉘기 전인 공통 commit 으로 이동한다. _(to Common ancestor)_ 
* 해당 commit 부터 현재 Checkout 한 브랜치가 가리키는 commit 까지 diff 를 차례로 만들어 (어딘가에) 임시로 저장해둔다. 
* Rebase 할 브랜치가 합칠 브랜치가 가리키는 commit 을 가리키게 하고 (포인터 이동), 사전에 저장했던 변경사항을 차례대로 적용한다. _(Conflict 가 발생하지 않으면 FF-Merge 가 문제없이 시행될 것이다.)_

```bash
git rebase master #rebase current branch onto the master

# 이후 FF-merge 하는 방법은 아래와 같다.
git checkout master
git merge branchName

git rebase --onto newForker oldForker forkee #take commits of the ui branch forked from the service branch and move them to master
```

_**rebase를 실행할 때 conflict가 발생할 수는 없나? 그 상황에서는 어떻게?**_

## Cherry picking
> 다른 브랜치 상에 존재하는 특정 commit이 master 브랜치에 필요하다면 cherry pick을 활용할 수 있다. 
```bash
git cherry-pick hash #applies the given commit 
```

# Undo

## Local Changes
> rebase 와 마찬가지로 서버에 이미 push 된 commit 이나, 다른 개발자들과 협업하는 branch 인 경우에는 신중하게 Undo 를 수행해야 한다. (하면 안된다에 가깝다.)

**Unstaging a staged file**

```bash
git reset HEAD file.txt
```

**Unmodifying a modified file**
> 수정한 내용이 전부 사라지고, 이를 복구할 수 없기 때문에 매우 조심해서 사용해야 한다. 변경한 내용을 쉽게 버릴 수는 없고, But 당장은 되돌려야만 하는 상황이라면 _Stash_ 와 _Branch_ 를 사용하자. 

```bash
git checkout -- file.txt
```

**Discarding local changes**

```bash
git restore --staged file.txt #unstaging a staged file
git restore file.txt #unmodifying a modified file
git restore . #unmodifying all modified files in the directory
git clean -fd #removes all untracked files
```

**Restoring file from certain commit**

```bash
git restore --source=hash file.txt
git restore --source=HEAD~2 file.txt
```

## Commit

**Amending the last commit**
> 아직 서버에 업로드 _(by git push)_ 하지 않은 커밋을 수정하고자 할 때, _amend_ 옵션을 이용할 수 있다. _git commit --amend_ 커맨드를 실행하면 현재 Staging area에 있는 내용들이 해당 커밋에 새로이 (재)반영되며, 이 때 last commit 에 대한 정보가 _editor_ 상에서 open 된다. 만약 last commit 이후 수정한 것이 없다면 커밋 메시지만 수정하여 이를 해당 커밋에 반영하게 된다. **-m** 옵션을 추가하면 터미널 상에서 바로 새로운 커밋 메시지를 작성할 수 있다.
```bash
# amend the last commit with a new commit message
git commit --amend -m "new commit msg"
# amend the last commit using the default editor
git commit --amend
```

### 사용 예시
* 커밋을 했는데 Stage 하는 것을 빠트린 파일이 있으면 아래와 같이 고칠 수 있다.
```bash
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```

_**이때 SHA-1 값이 바뀌기 때문에 과거의 커밋을 변경할 때 주의해야 한다. Rebase와 같이 이미 Push 한 커밋은 수정하면 안 된다.**_

## Reset
> Git 은 서로 다른 세 트리 (Data structure tree 아님, **파일의 묶음**을 지칭함.)를 관리하는 컨텐츠 관리자로 생각할 수 있다. reset 명령을 통해 가장 최근의 git commit 명령을 되돌릴 수 있다. 
- Head: 마지막 커밋의 스냅샷 (다음 커밋의 부모 커밋)
- Index (Staging area): 다음에 커밋할 스냅샷
  - 인덱스는 마지막으로 Checkout 한 브랜치의 파일 목록과 파일 내용으로 채워진다. (파일들을 Tracking 한다는 말인듯.)
- Working directory: 샌드박스

* 브랜치를 Checkout 하면, HEAD가 새로운 브랜치를 가리키도록 바뀌고, 새로운 커밋의 스냅샷을 Index에 놓는다 (Tracking). 그리고 Index의 내용을 Working directory로 복사한다. 

_**reset 명령은 정해진 순서 (HEAD - Index - Working directory) 대로 세 개의 트리를 덮어써 나가다가 옵션에 따라 지정한 곳에서 멈춘다.**_ 

1. HEAD가 가리키는 브랜치를 옮긴다. _(--soft 옵션이 붙으면 여기까지)_
2. Index를 HEAD가 가리키는 상태로 만든다. _(if no --hard option)_
3. Working directory를 Index의 상태로 만든다. <br> (마지막 커밋에서 수정했던 사항/데이터들을 삭제한다. _**커밋한 적이 없는 내용의 경우, 복원할 수 없음.**_)

```bash
git reset --soft HEAD~ #removes the commit and keep changes at staging area
git reset (--mixed) HEAD~ #removes the commit and keep changes at working directory
git reset --hard HEAD~ #removes the commit and don't keep the code
```

## Undo action - reflog
> _**git reflog**_ 를 이용하면, (실수로) hard reset 을 실행하여 깃 히스토리 상에서 날려버린 이전의 (commited) 시점으로 돌아갈 수 있다. (**reflog 는 reference log 의 줄임말임.**)
<br> Git 은 **HEAD** 가 가리키고 있었던 내용들을 다 기억/기록하고 계시기 때문에, 실수를 하더라도 돌이킬 수 있다. 언제든지 우리가 원하는 시점으로 돌아갈 수 있는 것이다! 
<br> 단, 모든 시점으로 돌아갈 수 있는 것은 아니고, commit 된 상태/시점으로만 reset 할 수 있다. (만약 아직 commit 을 하지 않은 상태에서 local 에 작성한 파일을 _git reset --hard_ 를 통해서 초기화해버렸다면, 다시 돌아갈 수 있을 확률이 매우 작다.) 


```bash
git reflog
git reset --hard hash
```

## Revert
> reset, restore 같은 명령어들은 언제든지 이전 버젼으로 돌아갈 수 있긴 하지만, History 상에 이전으로 돌아갔다는 기록이 남지는 않는다. 뒤늦게 기능에 문제가 생겼다던지, 원치 않는 심각한 이슈가 발생해서 해당 commit 을 완전히 제거해야 하는 경우가 발생할 수 있다. 이럴 경우 문제가 되는 commit 을 _**revert**_ 를 통해 취소할 수 있다. 특정 commit 을 _**revert**_ 해주게 되면 해당 commit 의 변경 사항을 다시 없애 주는 commit 이 만들어진다. (그 기록이 History 에도 남게 됨.) 
<br> ► 이미 server master branch 에 commit 된 내용들의 경우, _revert_ 를 통해 취소하는 것이 좋다 (rather than _reset, rebase_).
<br> **_revert_ 는 새로운 commit 을 통해 이미 추가된 내용을 변경하는 것이므로 (i.e. History 를 수정하지 않음, 따지자면 History 에 추가하는 것이라고 볼 수 있음.) 언제든지 자유롭게 이용할 수 있음!

- 당연히! revert 하는 그 내용만 작성해서 commit 을 해야 한다. <br> (※ revert 할 때 다른 내용 추가해서 commit 하는 게 애초에 가능한가...? => revert 이후를 얘기하는 듯)
- revert 를 시행 시, 기존의 다른 commit 과 conflict 이 발생하기 때문에 manually 수정해줘야 되는 경우 **--no-commit** 옵션을 이용해서 관련 부분을 수정한 다음 commit 을 진행할 수 있다. 

```bash
git revert hash #reverts the given commit
git revert HEAD~1 
gut revert --no-commit hash #reverts the given commit without revert commit (instead, add to staging area)
```

## Interactive Rebasing: History 변경!
__* Rebasing에서는 각 트리들이 어떻게 덮어써지는가?__
* git commit --amend 를 통해 직전 커밋을 수정할 수 있었다면 Interactive rebasing 을 이용하면 그 이전의 커밋들도 자유롭게 수정할 수 있다. + History 도 변경됨.
* rebasing 을 하는 순간, 이후 commit 들의 pointer 들이 모두 업데이트 되어야 하기 때문에 rebased commit (interactively) 이후의 commit 들도 모두 새로운 아이템이 된다. (전체적인 History 가 업데이트 됨.)
* 수정하고자 하는 커밋들 중에 가장 이전의 커밋 직전 커밋으로 rebase 해야한다. 

> **Interactive Rebasing Commands:** <br>
p, pick <commit> = use commit <br>
r, reword <commit> = use commit, but edit the commit message <br>
e, edit <commit> = use commit, but stop for amending <br>
s, squash <commit> = use commit, but meld into previous commit <br>
f, fixup <commit> = like "squash", but discard this commit's log message <br>
x, exec <command> = run command (the rest of the line) using shell <br>
b, break = stop here (continue rebase later with 'git rebase continue') <br>
d, drop <commit> = remove commit <br>
l, label <label> = label current HEAD with a name <br>
t, reset <label> = reset HEAD to a label <br>
m, merge [-C <commit> | -c <commit>] <label> [# <oneline>] <br>
- Create a merge commit using the original merge commit's
message (or the oneline, if no original merge commit was specified). 
- Use -c <commit> to reword the commit message.

```bash
git rebase -i HEAD~2
git rebase --continue
git rebase --abort
```

# Stashing
> 개발을 하다보면, working directory 에서 열심히 작업을 하던 도중에 갑작스럽게 브랜치를 전환해야 하는 상황에 직면할 수 있다. 아직 완성도가 부족하여 commit 을 하기에는 다소 찝찝하지만, 작업한 변경 사항들을 잃어버리고 싶지 않을 때 _**stash**_ 를 활용하면 된다! 

* Git 에는 이러한 경우에 유용하게 쓸 수 있는 _Stash Stack_ 이 존재한다.
* Git history 에 저장하지 않고도, 작업하던 내용들을 잠시 저장해둘 수 있다. 
* 다른 브랜치를 checkout 해서 확인/작업한 다음에, 다시 원하는 순간에 Stash Stack 에 있는 내용들을 working directory 로 가지고 올 수 있다. 
* 새로운 문제를 해결하고자 할 때, 여러 가지 방법들을 try 해보는 상황에서 각각의 시도들을 잠시 저장해두고 싶을 때에도 유용하게 활용 가능하다! 
- _**git stash**_ command 를 실행하면, 작업하고 있던 모든 working directory 내의 아이들과 index (staging area)가 Stash Stack 에 저장된다. 

- **_git add_ 를 통해서 staging area 에 올려놓고 나중에 다시 와서 해당 내용만 불러올 순 없다. 
  <br> * staging area 에 있는 내용/history 로는 switching/checkout 이 안된다!**


### Saving

```bash
git stash push -m "message" #make a new stash
git stash #same as above
git stash --keep-index #stash but keep them in the staging area
git stash -u #--include-untracked 
```

### Listing

```bash
git stash list #see all the stashes
git stash show hash #see the given stash
git stash show hash -p #see the given stash with details 
```

### Applying

```bash
git stash apply hash #applies the given stash
git stash apply #applies the latest stash
git stash apply --index #apply the stated changes
git stash pop #apply and drop 
git stash branch branchName #apply stash in a new branch
```

### Deleting

```bash
git stash drop hash #deletes the given stash
git stash clear #deletes all the stashes
```

# Remote

```bash
git clone URL #cloning
git remote -v #shows all the remote URLs
#add a new remote with name, 이거는 그냥 repo가 그대로 복제되는건가?
git remote add name URL 
```

### **_What is the reason of using multi-remote 저장소?_**
<br>

**Inspecting** 

```bash
git remote
git remote show 
git remote show origin
```

**Syncing with remotes**

```bash
#현재 작업 중인 HEAD는 그대로 유지하면서, 서버에 업데이트된 히스토리 정보만 가지고 올 때 git fetch 를 이용할 수 있다. fetch의 경우, 내가 서버에 있는 히스토리 정보를 업데이트해서 서버에서 어떤 일들이 지금 발생하고 있는지 누가 어떤 일을 했는지 확인하고 싶은 경우에 많이 사용한다. (로컬에서 하던 작업들을 정리하고 나서 수동으로 Merge 해야함.) 
git fetch #pulls down all the data from remote 
git fetch origin #pulls down the remote-origin
git fetch origin master #pulls down only master branch

# 서버에 있는 내용을 받아와서 로컬 버전도 서버와 동일하게 만들고 싶다면 git pull 을 이용하면 된다. Local과 서버의 Synchronization!
git pull #fetch and merge
git pull --rebase #use rebase when pulling instead of merge
git push
git push origin master 
git push -f #무조건 로컬에 있는 변경 내용이 맞다! 로컬과 싱크가 맞지 않는 서버의 변경 내용들을 다 무시/지워라!
```

**Renaming or Removing** 

```bash
git remote rename sec second
git remote remove second
```

# Tools

## Basic Debugging

```bash
git blame file.txt
```
_Sourcetree_ 로도 가능함. 

## Bisect
> _git bisect_ 를 이용하면 프로젝트에 어느 시점에 발생하였는지 파악하지 못한 버그가 존재할 때, (버그가 발생하지 않는 commit 과 버그가 발생하는 commit 을 표시하면 +_Binary search_ 를 통해) 그 근원이 되는 commit 을 찾을 수 있다.

```bash
git bisect start
git bisect good hash
git bisect good #해당 commit 은 괜찮은 것으로 marking
git bisect bad #해당 commit 은 문제가 발생하는 것으로 marking
git bisect reset
```

## Config

```bash
[alias]
	s = status
	l = log --graph --all --pretty=format:'%C(yellow)%h%C(cyan)%d%Creset %s %Cgreen(%cr) %C(magenta)<%an>%Creset'
	up = !git fetch origin master && git rebase origin/master
	co = checkout
	ca = !git add -A && git commit -m 
	cad = !git add -A && git commit -m "."
	c = commit
	b = branch
  list = stash list
  save = stash save 
	pop = stash pop
	apply = stash apply
	rc = rebase —continue
	get = "!f(){ git fetch && git checkout $1 && git reset --hard origin/$1; };f"
	new = "!f(){ git co -b ellie-$1 origin/master && git pull; };f"
	st = status
	hist = log --graph --all --pretty=format:'%C(yellow)[%ad]%C(reset) %C(green)[%h]%C(reset) | %C(white)%s %C(bold red){{%an}}%C(reset) %C(blue)%d%C(reset)' --date=short
```