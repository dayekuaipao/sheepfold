# shell入门
> "shell脚本编程入门"
- toc:true
- badges:true
- comments:true
- author:dayekuaipao
- categories:[linux]
---

平时我们可以使用图形化界面来与系统交互。但是有时候,我们没办法使用图形化界面(如使用ssh远程连接或者由于条件限制系统没有安装GUI的时候)，这时就需要我们使用命令行工具来进行控制。这个工具就是shell。
## Shell简介
### 什么是shell
我们先来看看Linux系统的组成。Linux系统由三部分组成:内核(The kernel),壳(The shell)和程序(The programs)。内核是操作系统的枢纽,它负责为程序分配时间和内存,处理文件储存和系统调用。壳则是一个命令行解释器,也是用户与内核交互的界面。几乎所有的操作系统都支持某种形式的shell，有些甚至还有好几种shell。你可以自己配置使用哪种shell。但是每个shell的语法都差别不大。在linux系统上，最常用的shell是Bourne Again SHell（bash），当然你也可以采用zsh（MacOS的默认shell）。个人使用的shell为zsh+oh my zsh，美观且易用。每个用户的shell配置文件通常是用户目录下的一个隐藏文件，以rc结尾。如bash的配置文件为` ~/.bashrc`，zsh的配置文件为`~/.zshrc`。每次修改之后需要使用source命令使修改生效。

### 使用shell
每当一个用户登陆时,系统都会为这个用户启动一个shell。打开终端，你就可以看到一个shell的文本接口。

```shell
 ➜  ~ 
```

其中，~表示用户目录。

我们来看看以下的例子：

第一个例子：

```shell
 ➜  ~ date
2021年 08月 26日 星期四 11:19:29 CST
```

我们可以看到输出了当前的日期和时间。

这是一个比较简单的例子。我们执行了date程序，然后获得了date的输出。

第二个例子：

```shell
(base) ➜  missing-semester echo $PATH
/opt/ros/noetic/bin:/home/shaun/anaconda3/bin:/home/shaun/anaconda3/condabin:/home/shaun/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

我们可以看到输出了一系列以分号隔开的路径。

这个例子要稍微复杂一些。主要涉及到以下两点：

第一个是命令的参数。我们执行了echo，这一程序的作用是将参数打印出来，同时为echo传递了参数$PATH。shell 基于空格分割命令并进行解析，然后执行第一个单词代表的程序，并将后续的单词作为程序可以访问的参数。如果您希望传递的参数中包含空格（例如一个名为 My Photos 的文件夹），您要么用使用单引号，双引号将其包裹起来，要么使用转义符号 `\` 进行处理（`My\ Photos`）。

第二个是变量的表示。在shell中也是有变量的。此处我们打印的是一个系统变量，为了与常量相区别，变量以$开头。我们打印的就是PATH变量。shell的变量是大小写区分的，一般环境变量用大写表示，而我们自己定义的局部变量最好用小写。

那么，shell是在哪里找到需要执行的程序的呢？实际上，shell类似于Python和Ruby，也是一个编程环境。如果你要求 shell 执行某个指令，但是该指令并不是 shell 所了解的编程关键字，那么它会在当前路径或环境变量`$PATH`定义的路径中搜索是否有该程序，当找到时就执行。我们可以使用which命令确定某个程序名代表的是哪个具体的程序。

## 在shell中导航

hell 中的路径是一组被分割的目录，在 Linux 和 macOS 上使用 `/`分割。路径 `/`代表的是系统的根目录，所有的文件夹都包括在这个路径之下。如果某个路径以 `/`开头，那么它是一个*绝对路径*，其他的都是*相对路径* 。相对路径是指相对于当前工作目录的路径，当前工作目录可以使用 `pwd` （print working directory）命令来获取。此外，切换目录需要使用 `cd` （change directory）命令。在路径中，`.` 表示的是当前目录，而 `..` 表示上级目录。

```shell
(base) ➜  missing-semester pwd
/home/shaun/workspace/missing-semester
(base) ➜  missing-semester cd ..
(base) ➜  missing-semester pwd
/home/shaun/workspace
```

我们可以使用 `ls`命令（list）查看指定目录下有哪些文件。除非我们利用第一个参数指定目录，否则 `ls` 会打印当前目录下的文件。大多数的命令接受标记和选项（带有值的标记），它们以 `-` 开头，并可以改变程序的行为。通常，在执行程序时使用 `-h` 或 `--help` 标记可以打印帮助信息，以便了解有哪些可用的标记或选项。可以看到`ls`的选项包括`-l`和`-a`等。

```shell
(base) ➜  missing-semester ls
add_foobar  test
(base) ➜  missing-semester ls --help
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.
```

使用`ls -l`，我们可以看到目录下文件或目录的详细信息。首先是开头的10个字符。第一个字符d表示当前对象是个目录，此外还有-（文件），s（socket，套接字文件），c（char，字符设备文件），b（block，块设备文件），l（link，链接文件），p（pipe，管道文件）等。接下来的9个字符每三个为一组，分别表示u（user，文件所有者）、g（group，所属用户组）以及o（other，其他用户）所具有的权限。rwx分别表示读取（read）、修改（write）、执行（execute）三种权限。后面的数字表示硬连接数量。再后面是所有者、所属用户组，文件大小、修改时间以及文件名。使用ls -a则可以查看到包含隐藏文件的所有文件。Linux中的隐藏文件的文件名以 . 开头。

```shell
(base) ➜  missing-semester ls -al
total 12
drwxrwxr-x  3 shaun shaun 4096 1月   5 10:58 .
drwxrwxr-x 14 shaun shaun 4096 1月   5 10:40 ..
-rw-rw-r--  1 shaun shaun    0 1月   5 10:52 add_foobar
drwxrwxr-x  2 shaun shaun 4096 1月   5 10:58 test
```

此外，还有`mv`（move，用于移动和重命名），`cp`（copy，用于复制），`rm`（remove，用于删除），`mkdir`（make directory，用于新建文件夹）,`touch`（用于新建文件和修改时间戳）等命令。

如果想要知道关于程序参数、输入输出的信息，或是想要了解它们的工作方式，可以使用 `man` （manual，用于查看手册）这个程序。它会接受一个程序名作为参数，然后将它的文档（用户手册）显示出来。

## 连接程序

在shell中，程序有两个主要的“流”：输入流和输出流。 当程序尝试读取信息时，它们会从输入流中进行读取，当程序返回信息时，它们会将信息输出到输出流中。 通常，一个程序的输入输出流都是终端。也就是，键盘作为输入，显示器作为输出。 我们将终端输入的流称为标准输入流（`STDIN`），将终端输出的流称为标准输出流（`STDOUT`）。但是，我们也可以重定向这些流。

最简单的重定向是 `< file` 和 `> file`。这两个命令可以将程序的输入输出流分别重定向到文件而不是标准输入输出。还可以使用 `>>` 来向一个文件追加内容。有以下例子。

第一个例子：

```shell
(base) ➜  missing-semester echo hello>hello.txt
(base) ➜  missing-semester cat<hello.txt
hello
(base) ➜  missing-semester cat<hello.txt>hello2.txt
(base) ➜  missing-semester cat hello2.txt
hello
(base) ➜  missing-semester echo goodbye>>hello2.txt
(base) ➜  missing-semester cat<hello2.txt 
hello
goodbye
(base) ➜  missing-semester cat<hello2.txt 
hello
goodbye                             
```

cat（concatente，用于连接文件并输出）与echo类似，都是对某个内容进行输出，只是echo的输入是标准输入，cat的输入是文件。值得注意的是`cat<hello.txt>hello2.txt`。这句话首先执行`cat<hello.txt`，然后其输出再执行`>hello2.txt`。对于这种连续的操作，我们还可以使用管道：

```shell
(base) ➜  missing-semester cat hello2.txt|cat>hello3.txt
(base) ➜  missing-semester cat hello3.txt 
hello
goodbye
```

## 使用shell进行系统设置

对于大多数的类 Unix 系统，有一类用户是非常特殊的，那就是：根用户（root user）。根用户几乎不受任何限制，他可以创建、读取、更新和删除系统中的任何文件。 通常在我们并不会以根用户的身份直接登录系统，因为这样可能会因为某些错误的操作而破坏系统。 取而代之的是我们会在需要的时候使用 `sudo` 命令。顾名思义，它的作用是让你可以以 su（super user 或 root 的简写）的身份执行一些操作。 当遇到拒绝访问（permission denied）的错误时，通常是因为此时必须是根用户才能操作。然而，请再次确认您是真的要执行此操作。

有一件事情是必须作为根用户才能做的，那就是向 `sysfs` 文件写入内容。系统被挂载在 `/sys` 下，`sysfs` 文件则暴露了一些内核（kernel）参数。 因此，不需要借助任何专用的工具，就可以轻松地在运行期间配置系统内核。

以下是一个简单的例子：

通常，屏幕的亮度都写在`/sys/class/backlight`下的一个名为`brightness`的文件中。通过将数值写入该文件，我们可以改变屏幕的亮度。我们首先找到这个文件：

```shell
(base) ➜  missing-semester sudo find -L /sys/class/backlight -maxdepth 2 -name '*brightness*'
[sudo] password for shaun: 
/sys/class/backlight/amdgpu_bl0/actual_brightness
/sys/class/backlight/amdgpu_bl0/brightness
find: File system loop detected; ‘/sys/class/backlight/amdgpu_bl0/subsystem’ is part of the same file system loop as ‘/sys/class/backlight’.
/sys/class/backlight/amdgpu_bl0/max_brightness
```

接下来我们试试对它进行修改：

```shell
(base) ➜  missing-semester sudo echo 1>/sys/class/backlight/amdgpu_bl0/brightness
zsh: permission denied: /sys/class/backlight/amdgpu_bl0/brightness
```

居然报错了！这是因为`|`、`>`、和 `<` 是通过 shell 执行的，而不是被各个程序单独执行。 `echo` 等程序并不知道 `|` 的存在，它们只知道从自己的输入输出流中进行读写。 对于上面这种情况， *shell* (权限为您的当前用户) 在设置 `sudo echo` 前（是的，在这里用`root`权限执行的只有`echo`，没有`>`）尝试打开 brightness 文件并写入，但是系统拒绝了 shell 的操作，因为此时 shell 不是根用户。

明白这一点后，我们可以这样操作：

```shell
(base) ➜  missing-semester echo 1 | sudo tee /sys/class/backlight/amdgpu_bl0/brightness
1
```

然后你的屏幕就会变得很暗。在这个例子中，因为打开 `/sys` 文件的是 `tee` 这个程序，并且该程序以 `root` 权限在运行，因此操作可以进行。 

## shell脚本

正如前面所说的那样，shell也是一个编程环境，我们可以在其中进行一些脚本的编写。这样就可以执行一系列的操作并使用条件或循环这样的控制流。大多数shell都有自己的一套脚本语言，包括变量、控制流和自己的语法。shell脚本与其他脚本语言不同之处在于，shell脚本是专门针对shell所从事的相关工作的。因此，创建命令流程（pipelines）、将结果保存到文件、从标准输入中读取输入，这些都是shell脚本中的原生操作，这让它比通用的脚本语言更易用。我们接下来介绍的语法都是基于使用最广泛的bash的，但是zsh可以完美兼容bash语法，因此在zsh中运行也不会有问题。

### 变量

在bash中为变量赋值的语法是`foo=bar`，访问变量则用 `$foo`。 需要注意的是，`foo = bar` （使用空格隔开）是不能正确工作的，因为解释器会调用程序`foo` 并将 `=` 和 `bar`作为参数。 总的来说，在shell脚本中使用空格会起到分割参数的作用，有时候可能会造成混淆，请务必多加检查。

Bash中的字符串通过`'` 和 `"`分隔符来定义，但是它们的含义并不相同。以`'`定义的字符串为原义字符串，其中的变量不会被转义，而 `"`定义的字符串会将变量值进行替换。

```shell
(base) ➜  missing-semester echo '$foo'
$foo
(base) ➜  missing-semester echo "$foo"
bar
```

### 控制流

和其他大多数的编程语言一样，`bash`也支持`if`, `case`, `while` 和 `for` 这些控制流关键字。

#### 条件判断

条件判断的语法如下：

```shell
if condition
then
    command1 
    command2
    ...
    commandN 
fi
```

```shell
if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi
```

```shell
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

`-gt -lt -eq -ne`分别表示大于、小于、等于、不等于。例子：

```shell
num1=$[2*3]
num2=$[1+5]
if test $[num1] -eq $[num2]
then
    echo '两个数字相等!'
else
    echo '两个数字不相等!'
fi
```

#### for循环

语法：

```shell
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```

例子：

```shell
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

#### while循环

语法：

```shell
while condition
do
    command
done
```

例子：

```shell
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

#### until循环

语法：

```shell
until condition
do
    command
done
```

例子：

```shell
a=0

until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1`
done
```

#### case esac多选择

语法：

```shell
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2)
    command1
    command2
    ...
    commandN
    ;;
esac
```

case 工作方式如上所示，取值后面必须为单词 **in**，每一模式必须以右括号结束。取值可以为变量或常数，匹配发现取值符合某一模式后，其间所有命令开始执行直至 **;;**。

取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。

例子：

```shell
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```

#### 跳出循环

break：

```shell
while :
do
    echo -n "输入 1 到 5 之间的数字:"
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的! 游戏结束"
            break
        ;;
    esac
done
```

continue：

```shell
while :
do
    echo -n "输入 1 到 5 之间的数字: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的!"
            continue
            echo "游戏结束"
        ;;
    esac
done
```



### 函数

同样地， `bash` 也支持函数，它可以接受参数并基于参数进行操作。下面这个函数是一个例子，它会创建一个文件夹并使用`cd`进入该文件夹。注意声明函数的形式。

```shell
(base) ➜  missing-semester mcd(){
function> mkdir -p "$1"
function> cd "$1"
function> }
(base) ➜  missing-semester mcd test
(base) ➜  test pwd
/home/shaun/workspace/missing-semester/test
```

这里 `$1` 是脚本的第一个参数。与其他脚本语言不同的是，bash使用了很多特殊的变量来表示参数、错误代码和相关变量。下面是列举来其中一些变量:

- `$0` - 脚本名
- `$1` 到 `$9` - 脚本的参数。 `$1` 是第一个参数，依此类推。类似于argv。
- `$@` - 所有参数
- `$#` - 参数个数
- `$?` - 前一个命令的返回值
- `$$` - 当前脚本的进程识别码
- `!!` - 完整的上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。
- `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。

我们还可以这样使用一个函数：首先新建一个脚本：

```shell
(base) ➜  missing-semester touch mcd.sh
```

然后将之前的mcd函数的代码写入其中。我们再用source命令载入这个函数，这样我们就可以在这个shell里使用mcd了。

```shell
(base) ➜  test source ./mcd.sh 
(base) ➜  test mcd testintest
(base) ➜  testintest pwd
/home/shaun/workspace/missing-semester/test/testintest
```

### 标准错误

命令通常使用 `STDOUT`来返回输出值，使用`STDERR` 来返回错误及错误码，便于脚本以更加友好的方式报告错误。 返回码或退出状态是脚本/命令之间交流执行状态的方式。返回值0表示正常执行，其他所有非0的返回值都表示有错误发生。

```shell
(base) ➜  test echo hello
hello
(base) ➜  test echo $?
0
(base) ➜  test grep foobar mcd.sh
(base) ➜  test echo $?
1
```

退出码可以搭配`&&` (与操作符) 和 `||` (或操作符)使用，用来进行条件判断，决定是否执行其他程序。它们都属于短路[运算符](https://en.wikipedia.org/wiki/Short-circuit_evaluation)（short-circuiting，即只要能够判断出结果，之后的命令不会再执行）。 同一行的多个命令可以用` ; `分隔。__程序 `true` 的返回码永远是`0`，`false` 的返回码永远是`1`。__让我们看几个例子:

```shell
(base) ➜  test false || echo "Oops, fail"
Oops, fail
(base) ➜  test true || echo "Will not be printed"
(base) ➜  test true && echo "Things went well"
Things went well
(base) ➜  test false && echo "Will not be printed"
(base) ➜  test false ; echo "This will always run"
This will always run
```

### 命令替换

另一个常见的模式是以变量的形式获取一个命令的输出，这可以通过 *命令替换* (*command substitution*)实现。当通过 `$(CMD)` 这样的方式来执行`CMD` 这个命令时，它的输出结果会替换掉 `$(CMD)` 。例如，如果执行 `for file in $(ls)` ，shell首先将调用`ls` ，然后遍历得到的这些返回值。还有一个冷门的类似特性是 *进程替换*（*process substitution*）， `<(CMD)` 会执行 `CMD` 并将结果输出到一个临时文件中，并将 `<(CMD)` 替换成临时文件名。这在我们希望返回值通过文件而不是标准输入输出传递时很有用。例如， `diff <(ls foo) <(ls bar)` 会显示文件夹 `foo` 和 `bar` 中文件的区别。

### 例子

接下来看一个例子，我们首先创建一个文件`add_foobar`:

```shell
(base) ➜  missing-semester touch add_foobar
```

然后在其中加入以下内容：

```shell
#!/bin/bash
echo "Starting program at $(date)" # date会被替换成日期和时间
echo "Running program $0 with $# arguments with pid $$"
for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # 如果模式没有找到，则grep退出状态为 1
    # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息。后面的2>就是用于处理stderr输出。
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

我们来看看这个程序是干什么的。首先第一句话会打印当前时间。第二句话会打印当前命令名、参数个数以及进程识别号。之后，针对命令的每个参数，我们使用grep寻找字符串foobar，并将grep的输出重定向到null，如果没有找到，则将foobar作为注释追加到文件中。

接下来，我们执行这个命令。

```shell
(base) ➜  test ./add_foobar
bash: permission denied: ./add_foobar
```

我们发现又报错了，提示我们没有权限。我们使用`ls -l`命令来看一下原因：

```shell
(base) ➜  test ls -l
total 4
-rw-rw-r-- 1 shaun shaun 512 8月  26 15:48 add_foobar
```

我们会发现，该程序对于所有的用户都没有可执行权限。我们需要修改权限：

```shell
(base) ➜  test chmod 777 ./add_foobar
```

`chmod`（change mode）命令用于修改文件权限。777表示文件的权限。linux中将文件权限用3个8进制数来表示，每个数字分别表示三类用户（ugo，可以参考上面关于`ls -l`的描述）所具有的权限。权限又分为三类，即rwx，按照是否有该权限将权限编码为3位二进制数，换算过来就是一个8进制数。例如三种权限都有就编码为111，将其换算成8进制数就是7。777表示三类用户都具有rwx权限。

```shell
(base) ➜  test ls -l 
total 4
-rwxrwxrwx 1 shaun shaun 512 8月  26 15:48 add_foobar
```

接下来就可以执行程序了。

```shell
(base) ➜  test ./add_foobar mcd.sh add_foobar
Starting program at 2022年 01月 05日 星期三 12:53:47 CST
Running program ./add_foobar with 2 arguments with pid 11276
File mcd.sh does not have any foobar, adding one
File add_foobar has foobar
```



在条件语句中，我们比较 `$?` 是否等于0。 Bash实现了许多类似的比较操作，您可以查看 [`test 手册`](https://man7.org/linux/man-pages/man1/test.1.html)。 在bash或zsh中进行比较时，尽量使用双方括号 `[[ ]]` 而不是单方括号 `[ ]`，这样会降低犯错的几率，尽管这样并不能兼容 `sh`。 更详细的说明参见[这里](http://mywiki.wooledge.org/BashFAQ/031)。

### 通配

#### 通配符

我们可以使用?和*来匹配一个或者任意个字符。例如：

```shell
(base) ➜  missing-semester ls
hello10.txt  hello2.txt  hello3.txt  hello.txt  test
(base) ➜  missing-semester ls hello*.txt
hello10.txt  hello2.txt  hello3.txt  hello.txt
(base) ➜  missing-semester ls hello?.txt
hello2.txt  hello3.txt
```

此外还有其他大量的通配符。

#### 花括号

当你有一系列的指令，其中包含一段公共子串时，可以用花括号来自动展开这些命令。这在批量移动或转换文件时非常方便。

例子：

```shell
convert image.{png,jpg}
# 会展开为
convert image.png image.jpg

cp /path/to/project/{foo,bar,baz}.sh /newpath
# 会展开为
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath

# 也可以结合通配使用
mv *{.py,.sh} folder
# 会移动所有 *.py 和 *.sh 文件

mkdir foo bar

# 下面命令会创建foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h这些文件
touch {foo,bar}/{a..h}
touch foo/x bar/y
# 比较文件夹 foo 和 bar 中包含文件的不同
diff <(ls foo) <(ls bar)
# 输出
# < x
# ---
# > y
```

### 函数与脚本

编写 `bash` 脚本有时候会很别扭和反直觉。例如 [shellcheck](https://github.com/koalaman/shellcheck)这样的工具可以帮助你定位sh/bash脚本中的错误。

注意，脚本并不一定只有用bash写才能在终端里调用。比如说，这是一段Python脚本，作用是将输入的参数倒序输出：

```python
#!/usr/local/bin/python
import sys
for arg in reversed(sys.argv[1:]):
    print(arg)
```

内核知道去用python解释器而不是shell命令来运行这段脚本，是因为脚本的开头第一行的[shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))。

在 `shebang` 行中使用 [`env`](https://man7.org/linux/man-pages/man1/env.1.html) 命令是一种好的实践，它会利用环境变量中的程序来解析该脚本，这样就提高来您的脚本的可移植性（例如，不是所有电脑上python所在目录都是`/usr/local/bin`）。`env` 会利用我们第一节讲座中介绍过的`PATH` 环境变量来进行定位。 例如，使用了`env`的shebang看上去是这样的`#!/usr/bin/env python`。

shell函数和脚本有如下一些不同点：

- 函数只能用与shell使用相同的语言，脚本可以使用任意语言。因此在脚本中包含 `shebang` 是很重要的。
- 函数仅在定义时被加载，脚本会在每次被执行时加载。这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次。
- 函数会在当前的shell环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。脚本需要使用 [`export`](httsp://man7.org/linux/man-pages/man1/export.1p.html) 将环境变量导出，并将值传递给环境变量。
- 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell脚本中往往也会包含它们自己的函数定义。

## 常用的shell工具

### 查询命令如何使用

常用的查询方法有三种。第一种就是许多命令都会有-h或者--help选项来输出一些帮助文档。第二种方法就是使用man命令，通常针对linux自带的一些命令。第三种就是，在交互式的、基于字符处理的终端窗口中，一般也可以通过 `:help` 命令或键入 `?`来获取帮助。

另外推荐一个小工具[tldr](https://tldr.sh/)用于替代man。这是一个社区维护的帮助文档，里面对许多命令有简介和使用的例子。由于man命令在很多情况下有点过于繁琐，tldr可以有效帮助我们找到最常用的用法。

### 查找文件

程序员们面对的最常见的重复任务就是查找文件或目录。所有的类UNIX系统都包含一个名为 [`find`](https://man7.org/linux/man-pages/man1/find.1.html)的工具，它是shell上用于查找文件的绝佳工具。`find`命令会递归地搜索符合条件的文件，例如：

```shell
# 查找所有名称为src的文件夹
shaun@shaun-KPL-W0X  ~/workspace/CPPLearning   main  find . -name src -type d
./week04/exercises/exercise1/build/CMakeFiles/stuinfo.dir/src
./week04/exercises/exercise1/src
./week04/exercises/cmake3/build/CMakeFiles/hello.dir/src
./week04/exercises/cmake3/src
./week04/exercises/exercise2/build/CMakeFiles/dayinfo.dir/src
./week04/exercises/exercise2/src
# 查找所有文件夹路径中包含src的cpp文件
shaun@shaun-KPL-W0X  ~/workspace/CPPLearning   main  find . -path '*/src/*.cpp' -type f
./week04/exercises/exercise1/src/main.cpp
./week04/exercises/exercise1/src/stuinfo.cpp
./week04/exercises/cmake3/src/main.cpp
./week04/exercises/cmake3/src/func.cpp
./week04/exercises/exercise2/src/dayinfo.cpp
./week04/exercises/exercise2/src/main.cpp
# 查找前一天修改的所有文件
find . -mtime -1
./week04/exercises/exercise1/src/main.cpp
./week04/exercises/exercise1/src/stuinfo.cpp
./week04/exercises/cmake3/src/main.cpp
./week04/exercises/cmake3/src/func.cpp
./week04/exercises/exercise2/src/dayinfo.cpp
./week04/exercises/exercise2/src/main.cpp
# 查找所有大小在500k至10M的tar.gz文件
find . -size +500k -size -10M -name '*.tar.gz'
```

除了列出所寻找的文件之外，find还能对所有查找到的文件进行操作。这能极大地简化一些单调的任务。

```shell
# 删除全部扩展名为.tmp 的文件
find . -name '*.tmp' -exec rm {} \;
# 查找全部的 PNG 文件并将其转换为 JPG
find . -name '*.png' -exec convert {} {}.jpg \;
```

尽管 `find` 用途广泛，它的语法却比较难以记忆。例如，为了查找满足模式 `PATTERN` 的文件，您需要执行 `find -name 'PATTERN'` (如果您希望模式匹配时是不区分大小写，可以使用`-iname`选项）

您当然可以使用alias设置别名来简化上述操作，但shell的哲学之一便是寻找（更好用的）替代方案。 记住，shell最好的特性就是您只是在调用程序，因此您只要找到合适的替代程序即可（甚至自己编写）。

例如， [`fd`](https://github.com/sharkdp/fd) 就是一个更简单、更快速、更友好的程序，它可以用来作为`find`的替代品。它有很多不错的默认设置，例如输出着色、默认支持正则匹配、支持unicode并且我认为它的语法更符合直觉。以模式`PATTERN` 搜索的语法是 `fd PATTERN`。

大多数人都认为 `find` 和 `fd` 已经很好用了，但是有的人可能想知道，我们是不是可以有更高效的方法，例如不要每次都搜索文件而是通过编译索引或建立数据库的方式来实现更加快速地搜索。

这就要靠 [`locate`](https://man7.org/linux/man-pages/man1/locate.1.html) 了。 `locate` 使用一个由 [`updatedb`](https://man7.org/linux/man-pages/man1/updatedb.1.html)负责更新的数据库，在大多数系统中 `updatedb` 都会通过 [`cron`](https://man7.org/linux/man-pages/man8/cron.8.html)每日更新。这便需要我们在速度和时效性之间作出权衡。而且，`find` 和类似的工具可以通过别的属性比如文件大小、修改时间或是权限来查找文件，`locate`则只能通过文件名。 [这里](https://unix.stackexchange.com/questions/60205/locate-vs-find-usage-pros-and-cons-of-each-other)有一个更详细的对比。

### 查找代码

查找文件是很有用的技能，但是很多时候您的目标其实是查看文件的内容。一个最常见的场景是您希望查找具有某种模式的全部文件，并找它们的位置。

为了实现这一点，很多类UNIX的系统都提供了[`grep`](https://man7.org/linux/man-pages/man1/grep.1.html)命令，它是用于对输入文本进行匹配的通用工具。它是一个非常重要的shell工具，我们会在后续的数据清理课程中深入的探讨它。

`grep` 有很多选项，这也使它成为一个非常全能的工具。其中我经常使用的有 `-C` ：获取查找结果的上下文（Context）；`-v` 将对结果进行反选（Invert），也就是输出不匹配的结果。举例来说， `grep -C 5` 会输出匹配结果前后五行。当需要搜索大量文件的时候，使用 `-R` 会递归地进入子目录并搜索所有的文本文件。

但是，我们有很多办法可以对 `grep -R` 进行改进，例如使其忽略`.git` 文件夹，使用多CPU等等。

因此也出现了很多它的替代品，包括 [ack](https://beyondgrep.com/), [ag](https://github.com/ggreer/the_silver_searcher) 和 [rg](https://github.com/BurntSushi/ripgrep)。它们都特别好用，但是功能也都差不多，我比较常用的是 ripgrep (`rg`) ，因为它速度快，而且用法非常符合直觉。例子如下：

```shell
# 查找所有使用了 requests 库的文件
rg -t py 'import requests'
# 查找所有没有写 shebang 的文件（包含隐藏文件）
rg -u --files-without-match "^#!"
# 查找所有的foo字符串，并打印其之后的5行
rg foo -A 5
# 打印匹配的统计信息（匹配的行和文件的数量）
rg --stats PATTERN
```

与 `find`/`fd` 一样，**重要的是你要知道有些问题使用合适的工具就会迎刃而解，而具体选择哪个工具则不是那么重要。**

## 查找 shell 命令

目前为止，我们已经学习了如何查找文件和代码，但随着你使用shell的时间越来越久，您可能想要找到之前输入过的某条命令。首先，按向上的方向键会显示你使用过的上一条命令，继续按上键则会遍历整个历史记录。

`history` 命令允许您以程序员的方式来访问shell中输入的历史命令。这个命令会在标准输出中打印shell中的里面命令。如果我们要搜索历史记录，则可以利用管道将输出结果传递给 `grep` 进行模式搜索。 `history | grep find` 会打印包含find子串的命令。

对于大多数的shell来说，您可以使用 `Ctrl+R` 对命令历史记录进行回溯搜索。敲 `Ctrl+R` 后您可以输入子串来进行匹配，查找历史命令行。

反复按下就会在所有搜索结果中循环。在 [zsh](https://github.com/zsh-users/zsh-history-substring-search)中，使用方向键上或下也可以完成这项工作。

`Ctrl+R` 可以配合 [fzf](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings#ctrl-r) 使用。`fzf` 是一个通用对模糊查找工具，它可以和很多命令一起使用。这里我们可以对历史命令进行模糊查找并将结果以赏心悦目的格式输出。

另外一个和历史命令相关的技巧我喜欢称之为**基于历史的自动补全**。 这一特性最初是由 [fish](https://fishshell.com/) shell 创建的，它可以根据您最近使用过的开头相同的命令，动态地对当前对shell命令进行补全。这一功能在 [zsh](https://github.com/zsh-users/zsh-autosuggestions) 中也可以使用，它可以极大的提高用户体验。

你可以修改 shell history 的行为，例如，如果在命令的开头加上一个空格，它就不会被加进shell记录中。当你输入包含密码或是其他敏感信息的命令时会用到这一特性。 为此你需要在`.bashrc`中添加`HISTCONTROL=ignorespace`或者向`.zshrc` 添加 `setopt HIST_IGNORE_SPACE`。 如果你不小心忘了在前面加空格，可以通过编辑。`bash_history`或 `.zhistory` 来手动地从历史记录中移除那一项。

## 文件夹导航

之前对所有操作我们都默认一个前提，即您已经位于想要执行命令的目录下，但是如何才能高效地在目录 间随意切换呢？有很多简便的方法可以做到，比如设置alias，使用 [ln -s](https://man7.org/linux/man-pages/man1/ln.1.html)创建符号连接等。而开发者们已经想到了很多更为精妙的解决方案。

由于本课程的目的是尽可能对你的日常习惯进行优化。因此，我们可以使用[`fasd`](https://github.com/clvv/fasd)和[autojump](https://github.com/wting/autojump)这两个工具来查找最常用或最近使用的文件和目录。

Fasd 基于 [*frecency*](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/Places/Frecency_algorithm)对文件和文件排序，也就是说它会同时针对频率（*frequency* ）和时效（ *recency*）进行排序。默认情况下，`fasd`使用命令 `z` 帮助我们快速切换到最常访问的目录。例如， 如果您经常访问`/home/user/files/cool_project` 目录，那么可以直接使用 `z cool` 跳转到该目录。对于 autojump，则使用`j cool`代替即可。

还有一些更复杂的工具可以用来概览目录结构，例如 [`tree`](https://linux.die.net/man/1/tree), [`broot`](https://github.com/Canop/broot) 或更加完整的文件管理器，例如 [`nnn`](https://github.com/jarun/nnn) 或 [`ranger`](https://github.com/ranger/ranger)。

# 参考文献:

> https://missing-semester-cn.github.io/2020/course-shell/
>
> https://missing-semester-cn.github.io/2020/shell-tools/
>
> http://www.ee.surrey.ac.uk/Teaching/Unix/  
>
> https://www.runoob.com/linux/linux-shell-process-control.html
>
> 《Linux就是这个范儿》
>
> 《鸟哥的Linux私房菜》