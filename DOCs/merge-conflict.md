## merge conflict

**ใช้เมื่อ** เกิด conflict ในขั้นตอนการ merge ระหว่าง 2 branch เช่น

* [ตอน merge](#ลอง-reproduce-conflict-ตอน-merge)
* [ตอน rebase](#ลอง-reproduce-conflict-ตอน-rebase)
* [ตอน stash pop หรือ stash apply](#ลอง-reproduce-conflict-ตอน-stash-pop)

### ลอง reproduce conflict ตอน merge

```
$ mkdir /git-conflict
$ cd /git-conflict
$ git init
Initialized empty Git repository in /git-conflict/.git/
$ echo Line 1 > example.txt
$ git add .
$ git commit -m "initial"
[master (root-commit) 7eb643c] initial
 1 file changed, 1 insertion(+)
 create mode 100644 example.txt
$ git checkout -b develop
Switched to a new branch 'develop'
$ echo Line 2 >> example.txt
$ git add .
$ git commit -m "Add Line 2"
[develop 7c76424] Add Line 2
 1 file changed, 1 insertion(+)
$ echo Line 3 >> example.txt
$ git add .
$ git commit -m "Add Line 3"
[develop 561d359] Add Line 3
 1 file changed, 1 insertion(+)
$ git checkout master
Switched to branch 'master'
$ echo line 2 >> example.txt
$ git add .
$ git commit -m "Add small line 2"
[master 488ad53] Add small line 2
 1 file changed, 1 insertion(+)
$ echo LINE 3 >> example.txt
$ git add .
$ git commit -m "Add LINE 3"
[master f091739] Add LINE 3
 1 file changed, 1 insertion(+)
$ git merge develop
Auto-merging example.txt
CONFLICT (content): Merge conflict in example.txt
Automatic merge failed; fix conflicts and then commit the result.
$
```

### เปิดไฟล์ที่เจอ conflict เพื่อแก้ไข merge conflict

```
Line 1
<<<<<<< HEAD
line 2
Line 3
=======
Line 2
LINE 3
>>>>>>> develop
```

ตั้งแต่ <<<<<<< ไปจนถึง ======== จะเป็นสิ่งที่อยู่ที่ HEAD กำลังชี้ ส่วนตั้งแต่ ======== ไปจนถึง >>>>>>>> จะเป็นสิ่งที่เรากำลังเอามา merge กับที่ ๆ HEAD กำลังชี้อยู่

สิ่งที่เราจะต้องทำคือ ตัดสินใจให้ git ว่าเราจะ merge 2 commit นี้ด้วยวิธีไหน เช่น ถ้าเราคิดว่า ```Line 2``` และ ```LINE 3``` เป็นสิ่งที่เราต้องการ และ Up-to-date มากกว่า ก็ให้เก็บบรรทัด Line 2 และ LINE 3 ไว้ และลบตั้งแต่ <<<<<<< HEAD ออกไปจนถึง ========= และ ลบ >>>>>>> develop ออก และ **git add . ตามด้วย git commit ให้เรียบร้อย**

### ลอง reproduce conflict ตอน rebase

```
$ mkdir /git-conflict
$ cd /git-conflict
$ git init
Initialized empty Git repository in /git-conflict/.git/
$ echo Line 1 > example.txt
$ git add .
$ git commit -m "initial"
[master (root-commit) d5760b7] initial
 1 file changed, 1 insertion(+)
 create mode 100644 example.txt
$ git checkout -b develop
Switched to a new branch 'develop'
$ echo Line 2 >> example.txt
$ git add .
$ git commit -m "Add Line 2"
[develop 7e7dde1] Add Line 2
 1 file changed, 1 insertion(+)
$ echo Line 3 >> example.txt
$ git add .
$ git commit -m "Add Line 3"
[develop a540525] Add Line 3
 1 file changed, 1 insertion(+)
$ git checkout master
Switched to branch 'master'
$ echo line 2 >> example.txt
$ git add .
$ git commit -m "Add small line 2"
[master 3b7044f] Add small line 2
 1 file changed, 1 insertion(+)
$ echo LINE 3 >> example.txt
$ git add .
$ git commit -m "Add LINE 3"
[master e9e781a] Add LINE 3
 1 file changed, 1 insertion(+)
$ git rebase develop
First, rewinding head to replay your work on top of it...
Applying: Add small line 2
Using index info to reconstruct a base tree...
M	example.txt
Falling back to patching base and 3-way merge...
Auto-merging example.txt
CONFLICT (content): Merge conflict in example.txt
error: Failed to merge in the changes.
Patch failed at 0001 Add small line 2
The copy of the patch that failed is found in: .git/rebase-apply/patch

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
$
```

### เปิดไฟล์ที่เจอ conflict เพื่อแก้ไข merge rebase conflict

```
Line 1
<<<<<<< HEAD
Line 2
Line 3
=======
line 2
>>>>>>> Add small line 2
```

สิ่งที่ต้องทำก็จะคล้าย ๆ กับตอนแก้ merge conflict **แต่!!!!** สังเกตว่า ด้านบน HEAD จะมี Line 3 แต่ด้านล่างมันไม่มี LINE 3!! เพราะว่ามันกำลัง replay change ทีละอันจาก branch develop

สิ่งที่เราต้องทำคือ resolve เฉพาะ conflict นี้ อย่าเพิ่งไปยุ่งอะไรกับ commit ที่เพิ่ม LINE 3 ดังนั้น ถ้าเราต้องการเก็บ Line 2 และ Line 3 ไว้ ให้ลบ <<<<<< HEAD และ ======== ไปจนถึง >>>>>>> Add small line 2 ออก

แต่ถ้าเราต้องการ line 2 และ LINE 3 (ซึ่ง LINE 3 ยังมาไม่ถึง) ให้ลบ <<<<<<< HEAD จนถึง ======== และ >>>>>>>> Add small line 2 ทิ้ง **โดยไม่จำเป็นต้องเพิ่ม LINE 3 เข้าไป**

ไม่ว่าจะเลือกอันไหน พอเสร็จแล้ว ให้ git add **ห้าม commit** เสร็จแล้วให้สั่ง ```git rebase --continue``` เพื่อทำการ rebase ต่อให้จบ

ถ้าไม่ต้องการ rebase ต่อแล้ว และต้องการยกเลิก ให้สั่ง ```git rebase --abort```

### ลอง reproduce conflict ตอน stash pop

```
$ mkdir /git-conflict
$ cd /git-conflict
$ git init
Initialized empty Git repository in /git-conflict/.git/
$ echo Line 1 > example.txt
$ git add .
$ git commit -m "initial"
[master (root-commit) a113b6f] initial
 1 file changed, 1 insertion(+)
 create mode 100644 example.txt
$ echo Line 2 >> example.txt
$ git add .
$ git stash
Saved working directory and index state WIP on master: a113b6f initial
$ echo Line 3 >> example.txt
$ git add .
$ git commit -m "Add Line 3"
[master 26d44c4] Add Line 3
 1 file changed, 1 insertion(+)
$ git stash pop
Auto-merging example.txt
CONFLICT (content): Merge conflict in example.txt
$
```

### เปิดไฟล์ที่เจอ conflict เพื่อแก้ไข merge stash conflict

```
Line 1
<<<<<<< Updated upstream
Line 3
=======
Line 2
>>>>>>> Stashed changes
```

วิธีแก้ไข conflict ให้ทำเหมือนกับตอน merge คือ ลบส่วนที่ไม่ต้องการออก เสร็จแล้วให้ add และ commit ให้เรียบร้อย หรือถ้ายังไม่ต้องการ commit ก็สามารถทำต่อจากที่ค้างไว้ได้เลย

<< [กลับไปหน้าแรก](README.md)