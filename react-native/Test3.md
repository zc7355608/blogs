### 界面与交互

#### 界面

- ##### 样式：

  在 React Native 中，你并不需要学习什么特殊的语法来定义样式。我们仍然是使用 JS 来写样式。所有的核心组件都接受名为`style`的属性。这些样式名基本上是遵循了 web 上的 CSS 的命名，只是按照 JS 的语法要求使用了驼峰命名法，例如将`background-color`改为`backgroundColor`。

  > 注意：RN中**没有CSS的继承**，而且**不支持CSS的渐变和CSS变量**。

  `style`属性可以是一个普通的 JS 对象，这是最简单的用法。而**在RN中，`style`属性的值还可以是一个数组——在数组中位置居后的样式对象比居前的优先级更高**，这样你可以间接实现样式的继承。

  实际开发中组件的样式会越来越复杂，我们建议使用`StyleSheet.create`来集中定义组件的样式。比如像下面这样：

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

  > `StyleSheet.create()` 其实是 React Native 官方提供的“样式注册器 + 样式校验器 + 优化工具”，是**官方的最佳实践和代码规范**。

  现在你已经了解如何调整文本样式了，下面我们要学习的是如何控制组件的尺寸。

- ##### 高度与宽度：

  组件的高度和宽度决定了其在屏幕上显示的尺寸。

  最简单的给组件设定尺寸的方式就是在样式中指定固定的`width`和`height`。**React Native 中的尺寸（数字）都是无单位的，表示的是与设备像素密度无关的逻辑像素点**。而且尺寸也可以用百分比字符串来表示。

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

  React Native 中的 Flexbox 的工作原理和 web 上的 CSS 基本一致，当然也存在少许差异。首先是默认值不同：`flexDirection`的默认值为`column`（而不是`row`），`alignContent`默认值为 `flex-start`（而不是 `stretch`）, `flexShrink` 默认值为`0` （而不是`1`）, 而`flex`只能指定一个数字值。

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

  xxx

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

