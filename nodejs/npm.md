## 包管理工具npm

> - NPM是NodeJS的包管理工具（随同NodeJS一起安装），能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：
>   - 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
>   - 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
>   - 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。
> - 由于新版的nodejs已经集成了npm，所以之前npm也一并安装好了。可以通过输入 **"npm -v"** 来测试是否成功安装。
> - **npm**全称`Node Package Manager`，node的包管理工具，它是node官方内置的管理工具，所以必须掌握。
> - **包**（package）在nodejs中，其实就是一些外部的代码，就像java中的jar包一样，是开发中要用的一些工具包。
> - **包管理工具**就是管理包的软件，可以对包进行下载安装、更新、删除、上传等操作，有点像maven的依赖管理部分。借助这个工具，可以提高开发效率，避免重复造轮子。
> - 包是一个通用的概念，很多编程语言中都有包的概念（Java中的包管理工具是maven），所以使用好包管理工具很重要。常用的：
>   - npm（重点）
>   - cnpm
>   - yarn
> - 除了编程语言中有包管理工具之外，操作系统中也有包管理工具（这里的包是系统上运行的软件包），如：
>   - Windows：chocolatey，网址：https://chocolatey.org/
>   - CentOS：yum（rpm包），网址：https://packages.debian.org/stable/
>   - Ubantu：apt，网址：https://packages.ubantu.com/
>   - MacOS：homebrew，网址：https://brew.sh/

- #### npm的基本使用：

  - **初始化**：创建一个空目录，cmd切换到该目录下执行`npm init`。目的是将该文件夹初始化为**包目录**，让其成为资源目录，即一个Node工程的目录。

    > 该命令会通过交互式的方式，来创建`package.json`文件。`package.json`文件是**包目录的配置文件**（类似maven中pom文件），每个合法的Node的包都必须包含该文件。该`package.json`文件也可以手动编写。
    >

    > package.json文件示例：

    ```json
    {
      "name": "test",  // 项目名（全球唯一性）
      "version": "1.0.0",  // 项目版本
      "description": "这是第一个npm包目录",  // 项目描述
      "main": "index.js",  // 项目入口
      "scripts": {  // 项目常用命令
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "author": "zc",  // 项目作者
      "license": "ISC"  // 项目许可证
    }
    ```

    > 初始化过程的一些注意事项：
    >
    > 1. 项目的**包名不能用中文和大写字母**，默认是目录名，所以项目的文件夹名最好**别用中文和大写字母**。
    > 2. 版本号`version`要求定义为`x.x.x`的形式，x必须是数字，默认值`1.0.0`
    > 3. 通过`npm init -y/--yes`命令，可以用默认配置来快速创建`package.json`文件
    >
    > （有些windows系统的powershell中不允许使用node全局命令来执行脚本文件，所以需要修改windows的执行策略。以管理员模式打开powershell，运行命令`set-ExecutionPolicy remoteSigned`即可）

  - **搜索包**：找npm包，可以通过cmd命令`npm s/search 关键字`的方式，也可以浏览该网址：https://www.npmjs.com/，找需要的包

  - **安装包/下载包**：cmd切换到**包目录下**，执行命令：`npm i/install 包名`，运行后会联网下载包。我们运行后发现多个2个文件，`package-lock.json`文件和`node_modules`目录。该命令有**自动向上寻找**的功能，会向上层找package.json文件，所以项目下任意位置都可以执行`npm i`安装包。

    - `node_modules`目录：该包目录下存放着下载的包。
    - `package-lock.json`文件：npm的锁文件，用来锁定包的版本。虽然`package.json`的`dependencies`中已经有关于包版本的描述信息了，但是它只是版本描述，如：`"express": "^4.19.2"`就表示安装4.x.x中最新的版本，`"express": "~4.19.2"`表示安装4.19.x中最新的版本，`"express": "*4.19.2"`表示安装最新的版本，`"express": "4.19.2"`表示固定该版本。但是固定版本也不好，如果想更新版本就得改该文件，而且该包可能依赖其他包，它们的版本你也控制不了。所以锁文件就是为了锁住住依赖树上所有包的版本的，这样别人用该配置文件下载包时，用的就是一样版本的包。该文件如果没有，会在npm安装包的时候自动产生，今后不管谁来安装包，都会在该文件的基础上进行包安装，里面的版本都是写死的。

    > 此时就可以在我们的包目录中，新建文件，文件中通过`require('包名')`导入包后，就可以使用该包了，以后就不需要我们手动下载jquery的js文件了。下载的该npm包，就是当前我们自己项目中的一个**依赖**了。

- #### 使用require('包名')导入npm包的原理：

  > 1. 它首先去当前路径中的`node_modules`目录下找包名对应的目录，找到就导入该目录
  > 2. 如果没有，它有**自动向上寻找**的功能。会去当前文件所在目录的上一级目录，找`node_modules`目录，找到就导入。没有则一级级的往上直到根目录。

- #### 生产依赖和开发依赖：

  > 我们在用npm安装包/依赖时，安装的依赖包大体上分为2种类型，生产依赖和开发依赖。
  >
  > 开发依赖是仅在开发时用，生产环境不需要，最终经过工具打包后，该依赖不会出现在产物中。生产依赖是指在开发和生产时都要用。
  >
  > - （默认）安装生产依赖：`npm -i -S/--save 包名`
  >
  > - 安装开发依赖：`npm -i -D/--save-dev 包名`
  >
  > 例如`less`，就只需要安装为开发依赖即可。

- #### npm的全局安装：（不需要初始化包目录就可以进行全局安装）

  > 之前我们安装的依赖，默认都是局部安装，都是只能在我们的项目的包目录下使用。我们如果想在任意位置都能用该包，需要加安装选项`-g`进行全局安装。全局安装的npm包相当于电脑上的一个软件，该软件依赖于Node环境运行，一般是通过命令行运行

  > 全局安装加`-g`参数：`npm i -g 包名`，全局的npm包的特点：
  >
  > - 全局包的使用不像我们之前那些包还得导入，全局包的使用是通过cmd中执行一个独立的命令。并且全局包使用的命令不受工作目录的影响，随便哪个路径都可以用全局包的运行命令。
  > - 全局包的安装目录默认是计算机user中某个位置，可以通过命令`npm root -g`来查看全局包的默认安装目录。
  > - 并不是所有包都适合全局安装，只有全局的工具才适合，可以通过查看包文档来确定安装方式。像webpack、nodemon、vue cli等开发工具就需要全局安装。
  >
  > （我们之前启动http服务，是通过node命令来运行js文件，如果源码改了还需要重新运行才行。这时我们可以安装`nodemon`插件，使用全局安装，然后运行js代码通过它提供的命令`nodemon 文件`来执行，此时源码修改保存后，http服务程序会自动重启）

- #### 根据已存在的package.json配置文件安装所有包依赖：（因为node_modules目录不会上传到git仓库）

  > `node i/install`，可以根据包目录下的`package.json`和`package-lock.json`文件中的依赖声明，安装所有依赖。

- #### npm安装指定版本的包：

  > 用@符`npm i/install jquery@1.11.2`

- #### npm卸载包：（且会更新package.json和package-lock.json文件）

  > 局部包的删除用`npm r/remove/uninstall 包名`，全局包的卸载用`npm r/remove/uninstall -g 包名`

- #### npm配置命令别名：

  > - 通常在`package.json`文件中，通过`scripts`属性，在里面加上`"别名": "node ./test.js"`，来配置命令别名。这些需要调用Node的执行环境来运行的命令，都可以在该文件（Node运行环境的配置文件）中来配置。
  > - 此时就可以这样来运行test.js文件：`npm run 别名`。如果别名叫做`start`，那么运行时可以省略`run`，如：`npm start`，它常用于启动项目。
  > - 使用别名命令`npm run`运行命令，它也有**自动向上寻找**的功能。（类似于`require('包名')`，很方便）

- #### 查看npm的配置信息：

  > 通过命令`npm config list`可以查看npm工具的配置信息。（常用于查看npm的下载地址）

- #### 查看已安装的包：（项目中必须有package.json文件）

  > `npm list`：查看当前已安装的局部包（也可以在package.json中查看）
  >
  > `npm list --dev`：查看局部包的开发环境包
  >
  > `npm list -g`：查看已安装的全局工具包
  >
  > `npm list -g --depth 0`：查看已安装的全局工具包，不看依赖项

- #### 将npm下载包的地址改为淘宝镜像：

  - 方式1：`npm config set registry https://registry.npmmirror.com/`
  - 方式2（推荐）通过nrm（Npm Registry Manager）工具来配置，它是专门管理npm的下载地址的。使用步骤：
    1. 安装nrm全局工具：`npm i -g nrm`
    2. 查看支持的镜像地址：`nrm ls`
    3. 修改镜像：`nrm use taobao`

------

## （了解）包管理工具cnpm

> - 由于npm的网站在国外，国内直接使用npm的官方镜像下载包很慢，所以推荐使用淘宝的npm的完整镜像（只读），网址：
>
>   https://www.npmmirror.com/，（注意：淘宝镜像只能下载，不能上传，是只读镜像）
>
> - 淘宝也提供了一个全局工具包cnpm，操作命令和npm大体相同。但是node.js中没有这个工具，需要下载。安装命令：
>
>   `npm i -g cnpm --registry=https://registry.npmmirror.com`

------

## 包管理工具yarn

> Yarn 是 Facebook, Google, Exponent 和 Tilde 在2016年开发的一款新的 JavaScript 包管理工具，目的是解决当时这些团队使用 npm 面临的少数问题。官方网址：https://yarnpkg.com/，yarn的特点：
>
> - 速度超快：yarn缓存了每个下载过的包，所以再次安装时无需重复下载。同时利用并行下载以最大化资源利用率，因此安装超快。
> - 超级安全：在运行代码之前，yarn会通过算法校验每个包的完整性。
> - 超级可靠：使用详细、简介的锁文件格式和明确的安装算法，yarn能够保证在不同系统上无差异的工作。

- #### yarn的安装：

  > 我们可以通过npm安装yarn：`npm i -g yarn`

- #### yarn的常用命令：

  > - 初始化：`yarn init`和`yarn init -y`，（yarn的锁文件是`yarn.lock`）
  >
  > - 安装：`yarn add 包名`，如果安装开发依赖，后面跟上`--dev`参数，全局安装`yarn global add 包名`（yarn全局安装的工具需要手动配置windows环境变量，通过`yarn global bin`查看全局工具的安装位置）
  >
  > - 删除：`yarn remove 包名`，全局删除`yarn global remove 包名`
  >
  > - 安装项目所有依赖：`yarn`
  >
  > - 运行命令别名：`yarn 别名`，（任何别名都是该命令，不需要加`run`）
  >
  > - 查看yarn的配置：`yarn config list`，和npm一样

- #### 配置yarn的下载地址为淘宝镜像：

  > 执行：`yarn config set registry https//registry.npmmirror.com/`

------

#### 用npm发布自己写的包：

- ##### 发布包：

  1. 新建一个目录，通过`npm init`进行将目录初始化为npm包。（包名最好不要用test、learn这种关键字，因为官方有垃圾包检测机制）
  2. 包中放代码文件，然后将里面的数据通过`modules.exports`暴露。
  3. 注册npm官网账号。
  4. 将npm地址修改为官方地址。
  5. （关键）执行`npm login`进行登录。
  6. （关键）发布当前npm包到官网服务器`npm publish`

- ##### 更新包：

  1. 在当前npm包中，更新包中代码，并写测试代码看是否可用
  2. （关键）修改包配置文件`package.json`中的版本号
  3. （关键）发布当前npm包到官网服务器`npm publish`

- ##### 删除包：

> 在**当前npm包**中执行`npm unpublish`，删除失败可以在后面添加`--force`参数强制删除。而且删除包需要满足一定条件：
>
> - 你是包的作者
> - 发布小于24h
> - 大于24h后，这个包没有被其他包依赖，且每周小于300下载量，且只有一个维护者

------

#### nvm工具：

> nvm（Node Version Manager）是node版本的管理工具，方便在切换电脑上不同版本的node环境。
>
> 它也是在cmd命令行中使用，用的话需要先安装，网址：https://github.com/coreybutler/nvm-windows/releases，选择nvm-setup.exe下载即可。安装都是下一步，先选择nvm的安装路径，再设置nodejs的安装路径（不要有中文空格什么的）。
>
> nvm的常用命令如下：
>
> - `nvm list`是列出当前pc上已经安装的所有node
> - `nvm list available`：列出目前node的所有可安装的版本
> - `nvm install 版本号`：安装该版本的node。`nvm install latest`是安装最新版node
> - `nvm uninstall 版本号`：卸载该版本的node
> - `nvm use 版本号`：使用该版本的node

------

#### Corepack：

> Corepack 是 Node.js 官方自带的“包管理器管理工具”。它的核心作用是让你和你的团队在开发项目时，不必再手动全局安装 Yarn 或 pnpm，更不用操心每个项目该用哪个版本，它会自动处理这一切。
>
> 你可以把它理解成一个“包管理器的统一调度中心”，主要解决了两个痛点：
>
> 1. **新成员上手项目时**：不需要再问“这个项目是用 Yarn 还是 pnpm？版本是多少？怎么装？”，Corepack 会自动搞定。
> 2. **团队协作时**：彻底杜绝因包管理器版本不一致导致的“我这里明明可以运行啊”这类问题。

###### 它是如何工作的？

1. **声明**：你只需要在项目的 `package.json` 里，通过 `"packageManager"` 字段明确指定要用哪个包管理器及其精确版本。

   ```json
   {
     "packageManager": "pnpm@9.15.0"
   }
   ```

2. **拦截与执行**：当你在项目目录下运行 `pnpm install` 时，Corepack 会拦截这个命令。

3. **自动下载**：它会读取 `package.json` 中的配置，检查本地是否有 `pnpm@9.15.0`。如果没有，它会自动、透明地从网络下载这个精确版本，然后用它来执行安装。（Corepack 下载的 pnpm 可执行文件放在系统缓存目录`~/.cache/node/corepack/` 目录下，不会出现在你的项目 `node_modules` 里，也不会写入 `package.json` 的任何依赖字段）

###### 如何使用？

Corepack 从 Node.js `v16.9.0` 和 `v14.19.0` 开始就已内置，但它目前仍是一个**实验性功能**，需要手动启用一下。

**1. 启用 Corepack**
在终端执行以下命令，它会在你的系统里设置好环境，让你能直接使用 `yarn` 和 `pnpm` 命令。

```bash
corepack enable
```

> 如果你想用 Corepack 也管理 `npm` 本身，可以运行 `corepack enable npm`。

**2. 为一个新项目指定包管理器**
在项目根目录下运行，Corepack 会自动下载指定版本，并帮你把 `packageManager` 字段写入 `package.json`。

```bash
# 告诉项目使用 pnpm 的最新 9.x 版本
corepack use pnpm@9.x
```

###### 需要注意的点

- **状态**：Corepack 目前仍是 **实验性** 功能。这意味着它的接口或行为在未来版本中可能会有调整，但目前已被主流项目广泛使用，相当稳定。

- **网络依赖**：它默认在团队每个人**首次运行**或**切换版本**时需要网络连接来下载包管理器。

  > 如果需要在完全离线的生产环境（如 CI/CD）中使用，可以先通过 `corepack pack` 命令提前打包好。
  >
  > 完整的流程是：
  >
  > 1. 在能联网的机器上（如本地电脑）执行 `corepack pack`，这会生成一个 `.tgz` 文件，你可以把它提交到 Git 仓库，或放在构建镜像里。
  > 2. 在离线的生产环境/CI机器上执行 `corepack install -g --cache-only <path/to/corepack.tgz>`：
  >    这个命令会读取第一步生成的 `.tgz` 文件，并将其中的包管理器安装到 Corepack 的缓存中（即`~/.cache/node/corepack/`）。之后，Corepack 就能在完全不联网的情况下，直接使用这个缓存版本来运行 `pnpm install` 了。

------

