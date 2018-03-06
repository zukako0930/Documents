## rails consoleについて
terminalからDBなどをいじる事ができる。
動作確認に有効。
```
$ User.update_attributes(name: "nanoha") #属性が一つの場合
$ User.update(name: "nanoha",email: "nanoha@nanoha.com") #複数の場合
```
または,
```
$ user = User.find(id: 1)
$ user.name = "nanoha"
$ user.save
```
