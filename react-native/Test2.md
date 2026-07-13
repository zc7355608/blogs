### 在真机上运行

在真机上仔细测试应用后再发布给用户总是不会错的。本文档将指导你通过必须的步骤在设备上运行 React Native 应用，为生产做准备。

> 如果你使用 `create-expo-app` 来创建项目，可以通过 Expo Go 扫描运行 `npm start` 时显示的二维码来在设备上预览应用。更多信息请参阅 Expo 的[在设备上运行项目](https://docs.expo.dev/get-started/expo-go/)指南。

#### 在 Android 设备上运行应用

1. ##### 开启 USB 调试：

   在默认情况下 Android 设备只能从应用市场来安装应用。你需要开启 USB 调试才能自由安装开发版本的 APP。

   要开启 USB 调试，首先需要在 **设置** → **关于手机** → **软件信息** 中连续点击底部的`版本号`七次来启用"开发者选项"菜单。然后回到 **设置** → **开发者选项** 中开启"USB 调试"。

2. ##### 通过 USB 数据线连接设备：

   现在我们设置一个 Android 设备来运行我们的 React Native 项目，通过 USB 将你的设备插入开发机器以继续。

   下面检查你的设备是否能正确连接到 ADB（Android Debug Bridge）。CMD中运行`adb devices`命令：

   ```shell
   List of devices attached
   emulator-5554 offline   # Google emulator
   14ed2fcc device         # Physical device
   ```

   在右边那列看到 `device` 说明你的设备已经被正确连接了。注意，你每次只应当**连接一个设备**。

3. ##### 在真机上运行应用：

   在项目根目录下运行以下命令来在设备上安装并启动应用：

   ```shell
   npm run android
   ```

   > 你还可以使用 `React Native CLI` 来生成和运行 `release` 版本的应用，例如在项目根目录下：`npm run android --mode release`。

##### 从设备上访问开发服务器：

在启用开发服务器的情况下，你可以快速的迭代修改应用，然后在设备上立即查看结果。有2种方式可以实现，取决于你是否有 USB 数据线或 Wi-Fi 网络。

1. ###### 使用adb reverse（推荐）：

   如果你的设备运行 Android 5.0（Lollipop）或更新版本，且已开启 USB 调试并通过 USB 连接到开发机器，你可以使用此方法。

   在命令行中运行：

   ```shell
   adb -s <device name> reverse tcp:8081 tcp:8081
   ```

   要查找设备名称，运行以下 adb 命令：

   ```shell
   adb devices
   ```

   现在你可以从[开发者菜单](https://reactnative.cn/docs/debugging#opening-the-dev-menu)启用快速刷新。每当你的 JS 代码发生更改，应用就会重新加载。

2. ###### 通过wifi连接的方式：

   你还可以通过 Wi-Fi 连接到开发服务器。你首先需要使用 USB 在设备上安装应用，完成之后便可以按照以下说明进行无线调试。在继续之前，你需要知道开发机器的当前 IP 地址。

   打开命令提示符并输入 `ipconfig` 来查找你电脑的 IP 地址。

   1. 确保你的电脑和手机在**同一个** Wi-Fi 网络中。
   2. 在设备上打开 React Native 应用。
   3. 你会看到一个[红屏错误提示](https://reactnative.cn/docs/debugging#logbox)。这是正常的，下面的步骤会解决。
   4. 打开应用内的[开发者菜单](https://reactnative.cn/docs/debugging#opening-the-dev-menu)。
   5. 前往 **Dev Settings** → **Debug server host & port for device**。
   6. 输入你电脑的 IP 地址和端口号（例如 `10.0.1.1:8081`）。
   7. 回到**开发者菜单**然后选择 **Reload JS**。

   现在你可以从[开发者菜单](https://reactnative.cn/docs/debugging#opening-the-dev-menu)启用快速刷新。每当你的 JS 代码发生更改，应用就会重新加载。

你已经使用 React Native 构建了一个很棒的应用，现在你渴望在 Play Store 中发布它。该过程与任何其他原生 Android 应用相同，但需要考虑一些额外的注意事项。按照[生成签名 APK](https://reactnative.cn/docs/signed-apk-android)的指南了解更多信息。

#### 在 iOS 设备上运行应用

xxx

### React Native中使用第三方库

React Native 第三方库都保存在 npm 中心仓中，要在你的项目中安装一个RN的库，直接`npm install 包名`即可。

[React Native 目录](https://reactnative.directory)中收录了专门为 React Native 构建的第三方库。这是寻找适用于你的 React Native 应用的库的第一站。

> 你在目录中找到的许多库来自 [React Native 社区](https://github.com/react-native-community/) 或者 [Expo](https://docs.expo.dev/versions/latest/)。
>
> 由 React Native 社区构建的库是由志愿者和依赖 React Native 的公司的个人推动的。它们通常支持  iOS、tvOS、Android、Windows，但这在各个项目之间有所不同。这个组织中的许多库曾经是 React Native 的核心组件和  API。
>
> 由 Expo 构建的库都用 TypeScript 编写，并尽可能支持 iOS、Android 和 `react-native-web`。

如果在 **React Native 目录**找不到专门针对 React Native 的库，那么 npm 中心仓库是下一个最佳选择。npm 中心仓是 JS 库的权威来源，但它列出的库可能并非都与 React Native 兼容。React  Native 是众多 JS 运行环境之一，类似 Node.js、Web 浏览器、Electron 等，而 npm  中存放了适用于所有这些环境的库。

> 通常为其他平台特别构建的库与 React Native 不兼容。例如，`react-select`是为 Web 构建的，专门针对`react-dom`，而`rimraf`是为 Node.js 构建的，并与计算机文件系统交互。像`lodash`这样的库仅使用 JavaScript 语言特性，在任何环境中都可以工作。随着时间的推移你将对此有所了解，但在那之前，最简单的方法是自己尝试。如果发现它在 React Native 中不起作用，你可以使用`npm uninstall`来移除包。

因为不同平台使用的包管理工具不同，因此npm安装了第三方库后，通常需要将其中的内容与原生代码进行链接。

#### 链接 iOS 原生代码

React Native 使用 CocoaPods 来管理 iOS 项目的依赖项，大多数 React Native 库都遵循这个约定。如果你使用的库不是这样的，请参考它们的 README 获取额外的指导。在大多数情况下以下说明都适用。

在 `ios` 目录下运行 `pod install` 将其链接到原生 iOS 项目。一个不需要切换到 `ios` 目录就可以做到这一点的快捷方式是运行 `npx pod-install`。

```shell
npx pod-install
```

完成后，重新构建应用程序以开始使用新库：

```shell
npm run ios
```

#### 链接 Android 原生代码

React Native 使用 Gradle 来管理 Android 项目的依赖关系。在安装了带有原生依赖项的库之后，你需要重新构建应用程序二进制文件才能使用新库：

```shell
npm run android
```

### Metro

React Native 使用 [Metro](https://metrobundler.dev/) 来构建你的 JS 代码和资源。

Metro 的配置选项可在您的项目中进行自定义`metro.config.js`文件。它可以导出以下两种格式之一:

- 一个对象，它将被合并到Metro的内部配置默认值之上。（推荐）
- 一个函数，它将被调用，并与Metro的内部配置默认值，合并后返回一个最终的配置对象。

在 React Native 中，您的 Metro 配置应同时继承[`@react-native/metro-config`](https://www.npmjs.com/package/@react-native/metro-config)或[`@expo/metro-config`](https://www.npmjs.com/package/@expo/metro-config)。这些包包含构建和运行 React Native 应用程序所必需的必要默认值。

以下是默认值`metro.config.js`React Native 模板项目中的文件：

```js
const {
  getDefaultConfig,
  mergeConfig,
} = require('@react-native/metro-config');

/**
 * Metro configuration
 * https://metrobundler.dev/docs/configuration
 *
 * @type {import('metro-config').MetroConfig}
 */
const config = {};

module.exports = mergeConfig(getDefaultConfig(__dirname), config);
```

您希望自定义的Metro选项可以在`config`对象中完成。

#### 高级——使用配置功能

**导出配置功能是自行管理最终配置的选项——Metro** 不会应用任何内部默认值。此模式在需要从 Metro 读取基础默认配置对象或动态设置选项时可能非常有用。

> **来自`@react-native/metro-config`0.72.1** 不再需要使用配置函数来访问完整的默认配置。**Tip**请参见下方的提示部分。

```js
const {
  getDefaultConfig,
  mergeConfig,
} = require('@react-native/metro-config');

module.exports = function (baseConfig) {
  const defaultConfig = mergeConfig(
    baseConfig,
    getDefaultConfig(__dirname),
  );
  const {
    resolver: {assetExts, sourceExts},
  } = defaultConfig;

  return mergeConfig(defaultConfig, {
    resolver: {
      assetExts: assetExts.filter(ext => ext !== 'svg'),
      sourceExts: [...sourceExts, 'svg'],
    },
  });
};
```

> 使用配置功能适用于高级用例。比上述方法更简单,例如用于自定义`sourceExts`将从以下步骤中读取这些默认值`@react-native/metro-config`。
>
> **替代方案**
>
> ```js
> const defaultConfig = getDefaultConfig(__dirname);
> 
> const config = {
>   resolver: {
>     sourceExts: [...defaultConfig.resolver.sourceExts, 'svg'],
>   },
> };
> 
> module.exports = mergeConfig(defaultConfig, config);
> ```
>
> **However!**但是!我们建议在覆盖这些配置值时进行复制和编辑——将真相的来源置于您的配置文件中。
>
> ✅ **推荐**
>
> ```js
> const config = {
>   resolver: {
>     sourceExts: ['js', 'ts', 'tsx', 'svg'],
>   },
> };
> ```

### 使用TS

由 [React Native CLI](https://reactnative.cn/docs/getting-started-without-a-framework#step-1-creating-a-new-application) 创建的新项目或使用 [Ignite](https://infinite.red/ignite) 等流行模板将默认使用 TypeScript，同时也支持 JS 和 Flow，无需额外配置。和非 TypeScript 的 React Native 项目一样都是直接通过 [Babel](https://reactnative.cn/docs/javascript-environment#javascript-syntax-transformers) 将您的文件转换为 JavaScript。

具有 `.jsx` 或 `.js` 扩展名的文件将被视为 JavaScript 而非 TypeScript，并且不会进行类型检查。TypeScript 模块仍可导入 JavaScript 模块，反之亦然。

##### 在已有项目中添加TS：

1. 将 TypeScript，类型文件，以及 ESLint 插件等依赖添加到项目中。

   ```shell
   npm install -D typescript @react-native/typescript-config @types/jest @types/react @types/react-test-renderer
   ```

   > 该命令添加了每个依赖项的最新版本。版本可能需要更改，以匹配项目使用的现有软件包。您可以使用类似 [React Native Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 的工具查看 React Native 提供的版本。

2. 添加一个 TypeScript 配置文件。在项目的根目录中创建一个`tsconfig.json`：

   ```json
   {
     "extends": "@react-native/typescript-config"
   }
   ```

3. 将 `.js` 文件重命名为`* .tsx`。

   > 请保留`./index.js`入口文件，否则可能将在打包生产版本时遇到问题。

4. 运行 `tsc` 对 TS 文件进行类型检查。

   ```shell
   npx tsc
   ```

##### 在TS中使用自定义路径别名：

要使用 TypeScript 的自定义路径别名，需要设置从 Babel 和 TypeScript 操作的路径别名。具体方法如下：

1. 编辑`tsconfig.json`。在根目录中设置任何内容`src`无需前面的路径引用，并允许使用任何测试文件进行访问`tests/File.tsx`：

   ```json
   {
     "extends": "@react-native/typescript-config",
     "compilerOptions": {
       "baseUrl": ".",
       "paths": {
         "*": ["src/*"],
         "tests": ["tests/*"],
         "@components/*": ["src/components/*"],
       },
     }
   }
   ```

2. 安装`babel-plugin-module-resolver`包作为开发依赖：

   ```shell
   npm install -D babel-plugin-module-resolver
   ```

3. 配置`babel.config.js`：

   ```js
   {
     presets: ['module:metro-react-native-babel-preset'],
     plugins: [
       [
         'module-resolver',
         {
           root: ['./src'],
           extensions: ['.ios.js', '.android.js', '.js', '.ts', '.tsx', '.json'],
           alias: {
           tests: ['./tests/'],
             "@components": "./src/components",
           }
         }
       ]
     ]
   }
   ```

