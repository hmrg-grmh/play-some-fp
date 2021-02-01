# play some fp on bash



## tail rec play

这部分测试(00,01)都是在没给 root/sudo 权限的普通用户下测的。

### 00

#### code demo

假设下面内容在文件 `tailbashrec.test` 中：

```bash
#! /bin/bash

numdir=${1:-0} ;
cd "$numdir" &&
{
    pwd ;
} ||
{
    exec bash tailbashrec.test $((numdir+1)) ;
} ;
```

#### desc

其实它也可表现 `source` 和 `exec bash` 的两方面区别：
- 退出效果：倒数第二行那里的 `exec bash` 处换成 `source` 的话， `bash <脚本文件名>` 没问题， `source <脚本文件名>` 就会在最终退出。
- 是否有栈溢出： 在用 `bash <脚本文件名>` 执行的时候，如果倒数第二行那里的 `exec bash` 那里换成 `source` 就会有类似于栈溢出的情况，否则不会。

类似栈溢出的情况：

```log
tailrec-test.sh: 第 2 行:cd: 4701: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4702: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4703: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4704: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4705: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4706: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4707: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4708: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4709: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4710: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4711: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4712: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4713: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4714: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4715: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4716: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4717: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4718: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 4719: 没有那个文件或目录
^C^Z^Z^Z段错误(吐核)
```

上面出现“^C^Z^Z^Z”的地方就是它卡住了的地方，卡住了所以我试图退出或暂停它，但也没用。

没有问题一直“尾调用”下去了的情况是这样：

```log
...
tailrec-test.sh: 第 2 行:cd: 498189: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498190: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498191: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498192: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498193: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498194: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498195: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498196: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498197: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498198: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498199: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498200: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498201: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498202: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498203: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498204: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498205: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498206: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498207: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498208: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498209: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498210: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498211: 没有那个文件或目录
tailrec-test.sh: 第 2 行:cd: 498212: 没有那个文件或目录
...
```

总之一直执行了很久。


### 01

其实，上面的代码这样一改，就没有问题了：我是说，不怕不小心把它加到会被自动 `source` 的文件里了。
(比如不小心放到 `/etc/profile.d` 下面就会很有趣的。当然建议不要尝试)

#### code demo

```bash
#! /bin/bash

numdir=${1:-0} ;
cd "$numdir" &&
{
    pwd ;
} ||
{
    exec bash "$0" "$((numdir+1))" ;
} ;
```

#### desc

很简单，就是把那个不知道叫啥的文件名换成了 `$0` 而已！~

顺便，也方便改文件名了。

## tail rec play ## EOF!
