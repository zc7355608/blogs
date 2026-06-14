## scp 命令

> `scp`是 SSH 提供的一个客户端程序，用来在两台主机之间加密传送文件（即复制文件）。

- #### 简介

  > `scp`是 secure copy 的缩写，相当于`cp`命令 + SSH。它的底层是 SSH 协议，默认端口是22，相当于先使用`ssh`命令登录远程主机，然后再执行拷贝操作。
  >
  > `scp`主要用于以下三种复制操作。
  >
  > - 本地复制到远程。
  > - 远程复制到本地。
  > - 两个远程系统之间的复制。
  >
  > 使用`scp`传输数据时，文件和密码都是加密的，不会泄漏敏感信息。

- #### 基本语法

  > `scp`的语法类似`cp`的语法。
  >
  > ```
  > $ scp source destination
  > ```
  >
  > 上面命令中，`source`是文件当前的位置，`destination`是文件所要复制到的位置。它们都可以包含用户名和主机名。
  >
  > ```
  > $ scp user@host:foo.txt bar.txt
  > ```
  >
  > 上面命令将远程主机（`user@host`）用户主目录下的`foo.txt`，复制为本机当前目录的`bar.txt`。可以看到，主机与文件之间要使用冒号（`:`）分隔。
  >
  > `scp`会先用 SSH 登录到远程主机，然后在加密连接之中复制文件。客户端发起连接后，会提示用户输入密码，这部分是跟 SSH 的用法一致的。
  >
  > 用户名和主机名都是可以省略的。用户名的默认值是本机的当前用户名，主机名默认为当前主机。注意，`scp`会使用 SSH 客户端的配置文件`.ssh/config`，如果配置文件里面定义了主机的别名，这里也可以使用别名连接。
  >
  > `scp`支持一次复制多个文件。
  >
  > ```
  > $ scp source1 source2 destination
  > ```
  >
  > 上面命令会将`source1`和`source2`两个文件，复制到`destination`。
  >
  > 注意，如果所要复制的文件，在目标位置已经存在同名文件，`scp`会在没有警告的情况下覆盖同名文件。

- #### 用法示例

  > **（1）本地文件复制到远程**
  >
  > 复制本机文件到远程系统的用法如下。
  >
  > ```
  > # 语法
  > $ scp SourceFile user@host:directory/TargetFile
  > 
  > # 示例
  > $ scp file.txt remote_username@10.10.0.2:/remote/directory
  > ```
  >
  > 下面是复制整个目录的例子。
  >
  > ```
  > # 将本机的 documents 目录拷贝到远程主机，
  > # 会在远程主机创建 documents 目录
  > $ scp -r documents username@server_ip:/path_to_remote_directory
  > 
  > # 将本机整个目录拷贝到远程目录下
  > $ scp -r localmachine/path_to_the_directory username@server_ip:/path_to_remote_directory/
  > 
  > # 将本机目录下的所有内容拷贝到远程目录下
  > $ scp -r localmachine/path_to_the_directory/* username@server_ip:/path_to_remote_directory/
  > ```
  >
  > **（2）远程文件复制到本地**
  >
  > 从远程主机复制文件到本地的用法如下。
  >
  > ```
  > # 语法
  > $ scp user@host:directory/SourceFile TargetFile
  > 
  > # 示例
  > $ scp remote_username@10.10.0.2:/remote/file.txt /local/directory
  > ```
  >
  > 下面是复制整个目录的例子。
  >
  > ```
  > # 拷贝一个远程目录到本机目录下
  > $ scp -r username@server_ip:/path_to_remote_directory local-machine/path_to_the_directory/
  > 
  > # 拷贝远程目录下的所有内容，到本机目录下
  > $ scp -r username@server_ip:/path_to_remote_directory/* local-machine/path_to_the_directory/
  > $ scp -r user@host:directory/SourceFolder TargetFolder
  > ```
  >
  > **（3）两个远程系统之间的复制**
  >
  > 本机发出指令，从远程主机 A 拷贝到远程主机 B 的用法如下。
  >
  > ```
  > # 语法
  > $ scp user@host1:directory/SourceFile user@host2:directory/SourceFile
  > 
  > # 示例
  > $ scp user1@host1.com:/files/file.txt user2@host2.com:/files
  > ```
  >
  > 系统将提示你输入两个远程帐户的密码。数据将直接从一个远程主机传输到另一个远程主机。

- #### 配置项

  > **（1）`-c`**
  >
  > `-c`参数用来指定文件拷贝数据传输的加密算法。
  >
  > ```
  > $ scp -c blowfish some_file your_username@remotehost.edu:~
  > ```
  >
  > 上面代码指定加密算法为`blowfish`。
  >
  > **（2）`-C`**
  >
  > `-C`参数表示是否在传输时压缩文件。
  >
  > ```
  > $ scp -c blowfish -C local_file your_username@remotehost.edu:~
  > ```
  >
  > **（3）`-F`**
  >
  > `-F`参数用来指定 ssh_config 文件，供 ssh 使用。
  >
  > ```
  > $ scp -F /home/pungki/proxy_ssh_config Label.pdf root@172.20.10.8:/root
  > ```
  >
  > **（4）`-i`**
  >
  > `-i`参数用来指定密钥。
  >
  > ```
  > $ scp -vCq -i private_key.pem ~/test.txt root@192.168.1.3:/some/path/test.txt
  > ```
  >
  > **（5）`-l`**
  >
  > `-l`参数用来限制传输数据的带宽速率，单位是 Kbit/sec。对于多人分享的带宽，这个参数可以留出一部分带宽供其他人使用。
  >
  > ```
  > $ scp -l 80 yourusername@yourserver:/home/yourusername/* .
  > ```
  >
  > 上面代码中，`scp`命令占用的带宽限制为每秒 80K 比特位，即每秒 10K 字节。
  >
  > **（6）`-p`**
  >
  > `-p`参数用来保留修改时间（modification time）、访问时间（access time）、文件状态（mode）等原始文件的信息。
  >
  > ```
  > $ scp -p ~/test.txt root@192.168.1.3:/some/path/test.txt
  > ```
  >
  > **（7）`-P`**
  >
  > `-P`参数用来指定远程主机的 SSH 端口。如果远程主机使用默认端口22，可以不用指定，否则需要用`-P`参数在命令中指定。
  >
  > ```
  > $ scp -P 2222 user@host:directory/SourceFile TargetFile
  > ```
  >
  > **（8）`-q`**
  >
  > `-q`参数用来关闭显示拷贝的进度条。
  >
  > ```
  > $ scp -q Label.pdf mrarianto@202.x.x.x:.
  > ```
  >
  > **（9）`-r`**
  >
  > `-r`参数表示是否以递归方式复制目录。
  >
  > **（10）`-v`**
  >
  > `-v`参数用来显示详细的输出。
  >
  > ```bash
  > $ scp -v ~/test.txt root@192.168.1.3:/root/help2356.txt
  > ```

## rsync 命令

- #### 简介

  > rsync 是一个常用的 Linux 应用程序，用于文件同步。
  >
  > 它可以在本地计算机与远程计算机之间，或者两个本地目录之间同步文件（但不支持两台远程计算机之间的同步）。它也可以当作文件复制工具，替代`cp`和`mv`命令。
  >
  > 它名称里面的`r`指的是 remote，rsync 其实就是“远程同步”（remote sync）的意思。与其他文件传输工具（如 FTP 或 scp）不同，rsync 的最大特点是会检查发送方和接收方已有的文件，仅传输有变动的部分（默认规则是文件大小或修改时间有变动）。
  >
  > 虽然 rsync 不是 SSH 工具集的一部分，但因为也涉及到远程操作，所以放在这里一起介绍。

- #### 安装

  > 如果本机或者远程计算机没有安装 rsync，可以用下面的命令安装。
  >
  > ```
  > # Debian
  > $ sudo apt-get install rsync
  > 
  > # Red Hat
  > $ sudo yum install rsync
  > 
  > # Arch Linux
  > $ sudo pacman -S rsync
  > ```
  >
  > 注意，传输的双方都必须安装 rsync。

- #### 基本用法

  > rsync 可以用于本地计算机的两个目录之间的同步。下面就用本地同步举例，顺便讲解 rsync 几个主要参数的用法。

  - ##### `-r` 参数

    > 本机使用 rsync 命令时，可以作为`cp`和`mv`命令的替代方法，将源目录拷贝到目标目录。
    >
    > ```
    > $ rsync -r source destination
    > ```
    >
    > 上面命令中，`-r`表示递归，即包含子目录。注意，`-r`是必须的，否则 rsync 运行不会成功。`source`目录表示源目录，`destination`表示目标目录。上面命令执行以后，目标目录下就会出现`destination/source`这个子目录。
    >
    > 如果有多个文件或目录需要同步，可以写成下面这样。
    >
    > ```
    > $ rsync -r source1 source2 destination
    > ```
    >
    > 上面命令中，`source1`、`source2`都会被同步到`destination`目录。

  - ##### `-a` 参数

    > `-a`参数可以替代`-r`，除了可以递归同步以外，还可以同步元信息（比如修改时间、权限等）。由于 rsync 默认使用文件大小和修改时间决定文件是否需要更新，所以`-a`比`-r`更有用。下面的用法才是常见的写法。
    >
    > ```
    > $ rsync -a source destination
    > ```
    >
    > 目标目录`destination`如果不存在，rsync 会自动创建。执行上面的命令后，源目录`source`被完整地复制到了目标目录`destination`下面，即形成了`destination/source`的目录结构。
    >
    > 如果只想同步源目录`source`里面的内容到目标目录`destination`，则需要在源目录后面加上斜杠。
    >
    > ```
    > $ rsync -a source/ destination
    > ```
    >
    > 上面命令执行后，`source`目录里面的内容，就都被复制到了`destination`目录里面，并不会在`destination`下面创建一个`source`子目录。

  - ##### `-n` 参数

    > 如果不确定 rsync 执行后会产生什么结果，可以先用`-n`或`--dry-run`参数模拟执行的结果。
    >
    > ```
    > $ rsync -anv source/ destination
    > ```
    >
    > 上面命令中，`-n`参数模拟命令执行的结果，并不真的执行命令。`-v`参数则是将结果输出到终端，这样就可以看到哪些内容会被同步。

  - ##### `--delete` 参数

    > 默认情况下，rsync 只确保源目录的所有内容（明确排除的文件除外）都复制到目标目录。它不会使两个目录保持相同，并且不会删除文件。如果要使得目标目录成为源目录的镜像副本，则必须使用`--delete`参数，这将删除只存在于目标目录、不存在于源目录的文件。
    >
    > ```
    > $ rsync -av --delete source/ destination
    > ```
    >
    > 上面命令中，`--delete`参数会使得`destination`成为`source`的一个镜像。

- #### 排除文件

  - ##### `--exclude` 参数

    > 有时，我们希望同步时排除某些文件或目录，这时可以用`--exclude`参数指定排除模式。
    >
    > ```
    > $ rsync -av --exclude='*.txt' source/ destination
    > # 或者
    > $ rsync -av --exclude '*.txt' source/ destination
    > ```
    >
    > 上面命令排除了所有 TXT 文件。
    >
    > 注意，rsync 会同步以“点”开头的隐藏文件，如果要排除隐藏文件，可以这样写`--exclude=".*"`。
    >
    > 如果要排除某个目录里面的所有文件，但不希望排除目录本身，可以写成下面这样。
    >
    > ```
    > $ rsync -av --exclude 'dir1/*' source/ destination
    > ```
    >
    > 多个排除模式，可以用多个`--exclude`参数。
    >
    > ```
    > $ rsync -av --exclude 'file1.txt' --exclude 'dir1/*' source/ destination
    > ```
    >
    > 多个排除模式也可以利用 Bash 的大扩号的扩展功能，只用一个`--exclude`参数。
    >
    > ```
    > $ rsync -av --exclude={'file1.txt','dir1/*'} source/ destination
    > ```
    >
    > 如果排除模式很多，可以将它们写入一个文件，每个模式一行，然后用`--exclude-from`参数指定这个文件。
    >
    > ```bash
    > $ rsync -av --exclude-from='exclude-file.txt' source/ destination
    > ```

  - ##### `--include` 参数

    > `--include`参数用来指定必须同步的文件模式，往往与`--exclude`结合使用。
    >
    > ```
    > $ rsync -av --include="*.txt" --exclude='*' source/ destination
    > ```
    >
    > 上面命令指定同步时，排除所有文件，但是会包括 TXT 文件。

- #### 远程同步

  - ##### SSH 协议

    > rsync 除了支持本地两个目录之间的同步，也支持远程同步。它可以将本地内容，同步到远程服务器。
    >
    > ```
    > $ rsync -av source/ username@remote_host:destination
    > ```
    >
    > 也可以将远程内容同步到本地。
    >
    > ```
    > $ rsync -av username@remote_host:source/ destination
    > ```
    >
    > rsync 默认使用 SSH 进行远程登录和数据传输。
    >
    > 由于早期 rsync 不使用 SSH 协议，需要用`-e`参数指定协议，后来才改的。所以，下面`-e ssh`可以省略。
    >
    > ```
    > $ rsync -av -e ssh source/ user@remote_host:/destination
    > ```
    >
    > 但是，如果 ssh 命令有附加的参数，则必须使用`-e`参数指定所要执行的 SSH 命令。
    >
    > ```
    > $ rsync -av -e 'ssh -p 2234' source/ user@remote_host:/destination
    > ```
    >
    > 上面命令中，`-e`参数指定 SSH 使用2234端口。

  - ##### rsync 协议

    > 除了使用 SSH，如果另一台服务器安装并运行了 rsync 守护程序，则也可以用`rsync://`协议（默认端口873）进行传输。具体写法是服务器与目标目录之间使用双冒号分隔`::`。
    >
    > ```
    > $ rsync -av source/ 192.168.122.32::module/destination
    > ```
    >
    > 注意，上面地址中的`module`并不是实际路径名，而是 rsync 守护程序指定的一个资源名，由管理员分配。
    >
    > 如果想知道 rsync 守护程序分配的所有 module 列表，可以执行下面命令。
    >
    > ```
    > $ rsync rsync://192.168.122.32
    > ```
    >
    > rsync 协议除了使用双冒号，也可以直接用`rsync://`协议指定地址。
    >
    > ```bash
    > $ rsync -av source/ rsync://192.168.122.32/module/destination
    > ```

- #### 增量备份

  > rsync 的最大特点就是它可以完成增量备份，也就是默认只复制有变动的文件。
  >
  > 除了源目录与目标目录直接比较，rsync 还支持使用基准目录，即将源目录与基准目录之间变动的部分，同步到目标目录。
  >
  > 具体做法是，第一次同步是全量备份，所有文件在基准目录里面同步一份。以后每一次同步都是增量备份，只同步源目录与基准目录之间有变动的部分，将这部分保存在一个新的目标目录。这个新的目标目录之中，也是包含所有文件，但实际上，只有那些变动过的文件是存在于该目录，其他没有变动的文件都是指向基准目录文件的硬链接。
  >
  > `--link-dest`参数用来指定同步时的基准目录。
  >
  > ```
  > $ rsync -a --delete --link-dest /compare/path /source/path /target/path
  > ```
  >
  > 上面命令中，`--link-dest`参数指定基准目录`/compare/path`，然后源目录`/source/path`跟基准目录进行比较，找出变动的文件，将它们拷贝到目标目录`/target/path`。那些没变动的文件则会生成硬链接。这个命令的第一次备份时是全量备份，后面就都是增量备份了。
  >
  > 下面是一个脚本示例，备份用户的主目录。
  >
  > ```
  > #!/bin/bash
  > 
  > # A script to perform incremental backups using rsync
  > 
  > set -o errexit
  > set -o nounset
  > set -o pipefail
  > 
  > readonly SOURCE_DIR="${HOME}"
  > readonly BACKUP_DIR="/mnt/data/backups"
  > readonly DATETIME="$(date '+%Y-%m-%d_%H:%M:%S')"
  > readonly BACKUP_PATH="${BACKUP_DIR}/${DATETIME}"
  > readonly LATEST_LINK="${BACKUP_DIR}/latest"
  > 
  > mkdir -p "${BACKUP_DIR}"
  > 
  > rsync -av --delete \
  >   "${SOURCE_DIR}/" \
  >   --link-dest "${LATEST_LINK}" \
  >   --exclude=".cache" \
  >   "${BACKUP_PATH}"
  > 
  > rm -rf "${LATEST_LINK}"
  > ln -s "${BACKUP_PATH}" "${LATEST_LINK}"
  > ```
  >
  > 上面脚本中，每一次同步都会生成一个新目录`${BACKUP_DIR}/${DATETIME}`，并将软链接`${BACKUP_DIR}/latest`指向这个目录。下一次备份时，就将`${BACKUP_DIR}/latest`作为基准目录，生成新的备份目录。最后，再将软链接`${BACKUP_DIR}/latest`指向新的备份目录。

- #### 配置项

  > `-a`、`--archive`参数表示存档模式，保存所有的元数据，比如修改时间（modification time）、权限、所有者等，并且软链接也会同步过去。
  >
  > `--append`参数指定文件接着上次中断的地方，继续传输。
  >
  > `--append-verify`参数跟`--append`参数类似，但会对传输完成后的文件进行一次校验。如果校验失败，将重新发送整个文件。
  >
  > `-b`、`--backup`参数指定在删除或更新目标目录已经存在的文件时，将该文件更名后进行备份，默认行为是删除。更名规则是添加由`--suffix`参数指定的文件后缀名，默认是`~`。
  >
  > `--backup-dir`参数指定文件备份时存放的目录，比如`--backup-dir=/path/to/backups`。
  >
  > `--bwlimit`参数指定带宽限制，默认单位是 KB/s，比如`--bwlimit=100`。
  >
  > `-c`、`--checksum`参数改变`rsync`的校验方式。默认情况下，rsync 只检查文件的大小和最后修改日期是否发生变化，如果发生变化，就重新传输；使用这个参数以后，则通过判断文件内容的校验和，决定是否重新传输。
  >
  > `--delete`参数删除只存在于目标目录、不存在于源目标的文件，即保证目标目录是源目标的镜像。
  >
  > `-e`参数指定使用 SSH 协议传输数据。
  >
  > `--exclude`参数指定排除不进行同步的文件，比如`--exclude="*.iso"`。
  >
  > `--exclude-from`参数指定一个本地文件，里面是需要排除的文件模式，每个模式一行。
  >
  > `--existing`、`--ignore-non-existing`参数表示不同步目标目录中不存在的文件和目录。
  >
  > `-h`参数表示以人类可读的格式输出。
  >
  > `-h`、`--help`参数返回帮助信息。
  >
  > `-i`参数表示输出源目录与目标目录之间文件差异的详细情况。
  >
  > `--ignore-existing`参数表示只要该文件在目标目录中已经存在，就跳过去，不再同步这些文件。
  >
  > `--include`参数指定同步时要包括的文件，一般与`--exclude`结合使用。
  >
  > `--link-dest`参数指定增量备份的基准目录。
  >
  > `-m`参数指定不同步空目录。
  >
  > `--max-size`参数设置传输的最大文件的大小限制，比如不超过200KB（`--max-size='200k'`）。
  >
  > `--min-size`参数设置传输的最小文件的大小限制，比如不小于10KB（`--min-size=10k`）。
  >
  > `-n`参数或`--dry-run`参数模拟将要执行的操作，而并不真的执行。配合`-v`参数使用，可以看到哪些内容会被同步过去。
  >
  > `-P`参数是`--progress`和`--partial`这两个参数的结合。
  >
  > `--partial`参数允许恢复中断的传输。不使用该参数时，`rsync`会删除传输到一半被打断的文件；使用该参数后，传输到一半的文件也会同步到目标目录，下次同步时再恢复中断的传输。一般需要与`--append`或`--append-verify`配合使用。
  >
  > `--partial-dir`参数指定将传输到一半的文件保存到一个临时目录，比如`--partial-dir=.rsync-partial`。一般需要与`--append`或`--append-verify`配合使用。
  >
  > `--progress`参数表示显示进展。
  >
  > `-r`参数表示递归，即包含子目录。
  >
  > `--remove-source-files`参数表示传输成功后，删除发送方的文件。
  >
  > `--size-only`参数表示只同步大小有变化的文件，不考虑文件修改时间的差异。
  >
  > `--suffix`参数指定文件名备份时，对文件名添加的后缀，默认是`~`。
  >
  > `-u`、`--update`参数表示同步时跳过目标目录中修改时间更新的文件，即不同步这些有更新的时间戳的文件。
  >
  > `-v`参数表示输出细节。`-vv`表示输出更详细的信息，`-vvv`表示输出最详细的信息。
  >
  > `--version`参数返回 rsync 的版本。
  >
  > `-z`参数指定同步时压缩数据。

## sftp 命令

> `sftp`是 SSH 提供的一个客户端应用程序，主要用来安全地访问 FTP。因为 FTP 是不加密协议，很不安全，`sftp`就相当于将 FTP 放入了 SSH。
>
> 下面的命令连接 FTP 主机。
>
> ```
> $ sftp username@hostname
> ```
>
> 执行上面的命令，会要求输入 FTP 的密码。密码验证成功以后，就会出现 FTP 的提示符`sftp>`，下面是一个例子。
>
> ```
> $ sftp USER@penguin.example.com
> USER@penguin.example.com's password:
> Connected to penguin.example.com.
> sftp>
> ```
>
> FTP 的提示符下面，就可以输入各种 FTP 命令了，这部分完全跟传统的 FTP 用法完全一样。
>
> - `ls [directory]`：列出一个远程目录的内容。如果没有指定目标目录，则默认列出当前目录。
> - `cd directory`：从当前目录改到指定目录。
> - `mkdir directory`：创建一个远程目录。
> - `rmdir path`：删除一个远程目录。
> - `put localfile [remotefile]`：本地文件传输到远程主机。
> - `get remotefile [localfile]`：远程文件传输到本地。
> - `help`：显示帮助信息。
> - `bye`：退出 sftp。
> - `quit`：退出 sftp。
> - `exit`：退出 sftp。

## Fail2ban 教程

- #### 简介

  > Fail2Ban 是一个 Linux 系统的应用软件，用来防止系统入侵，主要是防止暴力破解系统密码。它是用 Python 开发的。
  >
  > 它主要通过监控日志文件（比如`/var/log/auth.log`、`/var/log/apache/access.log`等）来生效。一旦发现恶意攻击的登录请求，它会封锁对方的 IP 地址，使得对方无法再发起请求。
  >
  > Fail2Ban 可以防止有人反复尝试 SSH 密码登录，但是如果 SSH 采用的是密钥登录，禁止了密码登录，就不需要 Fail2Ban 来保护。
  >
  > Fail2Ban 的安装命令如下。
  >
  > ```
  > # ubuntu & Debian
  > $ sudo apt install fail2ban
  > 
  > # Fedora
  > $ sudo dnf install epel-release
  > $ sudo dnf install fail2ban
  > 
  > # Centos & Red hat
  > $ yum install fail2ban
  > ```
  >
  > 安装后，使用下面的命令查看 Fail2Ban 的状态。
  >
  > ```
  > $ systemctl status fail2ban.service
  > ```
  >
  > 如果没有启动，就启动 Fail2Ban。
  >
  > ```
  > $ sudo systemctl start fail2ban
  > ```
  >
  > 重新启动 Fail2Ban。
  >
  > ```
  > $ sudo systemctl restart fail2ban
  > ```
  >
  > 设置 Fail2Ban 重启后自动运行。
  >
  > ```bash
  > $ sudo systemctl enable fail2ban
  > ```

- #### `fail2ban-client`

  > Fail2Ban 自带一个客户端 fail2ban-client，用来操作 Fail2Ban。
  >
  > ```
  > $ fail2ban-client
  > ```
  >
  > 上面的命令会输出 fail2ban-client 所有的用法。
  >
  > 下面的命令查看激活的监控目标。
  >
  > ```
  > $ fail2ban-client status
  > 
  > Status
  > |- Number of jail:	1
  > `- Jail list:	sshd
  > ```
  >
  > 下面的命令查看某个监控目标（这里是 sshd）的运行情况。
  >
  > ```
  > $ sudo fail2ban-client status sshd
  > 
  > Status for the jail: sshd
  > |- Filter
  > |  |- Currently failed: 1
  > |  |- Total failed:     9
  > |  `- Journal matches:  _SYSTEMD_UNIT=sshd.service + _COMM=sshd
  > `- Actions
  > |- Currently banned: 1
  > |- Total banned:     1
  > `- Banned IP list:   0.0.0.0
  > ```
  >
  > 下面的命令输出一个简要的版本，包括所有监控目标被封的 IP 地址。
  >
  > ```
  > $ sudo fail2ban-client banned
  > [{'sshd': ['192.168.100.50']}, {'apache-auth': []}]
  > ```
  >
  > 下面的命令可以解封某个 IP 地址。
  >
  > ```bash
  > $ sudo fail2ban-client set sshd unbanip 192.168.1.69
  > ```

- #### 配置

  - ##### 主配置文件

    > Fail2Ban 主配置文件是在`/etc/fail2ban/fail2ban.conf`，可以新建一份副本`/etc/fail2ban/fail2ban.local`，修改都针对副本。
    >
    > ```
    > $ sudo cp /etc/fail2ban/fail2ban.conf /etc/fail2ban/fail2ban.local
    > ```
    >
    > 下面是设置 Fail2Ban 的日志位置。
    >
    > ```
    > [Definition]
    > logtarget = /var/log/fail2ban/fail2ban.log
    > ```
    >
    > 修改配置以后，需要重新启动`fail2ban.service`，让其生效。

  - ##### 封禁配置

    > Fail2Ban 封禁行为的配置文件是`/etc/fail2ban/jail.conf`。为了便于修改，可以把它复制一份`/etc/fail2ban/jail.local`，后面的修改都针对`jail.local`这个文件。
    >
    > ```
    > $ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
    > ```
    >
    > 你也可以在目录`/etc/fail2ban/jail.d`里面，新建单独的子配置文件，比如`/etc/fail2ban/jail.d/sshd.local`。
    >
    > 同样地，修改配置以后，需要重新启动`fail2ban.service`，让其生效。
    >
    > 配置文件里面，`[DEFAULT]`标题行表示对于所有封禁目标生效。举例来说，如果封禁时间修改为1天，`/etc/fail2ban/jail.local`里面可以写成：
    >
    > ```
    > [DEFAULT]
    > bantime = 1d
    > ```
    >
    > 如果某人被封时，对站长发送邮件通知，可以如下设置。
    >
    > ```
    > [DEFAULT]
    > destemail = yourname@example.com
    > sender = yourname@example.com
    > 
    > # to ban & send an e-mail with whois report to the destemail.
    > action = %(action_mw)s
    > 
    > # same as action_mw but also send relevant log lines
    > #action = %(action_mwl)s
    > ```
    >
    > 如果配置写在其他标题行下，就表示只对该封禁目标生效，比如写在`[sshd]`下面，就表示只对 sshd 生效。
    >
    > 默认情况下，Fail2Ban 对各种服务都是关闭的，如果要针对某一项服务开启，需要在配置文件里面声明。
    >
    > ```
    > [sshd]
    > enabled = true
    > ```
    >
    > 上面声明表示，Fail2Ban 对 sshd 开启。

  - ##### 配置项

    > 下面是配置文件`jail.local`的配置项含义，所有配置项的格式都是`key=value`。
    >
    > （1）bantime
    >
    > 封禁的时间长度，单位`m`表示分钟，`d`表示天，如果不写单位，则表示秒。Fail2Ban 默认封禁10分钟（10m 或 600）。
    >
    > ```
    > [DEFAULT]
    > bantime = 10m
    > ```
    >
    > （2）findtime
    >
    > 登录失败计算的时间长度，单位`m`表示分钟，`d`表示天，如果不写单位，则表示秒。Fail2Ban 默认封禁 10 分钟内登录 5 次失败的客户端。
    >
    > ```
    > [DEFAULT]
    > 
    > findtime = 10m
    > maxretry = 5
    > ```
    >
    > （3）maxretry
    >
    > 尝试登录的最大失败次数。
    >
    > （4）destemail
    >
    > 接受通知的邮件地址。
    >
    > ```
    > [DEFAULT]
    > destemail = root@localhost
    > sender = root@<fq-hostname>
    > mta = sendmail
    > ```
    >
    > （5）sendername
    >
    > 通知邮件的“发件人”字段的值。
    >
    > （6）mta
    >
    > 发送邮件的邮件服务，默认是`sendmail`。
    >
    > （7）action
    >
    > 封禁时采取的动作。
    >
    > ```
    > [DEFAULT]
    > action = $(action_)s
    > ```
    >
    > 上面的`action_`是默认动作，表示拒绝封禁对象的流量，直到封禁期结束。
    >
    > 下面是 Fail2Ban 提供的一些其他动作。
    >
    > ```
    > # ban & send an e-mail with whois report to the destemail.
    > action_mw = %(action_)s
    >          %(mta)s-whois[sender="%(sender)s", dest="%(destemail)s", protocol="%(protocol)s", chain="%(chain)s"]
    > 
    > # ban & send an e-mail with whois report and relevant log lines
    > # to the destemail.
    > action_mwl = %(action_)s
    >           %(mta)s-whois-lines[sender="%(sender)s", dest="%(destemail)s", logpath="%(logpath)s", chain="%(chain)s"]
    > 
    > # See the IMPORTANT note in action.d/xarf-login-attack for when to use this action
    > #
    > # ban & send a xarf e-mail to abuse contact of IP address and include relevant log lines
    > # to the destemail.
    > action_xarf = %(action_)s
    >           xarf-login-attack[service=%(__name__)s, sender="%(sender)s", logpath="%(logpath)s", port="%(port)s"]
    > 
    > # ban IP on CloudFlare & send an e-mail with whois report and relevant log lines
    > # to the destemail.
    > action_cf_mwl = cloudflare[cfuser="%(cfemail)s", cftoken="%(cfapikey)s"]
    >              %(mta)s-whois-lines[sender="%(sender)s", dest="%(destemail)s", logpath="%(logpath)s", chain="%(chain)s"]
    > ```
    >
    > （8）ignoreip
    >
    > Fail2Ban 可以忽视的可信 IP 地址。多个 IP 地址之间使用空格分隔。
    >
    > ```
    > ignoreip = 127.0.0.1/8 192.168.1.10 192.168.1.20
    > ```
    >
    > （9）port
    >
    > 指定要监控的端口。可以设为任何端口号或服务名称，比如`ssh`、`22`、`2200`等。

  - ##### ssh 配置

    > 下面是 sshd 的设置范例。
    >
    > ```
    > [sshd]
    > enabled   = true
    > port = ssh
    > filter    = sshd
    > banaction = iptables
    > backend   = systemd
    > maxretry  = 5
    > findtime  = 1d
    > bantime   = 2w
    > ignoreip  = 127.0.0.1/8
    > ```
    >
    > 首先需要注意，为了让 Fail2Ban 能够完整发挥作用，最好在`/etc/ssh/sshd_config`里面设置`LogLevel VERBOSE`，保证日志有足够的信息。

------

