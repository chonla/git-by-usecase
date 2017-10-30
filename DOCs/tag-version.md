## tag version

**ใช้เมื่อ** ต้องการต้องการแปะ version ไว้ที่ commit ใด ๆ

```
$ git tag v1.0
$
```

ถ้าต้องการดูว่ามี tag อะไรอยู่บ้าง

```
$ git tags
v1.0
$
```

ต้องการ checkout ไปที่ tag version ใด ๆ

```
$ git fetch --all --tags --prune
$ git checkout v1.0
Note: checking out 'v1.0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at b6d815e... update example
$
```

ข้อสังเกต

* option --all เพื่อบอกว่าให้ทำกับทุก ๆ remote
* option --tags เพื่อบอกว่าให้ทำกับทุก tag ด้วย
* git checkout v1.0 ถึงแม้ว่า git จะบอกว่า HEAD ได้ detach ออกจาก branch ไปชี้ที่ tag v1.0 แล้ว แต่เพราะว่า tag v1.0 ชี้อยู่ที่เดียวกับปลายสุดของ branch ดังนั้นการแก้ไขใด ๆ ก็จะยังส่งผลถึง branch ที่ tag นั้นชี้อยู่ด้วย

<< [กลับไปหน้าแรก](README.md)