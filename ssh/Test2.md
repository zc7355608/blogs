## SSH 服务器

- #### 简介

  > SSH 的架构是服务器/客户端模式，两端运行的软件是不一样的。OpenSSH 的客户端软件是 ssh，服务器软件是 sshd。本章介绍 sshd 的各种知识。
  >
  > 如果没有安装 sshd，可以用下面的命令安装。
  >
  > ```
  > # Debian
  > $ sudo aptitude install openssh-server
  > 
  > # Red Hat
  > $ sudo yum install openssh-server
  > ```
  >
  > 一般来说，sshd 安装后会跟着系统一起启动。如果当前 sshd 没有启动，可以用下面的命令启动。
  >
  > ```
  > $ sshd
  > ```
  >
  > 上面的命令运行后，如果提示“sshd re-exec requires execution with an absolute path”，就需要使用绝对路径来启动。这是为了防止有人出于各种目的，放置同名软件在`$PATH`变量指向的目录中，代替真正的 sshd。
  >
  > ```
  > # Centos、Ubuntu、OS X
  > $ /usr/sbin/sshd
  > ```
  >
  > 上面的命令运行以后，sshd 自动进入后台，所以命令后面不需要加上`&`。
  >
  > 除了直接运行可执行文件，也可以通过 Systemd 启动 sshd。
  >
  > ```
  > # 启动
  > $ sudo systemctl start sshd.service
  > 
  > # 停止
  > $ sudo systemctl stop sshd.service
  > 
  > # 重启
  > $ sudo systemctl restart sshd.service
  > ```
  >
  > 下面的命令让 sshd 在计算机下次启动时自动运行。
  >
  > ```bash
  > $ sudo systemctl enable sshd.service
  > ```

- #### sshd 配置文件

  > sshd 的配置文件在`/etc/ssh`目录，主配置文件是`sshd_config`，此外还有一些安装时生成的密钥。
  >
  > - `/etc/ssh/sshd_config`：配置文件
  > - `/etc/ssh/ssh_host_ecdsa_key`：ECDSA 私钥。
  > - `/etc/ssh/ssh_host_ecdsa_key.pub`：ECDSA 公钥。
  > - `/etc/ssh/ssh_host_key`：用于 SSH 1 协议版本的 RSA 私钥。
  > - `/etc/ssh/ssh_host_key.pub`：用于 SSH 1 协议版本的 RSA 公钥。
  > - `/etc/ssh/ssh_host_rsa_key`：用于 SSH 2 协议版本的 RSA 私钥。
  > - `/etc/ssh/ssh_host_rsa_key.pub`：用于 SSH 2 协议版本的 RSA 公钥。
  > - `/etc/pam.d/sshd`：PAM 配置文件。
  >
  > 注意，如果重装 sshd，上面这些密钥都会重新生成，导致客户端重新连接 ssh 服务器时，会跳出警告，拒绝连接。为了避免这种情况，可以在重装 sshd 时，先备份`/etc/ssh`目录，重装后再恢复这个目录。
  >
  > 配置文件`sshd_config`的格式是，每个命令占据一行。每行都是配置项和对应的值，配置项的大小写不敏感，与值之间使用空格分隔。
  >
  > ```
  > Port 2034
  > ```
  >
  > 上面的配置命令指定，配置项`Port`的值是`2034`。`Port`写成`port`也可。
  >
  > 配置文件还有另一种格式，就是配置项与值之间有一个等号，等号前后的空格可选。
  >
  > ```
  > Port = 2034
  > ```
  >
  > 配置文件里面，`#`开头的行表示注释。
  >
  > ```
  > # 这是一行注释
  > ```
  >
  > 注意，注释只能放在一行的开头，不能放在一行的结尾。
  >
  > ```
  > Port 2034 # 此处不允许注释
  > ```
  >
  > 上面的写法是错误的。
  >
  > 另外，空行等同于注释。
  >
  > sshd 启动时会自动读取默认的配置文件。如果希望使用其他的配置文件，可以用 sshd 命令的`-f`参数指定。
  >
  > ```
  > $ sshd -f /usr/local/ssh/my_config
  > ```
  >
  > 上面的命令指定 sshd 使用另一个配置文件`my_config`。
  >
  > 修改配置文件以后，可以用 sshd 命令的`-t`（test）检查有没有语法错误。
  >
  > ```
  > $ sshd -t
  > ```
  >
  > 配置文件修改以后，并不会自动生效，必须重新启动 sshd。
  >
  > ```bash
  > $ sudo systemctl restart sshd.service
  > ```

- #### sshd 密钥

  > sshd 有自己的一对或多对密钥。它使用密钥向客户端证明自己的身份。所有密钥都是公钥和私钥成对出现，公钥的文件名一般是私钥文件名加上后缀`.pub`。
  >
  > DSA 格式的密钥文件默认为`/etc/ssh/ssh_host_dsa_key`（公钥为`ssh_host_dsa_key.pub`），RSA 格式的密钥为`/etc/ssh/ssh_host_rsa_key`（公钥为`ssh_host_rsa_key.pub`）。如果需要支持 SSH 1 协议，则必须有密钥`/etc/ssh/ssh_host_key`。
  >
  > 如果密钥不是默认文件，那么可以通过配置文件`sshd_config`的`HostKey`配置项指定。默认密钥的`HostKey`设置如下。
  >
  > ```
  > # HostKey for protocol version 1
  > # HostKey /etc/ssh/ssh_host_key
  > 
  > # HostKeys for protocol version 2
  > # HostKey /etc/ssh/ssh_host_rsa_key
  > # HostKey /etc/ssh/ssh_host_dsa_ke
  > ```
  >
  > 上面命令前面的`#`表示这些行都是注释，因为这是默认值，有没有这几行都一样。
  >
  > 如果要修改密钥，就要去掉行首的`#`，指定其他密钥。
  >
  > ```bash
  > HostKey /usr/local/ssh/my_dsa_key
  > HostKey /usr/local/ssh/my_rsa_key
  > HostKey /usr/local/ssh/my_old_ssh1_key
  > ```

- #### sshd 配置项

  > 以下是`/etc/ssh/sshd_config`文件里面的配置项。
  >
  > **AcceptEnv**
  >
  > `AcceptEnv`指定允许接受客户端通过`SendEnv`命令发来的哪些环境变量，即允许客户端设置服务器的环境变量清单，变量名之间使用空格分隔（`AcceptEnv PATH TERM`）。
  >
  > **AllowGroups**
  >
  > `AllowGroups`指定允许登录的用户组（`AllowGroups groupName`，多个组之间用空格分隔。如果不使用该项，则允许所有用户组登录。
  >
  > **AllowUsers**
  >
  > `AllowUsers`指定允许登录的用户，用户名之间使用空格分隔（`AllowUsers user1 user2`），也可以使用多行`AllowUsers`命令指定，用户名支持使用通配符。如果不使用该项，则允许所有用户登录。该项也可以使用`用户名@域名`的格式（比如`AllowUsers jones@example.com`）。
  >
  > **AllowTcpForwarding**
  >
  > `AllowTcpForwarding`指定是否允许端口转发，默认值为`yes`（`AllowTcpForwarding yes`），`local`表示只允许本地端口转发，`remote`表示只允许远程端口转发。
  >
  > **AuthorizedKeysFile**
  >
  > `AuthorizedKeysFile`指定储存用户公钥的目录，默认是用户主目录的`ssh/authorized_keys`目录（`AuthorizedKeysFile .ssh/authorized_keys`）。
  >
  > **Banner**
  >
  > `Banner`指定用户登录后，sshd 向其展示的信息文件（`Banner /usr/local/etc/warning.txt`），默认不展示任何内容。
  >
  > **ChallengeResponseAuthentication**
  >
  > `ChallengeResponseAuthentication`指定是否使用“键盘交互”身份验证方案，默认值为`yes`（`ChallengeResponseAuthentication yes`）。
  >
  > 从理论上讲，“键盘交互”身份验证方案可以向用户询问多重问题，但是实践中，通常仅询问用户密码。如果要完全禁用基于密码的身份验证，请将`PasswordAuthentication`和`ChallengeResponseAuthentication`都设置为`no`。
  >
  > **Ciphers**
  >
  > `Ciphers`指定 sshd 可以接受的加密算法（`Ciphers 3des-cbc`），多个算法之间使用逗号分隔。
  >
  > **ClientAliveCountMax**
  >
  > `ClientAliveCountMax`指定建立连接后，客户端失去响应时（超过指定时间，没有收到任何消息），服务器尝试连接（发送消息）的次数（`ClientAliveCountMax 8`）。
  >
  > **ClientAliveInterval**
  >
  > `ClientAliveInterval`指定允许客户端发呆的时间，单位为秒（`ClientAliveInterval 180`）。超过这个时间，服务器将发送消息以请求客户端的响应。如果为`0`，表示不向客户端发送消息，即连接不会自动断开。
  >
  > **Compression**
  >
  > `Compression`指定客户端与服务器之间的数据传输是否压缩。默认值为`yes`（`Compression yes`）
  >
  > **DenyGroups**
  >
  > `DenyGroups`指定不允许登录的用户组（`DenyGroups groupName`）。
  >
  > **DenyUsers**
  >
  > `DenyUsers`指定不允许登录的用户（`DenyUsers user1`），用户名之间使用空格分隔，也可以使用多行`DenyUsers`命令指定。
  >
  > **FascistLogging**
  >
  > SSH 1 版本专用，指定日志输出全部 Debug 信息（`FascistLogging yes`）。
  >
  > **HostKey**
  >
  > `HostKey`指定 sshd 服务器的密钥，详见前文。
  >
  > **KeyRegenerationInterval**
  >
  > `KeyRegenerationInterval`指定 SSH 1 版本的密钥重新生成时间间隔，单位为秒，默认是3600秒（`KeyRegenerationInterval 3600`）。
  >
  > **ListenAddress**
  >
  > `ListenAddress`指定 sshd 监听的本机 IP 地址，即 sshd 启用的 IP 地址，默认是 0.0.0.0（`ListenAddress 0.0.0.0`）表示在本机所有网络接口启用。可以改成只在某个网络接口启用（比如`ListenAddress 192.168.10.23`），也可以指定某个域名启用（比如`ListenAddress server.example.com`）。
  >
  > 如果要监听多个指定的 IP 地址，可以使用多行`ListenAddress`命令。
  >
  > ```
  > ListenAddress 172.16.1.1
  > ListenAddress 192.168.0.1
  > ```
  >
  > **LoginGraceTime**
  >
  > `LoginGraceTime`指定允许客户端登录时发呆的最长时间，比如用户迟迟不输入密码，连接就会自动断开，单位为秒（`LoginGraceTime 60`）。如果设为`0`，就表示没有限制。
  >
  > **LogLevel**
  >
  > `LogLevel`指定日志的详细程度，可能的值依次为`QUIET`、`FATAL`、`ERROR`、`INFO`、`VERBOSE`、`DEBUG`、`DEBUG1`、`DEBUG2`、`DEBUG3`，默认为`INFO`（`LogLevel INFO`）。
  >
  > **MACs**
  >
  > `MACs`指定sshd 可以接受的数据校验算法（`MACs hmac-sha1`），多个算法之间使用逗号分隔。
  >
  > **MaxAuthTries**
  >
  > `MaxAuthTries`指定允许 SSH 登录的最大尝试次数（`MaxAuthTries 3`），如果密码输入错误达到指定次数，SSH 连接将关闭。
  >
  > **MaxStartups**
  >
  > `MaxStartups`指定允许同时并发的 SSH 连接数量（MaxStartups）。如果设为`0`，就表示没有限制。
  >
  > 这个属性也可以设为`A:B:C`的形式，比如`MaxStartups 10:50:20`，表示如果达到10个并发连接，后面的连接将有50%的概率被拒绝；如果达到20个并发连接，则后面的连接将100%被拒绝。
  >
  > **PasswordAuthentication**
  >
  > `PasswordAuthentication`指定是否允许密码登录，默认值为`yes`（`PasswordAuthentication yes`），建议改成`no`（禁止密码登录，只允许密钥登录）。
  >
  > **PermitEmptyPasswords**
  >
  > `PermitEmptyPasswords`指定是否允许空密码登录，即用户的密码是否可以为空，默认为`yes`（`PermitEmptyPasswords yes`），建议改成`no`（禁止无密码登录）。
  >
  > **PermitRootLogin**
  >
  > `PermitRootLogin`指定是否允许根用户登录，默认为`yes`（`PermitRootLogin yes`），建议改成`no`（禁止根用户登录）。
  >
  > 还有一种写法是写成`prohibit-password`，表示 root 用户不能用密码登录，但是可以用密钥登录。
  >
  > ```
  > PermitRootLogin prohibit-password
  > ```
  >
  > **PermitUserEnvironment**
  >
  > `PermitUserEnvironment`指定是否允许 sshd 加载客户端的`~/.ssh/environment`文件和`~/.ssh/authorized_keys`文件里面的`environment= options`环境变量设置。默认值为`no`（`PermitUserEnvironment no`）。
  >
  > **Port**
  >
  > `Port`指定 sshd 监听的端口，即客户端连接的端口，默认是22（`Port 22`）。出于安全考虑，可以改掉这个端口（比如`Port 8822`）。
  >
  > 配置文件可以使用多个`Port`命令，同时监听多个端口。
  >
  > ```
  > Port 22
  > Port 80
  > Port 443
  > Port 8080
  > ```
  >
  > 上面的示例表示同时监听4个端口。
  >
  > **PrintMotd**
  >
  > `PrintMotd`指定用户登录后，是否向其展示系统的 motd（Message of the day）的信息文件`/etc/motd`。该文件用于通知所有用户一些重要事项，比如系统维护时间、安全问题等等。默认值为`yes`（`PrintMotd yes`），由于 Shell 一般会展示这个信息文件，所以这里可以改为`no`。
  >
  > **PrintLastLog**
  >
  > `PrintLastLog`指定是否打印上一次用户登录时间，默认值为`yes`（`PrintLastLog yes`）。
  >
  > **Protocol**
  >
  > `Protocol`指定 sshd 使用的协议。`Protocol 1`表示使用 SSH 1 协议，建议改成`Protocol 2`（使用 SSH 2 协议）。`Protocol 2,1`表示同时支持两个版本的协议。
  >
  > **PubkeyAuthentication**
  >
  > `PubkeyAuthentication`指定是否允许公钥登录，默认值为`yes`（`PubkeyAuthentication yes`）。
  >
  > **QuietMode**
  >
  > SSH 1 版本专用，指定日志只输出致命的错误信息（`QuietMode yes`）。
  >
  > **RSAAuthentication**
  >
  > `RSAAuthentication`指定允许 RSA 认证，默认值为`yes`（`RSAAuthentication yes`）。
  >
  > **ServerKeyBits**
  >
  > `ServerKeyBits`指定 SSH 1 版本的密钥重新生成时的位数，默认是768（`ServerKeyBits 768`）。
  >
  > **StrictModes**
  >
  > `StrictModes`指定 sshd 是否检查用户的一些重要文件和目录的权限。默认为`yes`（`StrictModes yes`），即对于用户的 SSH 配置文件、密钥文件和所在目录，SSH 要求拥有者必须是根用户或用户本人，用户组和其他人的写权限必须关闭。
  >
  > **SyslogFacility**
  >
  > `SyslogFacility`指定 Syslog 如何处理 sshd 的日志，默认是 Auth（`SyslogFacility AUTH`）。
  >
  > **TCPKeepAlive**
  >
  > `TCPKeepAlive`指定系统是否应向客户端发送 TCP keepalive 消息（`TCPKeepAlive yes`）。
  >
  > **UseDNS**
  >
  > `UseDNS`指定用户 SSH 登录一个域名时，服务器是否使用 DNS，确认该域名对应的 IP 地址包含本机（`UseDNS yes`）。打开该选项意义不大，而且如果 DNS 更新不及时，还有可能误判，建议关闭。
  >
  > **UseLogin**
  >
  > `UseLogin`指定用户认证内部是否使用`/usr/bin/login`替代 SSH 工具，默认为`no`（`UseLogin no`）。
  >
  > **UserPrivilegeSeparation**
  >
  > `UserPrivilegeSeparation`指定用户认证通过以后，使用另一个子线程处理用户权限相关的操作，这样有利于提高安全性。默认值为`yes`（`UsePrivilegeSeparation yes`）。
  >
  > **VerboseMode**
  >
  > SSH 2 版本专用，指定日志输出详细的 Debug 信息（`VerboseMode yes`）。
  >
  > **X11Forwarding**
  >
  > `X11Forwarding`指定是否打开 X window 的转发，默认值为 no（`X11Forwarding no`）。
  >
  > 修改配置文件以后，可以使用下面的命令验证，配置文件是否有语法错误。
  >
  > ```
  > $ sshd -t
  > ```
  >
  > 新的配置文件生效，必须重启 sshd。
  >
  > ```bash
  > $ sudo systemctl restart sshd
  > ```

- #### sshd 的命令行配置项

  > sshd 命令有一些配置项。这些配置项在调用时指定，可以覆盖配置文件的设置。
  >
  > （1）`-d`
  >
  > `-d`参数用于显示 debug 信息。
  >
  > ```
  > $ sshd -d
  > ```
  >
  > （2）`-D`
  >
  > `-D`参数指定 sshd 不作为后台守护进程运行。
  >
  > ```
  > $ sshd -D
  > ```
  >
  > （3）`-e`
  >
  > `-e`参数将 sshd 写入系统日志 syslog 的内容导向标准错误（standard error）。
  >
  > （4）`-f`
  >
  > `-f`参数指定配置文件的位置。
  >
  > （5）`-h`
  >
  > `-h`参数用于指定密钥。
  >
  > ```
  > $ sshd -h /usr/local/ssh/my_rsa_key
  > ```
  >
  > （6）`-o`
  >
  > `-o`参数指定配置文件的一个配置项和对应的值。
  >
  > ```
  > $ sshd -o "Port 2034"
  > ```
  >
  > 配置项和对应值之间，可以使用等号。
  >
  > ```
  > $ sshd -o "Port = 2034"
  > ```
  >
  > 如果省略等号前后的空格，也可以不使用引号。
  >
  > ```
  > $ sshd -o Port=2034
  > ```
  >
  > `-o`参数可以多个一起使用，用来指定多个配置关键字。
  >
  > （7）`-p`
  >
  > `-p`参数指定 sshd 的服务端口。
  >
  > ```
  > $ sshd -p 2034
  > ```
  >
  > 上面命令指定 sshd 在`2034`端口启动。
  >
  > `-p`参数可以指定多个端口。
  >
  > ```
  > $ sshd -p 2222 -p 3333
  > ```
  >
  > （8）`-t`
  >
  > `-t`参数检查配置文件的语法是否正确。

## 日志

> SSH 在服务器端可以生成日志，记录登录当前服务器的情况。
>
> SSH 日志是写在系统日志当中的，查看的时候需要从系统日志里面找到跟 SSH 相关的记录。

- #### `journalctl` 命令

  > 如果系统使用 Systemd，可以使用`journalctl`命令查看日志。
  >
  > ```
  > $ journalctl -u ssh
  > 
  > Mar 25 20:25:36 web0 sshd[14144]: Accepted publickey for ubuntu from 10.103.160.144 port 59200 ssh2: RSA SHA256:l/zFNib1vJ+64nxLB4N9KaVhBEMf8arbWGxHQg01SW8
  > Mar 25 20:25:36 web0 sshd[14144]: pam_unix(sshd:session): session opened for user ubuntu by (uid=0)
  > Mar 25 20:39:12 web0 sshd[14885]: pam_unix(sshd:session): session closed for user ubuntu
  > ...
  > ```
  >
  > 上面示例中，返回的日志每一行就是一次登录尝试，按照从早到晚的顺序，其中包含了登录失败的尝试。`-u`参数是 Unit 单元的意思，`-u ssh`就是查看 SSH 单元，有的发行版需要写成`-u sshd`。
  >
  > `-b0`参数可以查看自从上次登录后的日志。
  >
  > ```
  > $ journalctl -t ssh -b0
  > ```
  >
  > `-r`参数表示逆序输出，最新的在前面。
  >
  > ```
  > $ journalctl -t ssh -b0 -r
  > ```
  >
  > `since`和`until`参数可以指定日志的时间范围。
  >
  > ```
  > $ journalctl -u ssh --since yesterday # 查看昨天的日志
  > $ journalctl -u ssh --since -3d --until -2d # 查看三天前的日志
  > $ journalctl -u ssh --since -1h # 查看上个小时的日志
  > $ journalctl -u ssh --until "2022-03-12 07:00:00" # 查看截至到某个时间点的日志
  > ```
  >
  > 下面的命令查看实时日志。
  >
  > ```bash
  > $ journalctl -fu ssh
  > ```

- #### 其他命令

  > 如果系统没有使用 Systemd，可以在`/var/log/auth.log`文件中找到 sshd 的日志。
  >
  > ```
  > $ sudo grep sshd /var/log/auth.log
  > ```
  >
  > 下面的命令查看最后 500 行里面的 sshd 条目。
  >
  > ```
  > $ sudo tail -n 500 /var/log/auth.log | grep sshd
  > ```
  >
  > `-f`参数可以实时跟踪日志。
  >
  > ```
  > $ sudo tail -f -n 500 /var/log/auth.log | grep sshd
  > ```
  >
  > 如果只是想看谁登录了系统，而不是深入查看所有细节，可以使用`lastlog`命令。
  >
  > ```bash
  > $ lastlog
  > ```

- #### 日志设置

  > sshd 的配置文件`/etc/ssh/sshd_config`，可以调整日志级别。
  >
  > ```
  > LogLevel VERBOSE
  > ```
  >
  > 如果为了调试，可以将日志调整为 DEBUG。
  >
  > ```tex
  >  LogLevel DEBUG
  > ```

## 端口转发

- #### 简介

  > SSH 除了登录服务器，还有一大用途，就是作为加密通信的中介，充当两台服务器之间的通信加密跳板，使得原本不加密的通信变成加密通信。这个功能称为端口转发（port forwarding），又称 SSH 隧道（tunnel）。
  >
  > 端口转发有两个主要作用：
  >
  > （1）将不加密的数据放在 SSH 安全连接里面传输，使得原本不安全的网络服务增加了安全性，比如通过端口转发访问 Telnet、FTP 等明文服务，数据传输就都会加密。
  >
  > （2）作为数据通信的加密跳板，绕过网络防火墙。
  >
  > 端口转发有三种使用方法：动态转发，本地转发，远程转发。下面逐一介绍。

- #### 动态转发

  > 动态转发指的是，本机与 SSH 服务器之间创建了一个加密连接，然后本机内部针对某个端口的通信，都通过这个加密连接转发。它的一个使用场景就是，访问所有外部网站，都通过 SSH 转发。
  >
  > 动态转发需要把本地端口绑定到 SSH 服务器。至于 SSH 服务器要去访问哪一个网站，完全是动态的，取决于原始通信，所以叫做动态转发。
  >
  > ```
  > $ ssh -D local-port tunnel-host -N
  > ```
  >
  > 上面命令中，`-D`表示动态转发，`local-port`是本地端口，`tunnel-host`是 SSH 服务器，`-N`表示这个 SSH 连接只进行端口转发，不登录远程 Shell，不能执行远程命令，只能充当隧道。
  >
  > 举例来说，如果本地端口是`2121`，那么动态转发的命令就是下面这样。
  >
  > ```
  > $ ssh -D 2121 tunnel-host -N
  > ```
  >
  > 注意，这种转发采用了 SOCKS5 协议。访问外部网站时，需要把 HTTP 请求转成 SOCKS5 协议，才能把本地端口的请求转发出去。
  >
  > 下面是 SSH 隧道建立后的一个使用实例。
  >
  > ```
  > $ curl -x socks5://localhost:2121 http://www.example.com
  > ```
  >
  > 上面命令中，curl 的`-x`参数指定代理服务器，即通过 SOCKS5 协议的本地`2121`端口，访问`http://www.example.com`。
  >
  > 如果经常使用动态转发，可以将设置写入 SSH 客户端的用户个人配置文件（`~/.ssh/config`）。
  >
  > ```tex
  > DynamicForward tunnel-host:local-port
  > ```

- #### 本地转发

  > 本地转发（local forwarding）指的是，创建一个本地端口，将发往该端口的所有通信都通过 SSH 服务器，转发到指定的远程服务器的端口。这种情况下，SSH 服务器只是一个作为跳板的中介，用于连接本地计算机无法直接连接的远程服务器。本地转发是在本地计算机建立的转发规则。
  >
  > 它的语法如下，其中会指定本地端口（local-port）、SSH 服务器（tunnel-host）、远程服务器（target-host）和远程端口（target-port）。
  >
  > ```
  > $ ssh -L -N -f local-port:target-host:target-port tunnel-host
  > ```
  >
  > 上面命令中，有三个配置参数。
  >
  > - `-L`：转发本地端口。
  > - `-N`：不发送任何命令，只用来建立连接。没有这个参数，会在 SSH 服务器打开一个 Shell。
  > - `-f`：将 SSH 连接放到后台。没有这个参数，暂时不用 SSH 连接时，终端会失去响应。
  >
  > 举例来说，现在有一台 SSH 服务器`tunnel-host`，我们想要通过这台机器，在本地`2121`端口与目标网站`www.example.com`的80端口之间建立 SSH 隧道，就可以写成下面这样。
  >
  > ```
  > $ ssh -L 2121:www.example.com:80 tunnel-host -N
  > ```
  >
  > 然后，访问本机的`2121`端口，就是访问`www.example.com`的80端口。
  >
  > ```
  > $ curl http://localhost:2121
  > ```
  >
  > 注意，本地端口转发采用 HTTP 协议，不用转成 SOCKS5 协议。
  >
  > 另一个例子是加密访问邮件获取协议 POP3。
  >
  > ```
  > $ ssh -L 1100:mail.example.com:110 mail.example.com
  > ```
  >
  > 上面命令将本机的1100端口，绑定邮件服务器`mail.example.com`的110端口（POP3 协议的默认端口）。端口转发建立以后，POP3 邮件客户端只需要访问本机的1100端口，请求就会通过 SSH 服务器（这里是`mail.example.com`），自动转发到`mail.example.com`的110端口。
  >
  > 上面这种情况有一个前提条件，就是`mail.example.com`必须运行 SSH 服务器。否则，就必须通过另一台 SSH 服务器中介，执行的命令要改成下面这样。
  >
  > ```
  > $ ssh -L 1100:mail.example.com:110 other.example.com
  > ```
  >
  > 上面命令中，本机的1100端口还是绑定`mail.example.com`的110端口，但是由于`mail.example.com`没有运行 SSH 服务器，所以必须通过`other.example.com`中介。本机的 POP3 请求通过1100端口，先发给`other.example.com`的22端口（sshd 默认端口），再由后者转给`mail.example.com`，得到数据以后再原路返回。
  >
  > 注意，采用上面的中介方式，只有本机到`other.example.com`的这一段是加密的，`other.example.com`到`mail.example.com`的这一段并不加密。
  >
  > 这个命令最好加上`-N`参数，表示不在 SSH 跳板机执行远程命令，让 SSH 只充当隧道。另外还有一个`-f`参数表示 SSH 连接在后台运行。
  >
  > 如果经常使用本地转发，可以将设置写入 SSH 客户端的用户个人配置文件（`~/.ssh/config`）。
  >
  > ```tex
  > Host test.example.com
  > LocalForward client-IP:client-port server-IP:server-port
  > ```

- #### 远程转发

  > 远程转发指的是在远程 SSH 服务器建立的转发规则。
  >
  > 它跟本地转发正好反过来。建立本地计算机到远程 SSH 服务器的隧道以后，本地转发是通过本地计算机访问远程 SSH 服务器，而远程转发则是通过远程 SSH 服务器访问本地计算机。它的命令格式如下。
  >
  > ```
  > $ ssh -R remote-port:target-host:target-port -N remotehost
  > ```
  >
  > 上面命令中，`-R`参数表示远程端口转发，`remote-port`是远程 SSH 服务器的端口，`target-host`和`target-port`是目标服务器及其端口，`remotehost`是远程 SSH 服务器。
  >
  > 远程转发主要针对内网的情况。下面举两个例子。
  >
  > 第一个例子是内网某台服务器`localhost`在 80 端口开了一个服务，可以通过远程转发将这个 80 端口，映射到具有公网 IP 地址的`my.public.server`服务器的 8080 端口，使得访问`my.public.server:8080`这个地址，就可以访问到那台内网服务器的 80 端口。
  >
  > ```
  > $ ssh -R 8080:localhost:80 -N my.public.server
  > ```
  >
  > 上面命令是在内网`localhost`服务器上执行，建立从`localhost`到`my.public.server`的 SSH 隧道。运行以后，用户访问`my.public.server:8080`，就会自动映射到`localhost:80`。
  >
  > 第二个例子是本地计算机`local`在外网，SSH 跳板机和目标服务器`my.private.server`都在内网，必须通过 SSH 跳板机才能访问目标服务器。但是，本地计算机`local`无法访问内网之中的 SSH 跳板机，而 SSH 跳板机可以访问本机计算机。
  >
  > 由于本机无法访问内网 SSH 跳板机，就无法从外网发起 SSH 隧道，建立端口转发。必须反过来，从 SSH 跳板机发起隧道，建立端口转发，这时就形成了远程端口转发。跳板机执行下面的命令，绑定本地计算机`local`的`2121`端口，去访问`my.private.server:80`。
  >
  > ```
  > $ ssh -R 2121:my.private.server:80 -N local
  > ```
  >
  > 上面命令是在 SSH 跳板机上执行的，建立跳板机到`local`的隧道，并且这条隧道的出口映射到`my.private.server:80`。
  >
  > 显然，远程转发要求本地计算机`local`也安装了 SSH 服务器，这样才能接受 SSH 跳板机的远程登录。
  >
  > 执行上面的命令以后，跳板机到`local`的隧道已经建立了。然后，就可以从本地计算机访问目标服务器了，即在本机执行下面的命令。
  >
  > ```
  > $ curl http://localhost:2121
  > ```
  >
  > 本机执行上面的命令以后，就会输出服务器`my.private.server`的 80 端口返回的内容。
  >
  > 如果经常执行远程端口转发，可以将设置写入 SSH 客户端的用户个人配置文件（`~/.ssh/config`）。
  >
  > ```
  > Host remote-forward
  >   HostName test.example.com
  >   RemoteForward remote-port target-host:target-port
  > ```
  >
  > 完成上面的设置后，执行下面的命令就会建立远程转发。
  >
  > ```bash
  > $ ssh -N remote-forward
  > 
  > # 等同于
  > $ ssh -R remote-port:target-host:target-port -N test.example.com
  > ```

- #### 实例

  > 下面看两个端口转发的实例。

  - ##### 简易 VPN

    > VPN 用来在外网与内网之间建立一条加密通道。内网的服务器不能从外网直接访问，必须通过一个跳板机，如果本机可以访问跳板机，就可以使用 SSH 本地转发，简单实现一个 VPN。
    >
    > ```
    > $ ssh -L 2080:corp-server:80 -L 2443:corp-server:443 tunnel-host -N
    > ```
    >
    > 上面命令通过 SSH 跳板机，将本机的`2080`端口绑定内网服务器的`80`端口，本机的`2443`端口绑定内网服务器的`443`端口。

  - ##### 两级跳板

    > 端口转发可以有多级，比如新建两个 SSH 隧道，第一个隧道转发给第二个隧道，第二个隧道才能访问目标服务器。
    >
    > 首先，在本机新建第一级隧道。
    >
    > ```
    > $ ssh -L 7999:localhost:2999 tunnel1-host
    > ```
    >
    > 上面命令在本地`7999`端口与`tunnel1-host`之间建立一条隧道，隧道的出口是`tunnel1-host`的`localhost:2999`，也就是`tunnel1-host`收到本机的请求以后，转发给自己的`2999`端口。
    >
    > 然后，在第一台跳板机（`tunnel1-host`）执行下面的命令，新建第二级隧道。
    >
    > ```
    > $ ssh -L 2999:target-host:7999 tunnel2-host -N
    > ```
    >
    > 上面命令将第一台跳板机`tunnel1-host`的`2999`端口，通过第二台跳板机`tunnel2-host`，连接到目标服务器`target-host`的`7999`端口。
    >
    > 最终效果就是，访问本机的`7999`端口，就会转发到`target-host`的`7999`端口。

## SSH 证书登录

> SSH 是服务器登录工具，一般情况下都采用密码登录或密钥登录。
>
> 但是，SSH 还有第三种登录方法，那就是证书登录。某些情况下，它是更合理、更安全的登录方法，本文就介绍这种登录方法。

- #### 非证书登录的缺点

  > 密码登录和密钥登录，都有各自的缺点。
  >
  > 密码登录需要输入服务器密码，这非常麻烦，也不安全，存在被暴力破解的风险。
  >
  > 密钥登录需要服务器保存用户的公钥，也需要用户保存服务器公钥的指纹。这对于多用户、多服务器的大型机构很不方便，如果有员工离职，需要将他的公钥从每台服务器删除。

- #### 证书登录是什么？

  > 证书登录就是为了解决上面的缺点而设计的。它引入了一个证书颁发机构（Certificate Authority，简称 CA），对信任的服务器颁发服务器证书，对信任的用户颁发用户证书。
  >
  > 登录时，用户和服务器不需要提前知道彼此的公钥，只需要交换各自的证书，验证是否可信即可。
  >
  > 证书登录的主要优点有两个：（1）用户和服务器不用交换公钥，这更容易管理，也具有更好的可扩展性。（2）证书可以设置到期时间，而公钥没有到期时间。针对不同的情况，可以设置有效期很短的证书，进一步提高安全性。

- #### 证书登录的流程

  > SSH 证书登录之前，如果还没有证书，需要生成证书。具体方法是：（1）用户和服务器都将自己的公钥，发给 CA；（2）CA 使用服务器公钥，生成服务器证书，发给服务器；（3）CA 使用用户的公钥，生成用户证书，发给用户。
  >
  > 有了证书以后，用户就可以登录服务器了。整个过程都是 SSH 自动处理，用户无感知。
  >
  > 第一步，用户登录服务器时，SSH 自动将用户证书发给服务器。
  >
  > 第二步，服务器检查用户证书是否有效，以及是否由可信的 CA 颁发。证实以后，就可以信任用户。
  >
  > 第三步，SSH 自动将服务器证书发给用户。
  >
  > 第四步，用户检查服务器证书是否有效，以及是否由信任的 CA 颁发。证实以后，就可以信任服务器。
  >
  > 第五步，双方建立连接，服务器允许用户登录。

- #### 生成 CA 的密钥

  > 证书登录的前提是，必须有一个 CA，而 CA 本质上就是一对密钥，跟其他密钥没有不同，CA 就用这对密钥去签发证书。
  >
  > 虽然 CA 可以用同一对密钥签发用户证书和服务器证书，但是出于安全性和灵活性，最好用不同的密钥分别签发。所以，CA 至少需要两对密钥，一对是签发用户证书的密钥，假设叫做`user_ca`，另一对是签发服务器证书的密钥，假设叫做`host_ca`。
  >
  > 使用下面的命令，生成`user_ca`。
  >
  > ```
  > # 生成 CA 签发用户证书的密钥
  > $ ssh-keygen -t rsa -b 4096 -f ~/.ssh/user_ca -C user_ca
  > ```
  >
  > 上面的命令会在`~/.ssh`目录生成一对密钥：`user_ca`（私钥）和`user_ca.pub`（公钥）。
  >
  > 这个命令的各个参数含义如下。
  >
  > - `-t rsa`：指定密钥算法 RSA。
  > - `-b 4096`：指定密钥的位数是4096位。安全性要求不高的场合，这个值可以小一点，但是不应小于1024。
  > - `-f ~/.ssh/user_ca`：指定生成密钥的位置和文件名。
  > - `-C user_ca`：指定密钥的识别字符串，相当于注释，可以随意设置。
  >
  > 使用下面的命令，生成`host_ca`。
  >
  > ```
  > # 生成 CA 签发服务器证书的密钥
  > $ ssh-keygen -t rsa -b 4096 -f host_ca -C host_ca
  > ```
  >
  > 上面的命令会在`~/.ssh`目录生成一对密钥：`host_ca`（私钥）和`host_ca.pub`（公钥）。
  >
  > 现在，`~/.ssh`目录应该至少有四把密钥。
  >
  > - `~/.ssh/user_ca`
  > - `~/.ssh/user_ca.pub`
  > - `~/.ssh/host_ca`
  > - `~/.ssh/host_ca.pub`

- #### CA 签发服务器证书

  > 有了 CA 以后，就可以签发服务器证书了。
  >
  > 签发证书，除了 CA 的密钥以外，还需要服务器的公钥。一般来说，SSH 服务器（通常是`sshd`）安装时，已经生成密钥`/etc/ssh/ssh_host_rsa_key`了。如果没有的话，可以用下面的命令生成。
  >
  > ```
  > $ sudo ssh-keygen -f /etc/ssh/ssh_host_rsa_key -b 4096 -t rsa
  > ```
  >
  > 上面命令会在`/etc/ssh`目录，生成`ssh_host_rsa_key`（私钥）和`ssh_host_rsa_key.pub`（公钥）。然后，需要把服务器公钥`ssh_host_rsa_key.pub`，复制或上传到 CA 所在的服务器。
  >
  > 上传以后，CA 就可以使用密钥`host_ca`为服务器的公钥`ssh_host_rsa_key.pub`签发服务器证书。
  >
  > ```
  > $ ssh-keygen -s host_ca -I host.example.com -h -n host.example.com -V +52w ssh_host_rsa_key.pub
  > ```
  >
  > 上面的命令会生成服务器证书`ssh_host_rsa_key-cert.pub`（服务器公钥名字加后缀`-cert`）。这个命令各个参数的含义如下。
  >
  > - `-s`：指定 CA 签发证书的密钥。
  > - `-I`：身份字符串，可以随便设置，相当于注释，方便区分证书，将来可以使用这个字符串撤销证书。
  > - `-h`：指定该证书是服务器证书，而不是用户证书。
  > - `-n host.example.com`：指定服务器的域名，表示证书仅对该域名有效。如果有多个域名，则使用逗号分隔。用户登录该域名服务器时，SSH 通过证书的这个值，分辨应该使用哪张证书发给用户，用来证明服务器的可信性。
  > - `-V +52w`：指定证书的有效期，这里为52周（一年）。默认情况下，证书是永远有效的。建议使用该参数指定有效期，并且有效期最好短一点，最长不超过52周。
  > - `ssh_host_rsa_key.pub`：服务器公钥。
  >
  > 生成证书以后，可以使用下面的命令，查看证书的细节。
  >
  > ```
  > $ ssh-keygen -L -f ssh_host_rsa_key-cert.pub
  > ```
  >
  > 最后，为证书设置权限。
  >
  > ```bash
  > $ chmod 600 ssh_host_rsa_key-cert.pub
  > ```

- #### CA 签发用户证书

  > 下面，再用 CA 签发用户证书。这时需要用户的公钥，如果没有的话，客户端可以用下面的命令生成一对密钥。
  >
  > ```
  > $ ssh-keygen -f ~/.ssh/user_key -b 4096 -t rsa
  > ```
  >
  > 上面命令会在`~/.ssh`目录，生成`user_key`（私钥）和`user_key.pub`（公钥）。
  >
  > 然后，将用户公钥`user_key.pub`，上传或复制到 CA 服务器。接下来，就可以使用 CA 的密钥`user_ca`为用户公钥`user_key.pub`签发用户证书。
  >
  > ```
  > $ ssh-keygen -s user_ca -I user@example.com -n user -V +1d user_key.pub
  > ```
  >
  > 上面的命令会生成用户证书`user_key-cert.pub`（用户公钥名字加后缀`-cert`）。这个命令各个参数的含义如下。
  >
  > - `-s`：指定 CA 签发证书的密钥
  > - `-I`：身份字符串，可以随便设置，相当于注释，方便区分证书，将来可以使用这个字符串撤销证书。
  > - `-n user`：指定用户名，表示证书仅对该用户名有效。如果有多个用户名，使用逗号分隔。用户以该用户名登录服务器时，SSH 通过这个值，分辨应该使用哪张证书，证明自己的身份，发给服务器。
  > - `-V +1d`：指定证书的有效期，这里为1天，强制用户每天都申请一次证书，提高安全性。默认情况下，证书是永远有效的。
  > - `user_key.pub`：用户公钥。
  >
  > 生成证书以后，可以使用下面的命令，查看证书的细节。
  >
  > ```
  > $ ssh-keygen -L -f user_key-cert.pub
  > ```
  >
  > 最后，为证书设置权限。
  >
  > ```bash
  > $ chmod 600 user_key-cert.pub
  > ```

- #### 服务器安装证书

  > CA 生成服务器证书`ssh_host_rsa_key-cert.pub`以后，需要将该证书发回服务器，可以使用下面的`scp`命令，将证书拷贝过去。
  >
  > ```
  > $ scp ~/.ssh/ssh_host_rsa_key-cert.pub root@host.example.com:/etc/ssh/
  > ```
  >
  > 然后，将下面一行添加到服务器配置文件`/etc/ssh/sshd_config`。
  >
  > ```
  > HostCertificate /etc/ssh/ssh_host_rsa_key-cert.pub
  > ```
  >
  > 上面的代码告诉 sshd，服务器证书是哪一个文件。
  >
  > 重新启动 sshd。
  >
  > ```bash
  > $ sudo systemctl restart sshd.service
  > # 或者
  > $ sudo service sshd restart
  > ```

- #### 服务器安装 CA 公钥

  > 为了让服务器信任用户证书，必须将 CA 签发用户证书的公钥`user_ca.pub`，拷贝到服务器。
  >
  > ```
  > $ scp ~/.ssh/user_ca.pub root@host.example.com:/etc/ssh/
  > ```
  >
  > 上面的命令，将 CA 签发用户证书的公钥`user_ca.pub`，拷贝到 SSH 服务器的`/etc/ssh`目录。
  >
  > 然后，将下面一行添加到服务器配置文件`/etc/ssh/sshd_config`。
  >
  > ```
  > TrustedUserCAKeys /etc/ssh/user_ca.pub
  > ```
  >
  > 上面的做法是将`user_ca.pub`加到`/etc/ssh/sshd_config`，这会产生全局效果，即服务器的所有账户都会信任`user_ca`签发的所有用户证书。
  >
  > 另一种做法是将`user_ca.pub`加到服务器某个账户的`~/.ssh/authorized_keys`文件，只让该账户信任`user_ca`签发的用户证书。具体方法是打开`~/.ssh/authorized_keys`，追加一行，开头是`@cert-authority principals="..."`，然后后面加上`user_ca.pub`的内容，大概是下面这个样子。
  >
  > ```
  > @cert-authority principals="user" ssh-rsa AAAAB3Nz...XNRM1EX2gQ==
  > ```
  >
  > 上面代码中，`principals="user"`指定用户登录的服务器账户名，一般就是`authorized_keys`文件所在的账户。
  >
  > 重新启动 sshd。
  >
  > ```
  > $ sudo systemctl restart sshd.service
  > # 或者
  > $ sudo service sshd restart
  > ```
  >
  > 至此，SSH 服务器已配置为信任`user_ca`签发的证书。

- #### 客户端安装证书

  > 客户端安装用户证书很简单，就是从 CA 将用户证书`user_key-cert.pub`复制到客户端，与用户的密钥`user_key`保存在同一个目录即可。

- #### 客户端安装 CA 公钥

  > 为了让客户端信任服务器证书，必须将 CA 签发服务器证书的公钥`host_ca.pub`，加到客户端的`/etc/ssh/ssh_known_hosts`文件（全局级别）或者`~/.ssh/known_hosts`文件（用户级别）。
  >
  > 具体做法是打开`ssh_known_hosts`或`known_hosts`文件，追加一行，开头为`@cert-authority *.example.com`，然后将`host_ca.pub`文件的内容（即公钥）粘贴在后面，大概是下面这个样子。
  >
  > ```
  > @cert-authority *.example.com ssh-rsa AAAAB3Nz...XNRM1EX2gQ==
  > ```
  >
  > 上面代码中，`*.example.com`是域名的模式匹配，表示只要服务器符合该模式的域名，且签发服务器证书的 CA 匹配后面给出的公钥，就都可以信任。如果没有域名限制，这里可以写成`*`。如果有多个域名模式，可以使用逗号分隔；如果服务器没有域名，可以用主机名（比如`host1,host2,host3`）或者 IP 地址（比如`11.12.13.14,21.22.23.24`）。
  >
  > 然后，就可以使用证书，登录远程服务器了。
  >
  > ```
  > $ ssh -i ~/.ssh/user_key user@host.example.com
  > ```
  >
  > 上面命令的`-i`参数用来指定用户的密钥。如果证书与密钥在同一个目录，则连接服务器时将自动使用该证书。

- #### 废除证书

  > 废除证书的操作，分成用户证书的废除和服务器证书的废除两种。
  >
  > 服务器证书的废除，用户需要在`known_hosts`文件里面，修改或删除对应的`@cert-authority`命令的那一行。
  >
  > 用户证书的废除，需要在服务器新建一个`/etc/ssh/revoked_keys`文件，然后在配置文件`sshd_config`添加一行，内容如下。
  >
  > ```
  > RevokedKeys /etc/ssh/revoked_keys
  > ```
  >
  > `revoked_keys`文件保存不再信任的用户公钥，由下面的命令生成。
  >
  > ```
  > $ ssh-keygen -kf /etc/ssh/revoked_keys -z 1 ~/.ssh/user1_key.pub
  > ```
  >
  > 上面命令中，`-z`参数用来指定用户公钥保存在`revoked_keys`文件的哪一行，这个例子是保存在第1行。
  >
  > 如果以后需要废除其他的用户公钥，可以用下面的命令保存在第2行。
  >
  > ```bash
  > $ ssh-keygen -ukf /etc/ssh/revoked_keys -z 2 ~/.ssh/user2_key.pub
  > ```

------

