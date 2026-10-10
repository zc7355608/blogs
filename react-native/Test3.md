### 界面与交互

#### 界面

- ##### 样式：

  在 React Native 中，你并不需要学习什么特殊的语法来定义样式。我们仍然是使用 JS 来写样式。所有的核心组件都接受名为`style`的属性。这些样式名基本上是遵循了 web 上的 CSS 的命名，只是按照 JS 的语法要求使用了驼峰命名法，例如将`background-color`改为`backgroundColor`。

  > 注意：RN中**没有CSS的继承（除了`<Text>`组件）**。并且RN中**不支持CSS渐变和CSS变量**，还有一些RN中特有的CSS样式，比如：`marginHorizontal`（水平外边距），`marginVertical`（垂直外边距）。

  **`style`属性可以是一个普通的 JS 对象**，这是最简单的用法（和React一样）。而**在RN中，`style`属性的值还可以是一个数组**——在数组中位置居后的样式对象比居前的优先级更高，这样你可以间接实现样式的继承（数组中的对象是浅合并）。

  实际开发中组件的样式会越来越复杂，我们**建议使用`StyleSheet.create`来集中定义组件的样式**。比如像下面这样：

  ```jsx
  import { StyleSheet, Text, View } from 'react-native';
  
  const LotsOfStyles = () => {
      return (
        <View style={styles.container}>
          <Text style={styles.red}>just red</Text>
          <Text style={styles.bigBlue}>just bigBlue</Text>
          <Text style={[styles.bigBlue, styles.red]}>bigBlue, then red</Text>
          <Text style={[styles.red, styles.bigBlue]}>red, then bigBlue</Text>
        </View>
      );
  };
  
  const styles = StyleSheet.create({
    container: {
      marginTop: 50,
    },
    bigBlue: {
      color: 'blue',
      fontWeight: 'bold',
      fontSize: 30,
    },
    red: {
      color: 'red',
    },
  });
  
  export default LotsOfStyles;
  ```

  `StyleSheet.create()` 其实是 React Native 官方提供的“样式注册器 + 样式校验器 + 优化工具”，是**官方的最佳实践和代码规范**。

  现在你已经了解如何调整文本样式了，下面我们要学习的是如何控制组件的尺寸。

- ##### 高度与宽度：

  组件的高度和宽度决定了其在屏幕上显示的尺寸。

  最简单的给组件设定尺寸的方式就是在样式中指定固定的`width`和`height`。**React Native 中的尺寸（数字）都是无单位的**，表示的是与设备像素密度无关的逻辑像素点。而且尺寸**也可以用百分比字符串**来表示。

  ```jsx
  import {View} from 'react-native';
  
  const FixedDimensionsBasics = () => {
    return (
      <View>
        <View
          style={{
            width: 50,
            height: 50,
            backgroundColor: 'powderblue',
          }}
        />
        <View
          style={{
            width: 100,
            height: 100,
            backgroundColor: 'skyblue',
          }}
        />
        <View
          style={{
            width: '30%',
            height: '30%',
            backgroundColor: 'steelblue',
          }}
        />
      </View>
    );
  };
  
  export default FixedDimensionsBasics;
  ```

  这样给组件设置尺寸也是一种常见的模式，比如要求在不同尺寸的屏幕上都显示成一样的大小。

  > 注意：
  >
  > 逻辑像素点和物理尺寸之间没有统一的映射关系。这意味着一个使用固定尺寸的组件在不同设备和屏幕大小上可能对应不同的物理尺寸。但对于大多数场景来说，这种差异可以忽略不计。

- ##### RN中的Flexbox布局：

  React Native 中的 Flexbox 的工作原理和 web 上的 CSS 基本一致，当然也存在少许差异。首先是默认值不同：`flexDirection`的默认值为`column`（而不是`row`），`alignContent`默认值为 `flex-start`（而不是 `stretch`）, `flexShrink` 默认值为`0` （而不是`1`）, 并且`flex`只能指定一个数字值。

  ###### 定位：

  RN中只有绝对定位和相对定位，并且没有**文档流**的概念。RN中的 `static` 静态定位仅在新架构中可用。RN中`position`属性的默认值是`relative`，而不是Web中的`static`。RN中的`zIndex`和Web中的规则也不一样。

  ###### RN中的包含块：

  元素的包含块是控制其位置和大小的祖先元素。

  RN 中包含块的工作方式与 Web 上的工作方式非常相似，但由于缺少一些 Web 功能而有所简化。

  绝对定位元素的 `top`、`right`、`bottom` 和 `left` 值将相对于其包含块来计算。

  应用于绝对定位元素的百分比长度（例如：`width: '50%'` 或 `padding: '10%'`）将相对于其包含块的大小来计算。例如，如果包含块宽度为 100 点，则绝对定位元素上的 `width: 50%` 将使其宽度为 50 点。

  以下列表将帮助你确定任何给定元素的包含块：

  - 如果该元素的 `position` 类型为 `relative` 或 `static`，则其包含块是其父元素。
  - 如果该元素的 `position` 类型为 `absolute`，则其包含块是满足以下条件之一的最近祖先元素：
    - 它的 `position` 类型不是 `static`
    - 它具有 `transform`

- ##### 图片：

  - ###### 静态图片资源：

    React Native 提供了一个统一的方式来管理 iOS 和 Android 应用中的图片。要往 App 中添加一个静态图片，只需把图片文件放在代码文件夹中某处，然后像下面这样去引用它：

    ```jsx
    <Image source={require('./my-icon.png')} />
    ```

    图片文件的查找会和 JS 模块的查找方式一样。在上面的这个例子里，是哪个组件引用了这个图片，打包工具就会去这个组件所在的文件夹下查找`my-icon.png`。

    你还可以使用`@2x`，`@3x`这样的文件名后缀，来为不同的屏幕精度提供图片。比如下面这样的代码结构：

    ```tex
    .
    ├── button.js
    └── img
        ├── check.png
        ├── check@2x.png
        └── check@3x.png
    ```

    并且`button.js`里有这样的代码：

    ```jsx
    <Image source={require('./img/check.png')} />
    ```

    打包工具会打包所有的图片并且依据屏幕精度提供对应的资源。譬如说，iPhone 7 会使用`check@2x.png`，而 iPhone 7 plus 或是 Nexus 5 上则会使用`check@3x.png`。如果没有图片恰好满足屏幕分辨率，则会自动选中最接近的一个图片。

    这样会带来如下的一些好处:

    1. iOS 和 Android 一致的文件系统。

    2. 图片和 JS 代码处在相同的文件夹，这样组件就可以包含自己所用的图片而不用单独去设置。

    3. 不需要全局命名。你不用再担心图片名字的冲突问题了。

    4. 只有实际被用到（即被 require）的图片才会被打包到你的 app。

    5. 现在在开发期间，增加和修改图片不需要重新编译了，只要和修改 js 代码一样刷新你的模拟器就可以了。

       > 在 Windows 上，如果你向项目中添加了新的图片，可能需要重启打包工具Metro。

    6. 打包工具可以得知图片大小了，不需要在代码里再声明一遍尺寸。

    7. 现在通过 [npm](https://www.npmjs.com/) 来分发组件或库可以包含图片了。

    注意：为了使新的图片资源机制正常工作，require 中的图片名字必须是一个静态字符串（**不能使用变量**！因为 require 是在编译时期执行，而非运行时期执行！）。

    ```jsx
    // 正确
    <Image source={require('./my-icon.png')} />;
    
    // 错误
    const icon = this.props.active
      ? 'my-icon-active'
      : 'my-icon-inactive';
    <Image source={require('./' + icon + '.png')} />;
    
    // 正确
    const icon = this.props.active
      ? require('./my-icon-active.png')
      : require('./my-icon-inactive.png');
    <Image source={icon} />;
    ```

    请注意：通过这种方式引用的图片资源包含图片的尺寸（宽度，高度）信息，如果你需要动态缩放图片（例如，通过 flex），你可能必须手动在 style 属性设置`{ width: null, height: null }`。

  - ###### 静态的非图片资源：

    上面描述的`require`语法也可以用来静态地加载你项目中的声音、视频或者文档文件。大多数常见文件类型都支持，包括`.mp3`, `.wav`, `.mp4`, `.mov`, `.html`, `.pdf`等。完整列表请看 [bundler defaults](https://github.com/facebook/metro/blob/main/packages/metro-config/src/defaults/defaults.js#L16-L51)。

    > 你也可以在 [Metro 配置文件](https://metrobundler.dev/docs/configuration)中添加 [`assetExts` resolver 配置项](https://metrobundler.dev/docs/configuration#resolver-options)来支持其他类型的文件。

    需要注意的是**视频必须指定尺寸**而不能使用`flex`样式，因为我们**目前还不能从非图片资源中获取到尺寸信息**。对于直接链接到 Xcode 或者 Android 资源文件夹的视频，则不会有这个限制。

  - ###### 使用混合 App 的图片资源：

    如果你在编写一个混合 App（一部分 UI 使用 React Native，而另一部分使用平台原生代码），也可以使用已经打包到 App 中的图片资源（以拖拽的方式放置在 Xcode 的 asset 类目中，或是放置在 Android 的 drawable  目录里）。注意此时只使用文件名，不带路径也不带后缀：

    ```jsx
    <Image
      source={{uri: 'app_icon'}}
      style={{width: 40, height: 40}}
    />
    ```

    对于放置在 Android 的 assets 目录中的图片，还可以使用`asset:/` 前缀来引用：

    ```jsx
    <Image
      source={{uri: 'asset:/app_icon.png'}}
      style={{width: 40, height: 40}}
    />
    ```

    注意：这些做法并没有任何安全检查。你需要自己确保图片在应用中确实存在，而且还需要指定尺寸。

  - ###### 网络图片：

    很多要在 App 中显示的图片并不能在编译的时候获得，又或者有时候需要动态载入来减少打包后的二进制文件的大小。这些时候，与静态资源不同的是，你*需要手动指定图片的尺寸*。同时我们强烈建议你使用 https 以满足 iOS [App Transport Security](https://reactnative.cn/docs/publishing-to-app-store#1-enable-app-transport-security) 的要求。

    ```jsx
    // 正确
    <Image source={{uri: 'https://react.dev/logo-og.png'}}
           style={{width: 400, height: 400}} />
    
    // 错误
    <Image source={{uri: 'https://react.dev/logo-og.png'}} />
    ```

    你可以在 Image 组件的 source 属性中指定一些请求参数，如下面的示例：

    ```jsx
    <Image
      source={{
        uri: 'https://react.dev/logo-og.png',
        method: 'POST',
        headers: {
          Pragma: 'no-cache',
        },
        body: 'Your Body goes here',
      }}
      style={{width: 400, height: 400}}
    />
    ```

  - ###### Uri 数据图片：

    有时候你可能拿到的是图片的 base64 数据，此时可以使用`'data:'`格式来显示图片。请注意，你*需要手动指定图片的尺寸*。

    ```jsx
    // 请记得指定宽高！
    <Image
      style={{
        width: 51,
        height: 51,
        resizeMode: 'contain',
      }}
      source={{
        uri: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg==',
      }}
    />
    ```

    **缓存控制：**

    在某些情况下你可能仅仅想展示一张已经在本地缓存的图片，例如一个低分辨率的占位符，直到高分辨率的图片可用。又或者你无所谓图片是否过时，而且也不在乎显示过时的图片，节省带宽相对更重要。`cache` source 属性提供给了你控制网络层与缓存交互的方式。

    - `default`: 使用原生平台默认策略。
    - `reload`: URL 的数据将从原始地址加载。不使用现有的缓存数据。
    - `force-cache`: 现有的缓存数据将用于满足请求，忽略其期限或到期日。如果缓存中没有对应请求的数据，则从原始地址加载。
    - `only-if-cached`: 现有的缓存数据将用于满足请求，忽略其期限或到期日。如果缓存中没有对应请求的数据，则不尝试从原始地址加载，并且认为请求是失败的。

    ```jsx
    <Image
      source={{
        uri: 'https://react.dev/logo-og.png',
        cache: 'only-if-cached',
      }}
      style={{width: 400, height: 400}}
    />
    ```

  - ###### 背景图片与嵌套写法：

    开发者们常面对的一种需求就是类似 web 中的背景图（`background-image`）。要实现这一用例，只需使用`<ImageBackground>`组件（其 props 与`<Image>`完全相同），然后把需要背景图的子组件嵌入其中即可。

    也可能你并不需要使用`<ImageBackground>`，因为它的实现其实非常简单。你可以阅读其[文档](https://reactnative.cn/docs/imagebackground)然后思考你是否有更好更简单的布局方案。

    ```jsx
    return (
      <ImageBackground source={...} style={{width: '100%', height: '100%'}}>
        <Text>Inside</Text>
      </ImageBackground>
    );
    ```

    注意你*必须指定宽高*样式。

- ##### 颜色参考：

  React Native 中的组件使用 JS 来设置样式，颜色属性通常与 CSS 在 Web 上的工作方式相匹配。并且 RN 提供了多个颜色API，让你可以充分利用平台的设计系统和用户偏好：

  - [PlatformColor](https://reactnative.cn/docs/platformcolor) 允许你引用平台的颜色系统。
  - [DynamicColorIOS](https://reactnative.cn/docs/dynamiccolorios) 是 iOS 特有的，可以让你指定在浅色或深色模式下使用哪些颜色。

  ###### RN中的颜色表示方法有：（不仅支持 CSS 常见颜色写法，同时额外支持一些移动端特有的颜色写法）

  - 红-绿-蓝（RGB）：React Native 支持十六进制和函数表示法的 `rgb()` 与 `rgba()`。

    - `'#f0f'` (#rgb)
    - `'#ff00ff'` (#rrggbb)
    - `'#f0ff'` (#rgba)
    - `'#ff00ff00'` (#rrggbbaa)
    - `'rgb(255, 0, 255)'`
    - `'rgb(255 0 255)'`
    - `'rgba(255, 0, 255, 1.0)'`
    - `'rgba(255 0 255 / 1.0)'`

  - 色调-饱和度-亮度（HSL）：React Native 支持函数表示法的 `hsl()` 与 `hsla()`。

    - `'hsl(360, 100%, 100%)'`
    - `'hsl(360 100% 100%)'`
    - `'hsla(360, 100%, 100%, 1.0)'`
    - `'hsla(360 100% 100% / 1.0)'`

  - 色调-白度-黑度（HWB）：React Native 还支持函数表示法的 `hwb()`。

    - `'hwb(0, 0%, 100%)'`
    - `'hwb(360, 100%, 100%)'`
    - `'hwb(0 0% 0%)'`
    - `'hwb(70 50% 0%)'`

  - 颜色整数值：React Native 还支持将颜色表示为 `int` 值（以 RGB 颜色模式）。

    - `0xff00ff00` (0xrrggbbaa)

    > 注意 这看起来可能与 Android [Color](https://developer.android.com/reference/android/graphics/Color) 的整数表示类似，但在 Android 上，值是以 SRGB 颜色模式（0xaarrggbb）存储的。

  - 颜色名称：在 React Native 中你也可以使用颜色名称字符串作为值。

    > 注意：React Native 仅支持小写的颜色名称。不支持大写颜色名称。

    例如：`transparent`，这是 `rgba(0,0,0,0)` 的简写，与 [CSS3](https://www.w3.org/TR/css-color-3/#transparent) 一致。并且RN中的颜色名称的实现遵循 [CSS3/SVG 规范](https://www.w3.org/TR/css-color-3/#svg-color)：

    -  aliceblue (`#f0f8ff`)

    -  antiquewhite (`#faebd7`)

    -  aqua (`#00ffff`)

    -  aquamarine (`#7fffd4`)

      ...

#### 交互

#### 网络连接

