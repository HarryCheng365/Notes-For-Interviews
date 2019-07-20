# 硬连接与软连接



# 文件和目录命令 ✅

### ls 展示目录下文件：

1. 1. -a 展示所有文件

   2. -l (以列表形式列出文件)

   3. -h (配合 l 命令，文件大小更直观)

   4. 额外补充：与通配符的使用：

   5. - \* **任意个数字符**

   6. - ？任意一个字符

   7. - [] 匹配字符中选一个

        Eg. ls *.log

### cd 打开文件夹：

1. cd - 在最近工作的两个目录之间切换
2. cd 切换到当前用户的主目录(/home/用户目录)

### touch 创建文件：

1. 1. 若文件已经存在，则修改文件的最后一次修改日期
   2. 若文件不存在，创建文件

###  mkdir 创建文件夹：

1. 1. -p 递归创建目录，如：mkdir -p a/b/c/d

###  rm 删除文件、文件夹：

1. 1. -f  强制删除，忽略不存在的文件，无需提示
   2. -r 递归地删除目录下的内容，删除文件夹时必须加此参数
   3. 额外补充：rm命令删除文件不能恢复

###  tree 树状展示文件、文件夹:

1. -d：只显示目录

### find 命令

语法：`find path [options] params`

作用：在指定目录下查找文件

实例：

- 精确查找

  ```
  #不指定路径，则直接在当前目录下查找指定test.java文件
  >find -name "test.java"
  
  #在根目录下查找test.java文件
  >find / -name "test.java"
  ```

- 模糊查找

  find命令同样支持通配符，如下所示：

  ```
  #在用户home目录下寻找以test开头的所有文件
  >find ~ -name "test*"
  ```

- 忽略大小写的查找

  find也支持忽略大小写的查找(使用参数 -iname 替代 -name)

  ```
  >find ~ -iname "test*"
  ```


find命令支持通配符号



# 拷贝和移动命令

###  cp 拷贝:  

​	cp 原文件  目标文件

1. 1. -i：覆盖文件提示
   2. -r：若给出的源文件事目录文件，则cp将递归 复制该目录下的所有子目录和文件，目标文件必须为一个目录名
   3. 例：Desktop mrjiao$:       cp   ~/Documents/test.txt   ./test.txt
   4. 可以简写为cp   ~/Documents/test.txt   .

###  mv 剪切：

​	移动文件或者目录（剪切），也可以给文件重命名，无需加入参数

​	1.  -i：覆盖文件前提示

### pwd 查看当前全路径:



# 管道操作符 |



注意点：

- 只能处理前一个命令正确输出，不处理错误输出；

- 右边命令必须能够接收标准输入流，否则传递过程中数据会被抛弃。常见能接收标准输入流的命令如下：

  `sed, awk, grep, cut, head, top, less, more, wc, join, sort, split`等



# 文件内容命令

### cat/more：查看文件内容



### grep：查找文件内容：

语法：`grep [options] pattern file`

全称：grep, global regular expression print（支持正则表达式）

作用：查找文件里符合条件的字符串，会打印出匹配的行

实例：

- 精确查找文件中包含指定内容的行

  ```
  #从test开头的文件中查找内容包含"haha"的行
  >grep "haha" test*
  ```

- 结合管道在文件中查询指定的内容，并指定输出内容

  ​	如现已有一个较大的日志文件，查找其中包含`error`字段信息的行，由于行中信息很多，现在不仅要查找到包含指定信息的行，还要从中进行过滤，只显示行中包含的`errorCode[xxxx]`字段信息，其中xxxx为字母数字混合序列：

  ```
  #中括号要进行转义
  >grep "error" test.log | grep -o "errorCode\[[0-9a-z]\]"
  errorCode[asdcasd123sfas1]
  errorCode[asdcasd123sfas2]
  errorCode[asdcasd123sfas3]
  errorCode[asdcasd123sfas4]
  errorCode[asdcasd123sfas1]
  ...
  ```

- grep过滤指定信息

  ​	通过指定参数`-v`可以过滤掉指定信息。如上面例子中，不想看到有`error`字段信息的行

  ```
  >grep -v "error" test.log
  ```

  

### awk：统计文本内容

语法：`awk [options] 'cmd' file`

作用：会对输入部分按照分割符进行切片，切成多个部分，将切片直接保存在内建的变量$1, $2…中($0表示行全部)，支持对单个切片的判断，支持循环判断，**默认分隔符为空格**。

实例：

- 去除`ps -ef`命令输出的线程信息中切片字段

  ```
  # ps -ef 输出如下
  UID   PID  PPID   C STIME   TTY           TIME CMD
      0     1     0   0 三04下午 ??         5:21.63 /sbin/launchd
      0    44     1   0 三04下午 ??         0:19.47 /usr/sbin/syslogd
      0    45     1   0 三04下午 ??         0:14.40 /usr/libexec/UserEventAgent (System)
      0    48     1   0 三04下午 ??         0:04.83 /System/Library/Priva...
      0    49     1   0 三04下午 ??         0:21.71 /usr/libexec/kextd
      ...
  ```

  操作如下：

  ```
  >ps -ef | awk '{print $1,$2,$3,$4,$5,$6,$7}'
  UID PID PPID C STIME TTY TIME
  0 1 0 0 三04下午 ?? 5:22.31
  0 44 1 0 三04下午 ?? 0:19.49
  0 45 1 0 三04下午 ?? 0:14.42
  0 48 1 0 三04下午 ?? 0:04.84
  0 49 1 0 三04下午 ?? 0:21.71
  ...
  ```

- 去除`ps -ef`命令输出的线程信息中切片字段，并对内容再进行筛选，只输出uid为501、ppid为1的信息：

  ```
  >ps -ef | awk '$1==501 && $3==1  {print $1,$2,$3,$4,$5,$6,$7}'
  501 95 1 0 三04下午 ?? 0:40.28
  501 269 1 0 三04下午 ?? 0:45.77
  501 270 1 0 三04下午 ?? 0:12.31
  501 271 1 0 三04下午 ?? 1:03.09
  501 274 1 0 三04下午 ?? 0:55.42
  501 280 1 0 三04下午 ?? 0:44.76
  ...
  ```

- 使用逗号作为分隔符(指定 -F 参数)

  ```
  >cat test.txt
  dadadada,123123123
  vscsvsv,123123
  asdeq,34444
  
  >awk -F "," '{print $2}' test.txt
  123123123
  123123
  34444
  ```

- 做统计，如统计grep命令中，各种errorCode的出现次数

  ```
  >grep "error" test.log | grep -o "errorCode\[[0-9a-z]\]" | awk '{errorArr[$1]++}END{for (i in errorArr) print i '\t' errorArr[i]}'
  errorCode[asdcasd123sfas1] 3
  errorCode[asdcasd123sfas2] 5
  errorCode[asdcasd123sfas3] 10
  errorCode[asdcasd123sfas4] 1
  ```

  解析：`{errorArr[$1]++}`首先定义了一个errorArr数组，数组中索引为$1字段，字段每出现一次就累加一次，类似于哈希表。`END`定义了在累加结束后执行的操作，此处定义为遍历errorArr数组，打印出数组的下标和对应的值。

### sed 批量替换文本中内容

语法：`sed [option] 'sed command' filename`

全名：stream editor，流编辑器

实例：

- 如下test.java文件中，将所有以Str开头的行中的Str都替换成String，需要批量替换

  ```
  >cat test.java
  Str a = "acb test1";
  Str b = "acb test2".
  Str c = "acb acb test3".
  
  >sed -i 's/^Str/String/' test.java
  
  >cat test.java
  String a = "acb test1";
  String b = "acb test2".
  String c = "acb acb test3".
  ```

  解析：`'s/^Str/String/'`中第一个s表示是字符串操作，`^Str`表示以Str开头，`String`表示最终要替换的内容。`-i`参数表示直接在原文本上进行操作。

- 在上述test.java文件中，将所以以点号结尾的点号都替换成封号

  ```
  >sed -i 's/\.$/\;/' test.java
  String a = "acb test1";
  String b = "acb test2";
  String c = "acb acb test3";
  ```

  注：此处需要对点号和封号进行转义。`\.$`表示以$符号结尾。

- 在上述test.java文件中，将所有的acb都替换成me

  ```
  >sed -i 's/acb/me/' test.java
  String a = "me test1";
  String b = "me test2";
  String c = "me acb test3";
  ```

  可以注意到，并不是所有的acb都被替换，每行只有一个acb被替换，如果想要全局替换，需要制定全局替换参数g，如下所示：

  ```
  >sed -i 's/acb/me/g' test.java
  String a = "me test1";
  String b = "me test2";
  String c = "me acb test3";
  ```

- sed 删除指定行

  ```
  #删除空行
  >sed -i '/^ *$/d' test.java
  #删除包含Integer的行
  >sed -i '/Integer/d' test.java
  ```

  注：d参数表示删除符合条件的行

### vi：编辑文本

保存与退出：

1. :w   保存文件但不退出vi
2. :w file 将修改另外保存到file中，不退出vi
3. :w!   强制保存，不推出vi
4. :wq  保存文件并退出vi
5. :wq! 强制保存文件，并退出vi
6. q:  不保存文件，退出vi
7. :q! 不保存文件，强制退出vi
8. :e! 放弃所有修改，从上次保存文件开始再编辑

# 远程管理命令

### Ifconfig  查看本地网络信息



### Ping  测试网络连接



# 用户管理

注：创建用户/删除用户/修改用户密码 的终端命令都需要通过sudo来执行

### useradd, password 新建用户



# 僵尸进程与孤儿进程

# linux命令面试题

### 如何找到指定名称的文件？

​	见 文件和目录命令/find 命令。

### 如何检索文件内容？

​	见grep命令

### 如何对日志内容做统计？

​	见awk命令

### 如何批量替换日志内容？

​	见sed命令