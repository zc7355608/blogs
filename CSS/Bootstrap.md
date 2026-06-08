# Bootstrap

> Bootstrap是Twitter公司开发和维护的一个前端UI框架，它提供了大量写好的css样式，且支持响应时。开发者可以只编写html和js代码，就可以应用写好的css样式，快速完成网页的开发。常见的UI框架还有，ElementUI、EasyUI、LayUI等。
>
> 接下来我们学习，如何使用Bootstrap5框架来使用这些写好的样式：

#### 首先去bootstrap官网下载bootstrap的文件：

> 可以选择下载BootStrap源码，方便查看它内部的实现。用的话我们只下载Bootstrap生产文件bootstrap-4.6.2-dist/dist/即可，里面只有两个文件夹js和css，里面有js、css文件和用于做映射的map文件。
>
> 使用的话我们直接去官网查看文档使用，也可以下载Bootstrap的示例代码用于离线方式。

#### 使用：

1. 引入bootstrap.min.css核心的样式文件：

   ```html
   <link rel="stylesheet" href="../../../static/bootstrap-5.3.0-alpha1-dist/css/bootstrap.min.css">
   ```

2. 引入jquery库：（如果是bootstrap3，且用了组件中的js代码的话）

   ```html
   <script src="../../../static/js/jquery/jquery-3.7.1/"></script>
   ```

3. 引入bootstrap.min.js库：（如果需要用js的话）

   ```html
   <script src="../../../static/bootstrap-5.3.0-alpha1-dist/js/bootstrap.min.js"></script>
   ```

------

## 开始之前的重要的全局设置

> Bootstrap 依赖一些重要的全局样式和设置，专门针对的是跨浏览器的样式 *统一化（normalization）*，需要你在使用 Bootstrap 之前重点了解。

- ### Bootstrap 要求文档类型（doctype）是 HTML5。如果没有这一设置，你就会看到一些古怪的、不完整的样式，因此，正确设置文档类型（doctype）就能轻松避免这些困扰：

  ```html
  <!DOCTYPE html>
  <html lang="zh-CN">...</html>
  ```

- ### 响应式布局相关的标签：

  > Bootstrap 采用的是 *移动设备优先（mobile first）* 的开发策略，因此，我们首先为移动设备优化代码，然后根据需要并利用 CSS 媒体查询功能来缩放组件。为了确保所有设备都能支持正确的渲染和触屏缩放，请务必在 `<head>` 标签中 **添加让 viewport（视口）支持响应式布局的 标签**

  ```html
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  ```

- ### CSS 盒模型：

  > 为了让 CSS 中的尺寸设置更加直观，我们将全局的 `box-sizing` 从 `content-box` 调整为 `border-box`。这样可以确保 `padding` 的设置不会影响计算元素的最终宽度，但是会导致某些第三方软件（例如 Google Maps 和 Google Custom Search Engine）出现问题。在为数不多的情况下，你需要专门覆盖这一些设置，可以使用如下示例代码：

  ```css
  .selector-for-some-widget {
    box-sizing: content-box;
  }
  ```

  > 利用上述代码片段，嵌套的元素（包括通过 `::before` 和 `::after` 生成的内容）都将继承 `.selector-for-some-widget` 所指定的 `box-sizing` 值。

- ### Reboot：

> 为了提升跨浏览器的渲染效果，我们使用 [Reboot](https://v4.bootcss.com/docs/content/reboot/) 来纠正不同浏览器和设备之间的差异，并同时为常见的 HTML 元素提供更多更合适的样式重置。

### CSP（内容安全策略）和嵌入式 SVG：

> 某些 Bootstrap 组件在 CSS 中包含了嵌入式 SVG，以便能够让为组件设置的样式在浏览器和设备之间保持一致。**对于设置了更严格的 CSP 配置的组织**，我们在文档中对所有出现嵌入式 SVG（全部通过 `background-image` 设置）的地方都进行了说明，因此你可以更好的控制：
>
> - [关闭按钮](https://v4.bootcss.com/docs/utilities/close-icon/) （用于警告框和模态框）
> - [自定义复选框和单选按钮](https://v4.bootcss.com/docs/components/forms/#custom-forms)
> - [表单开关](https://v4.bootcss.com/docs/components/forms/#switches)
> - [表单验证图标](https://v4.bootcss.com/docs/components/forms/#validation)
> - [自定义选择菜单](https://v4.bootcss.com/docs/components/forms/#select-menu)
> - [轮播控件](https://v4.bootcss.com/docs/components/carousel/#with-controls)
> - [导航条切换按钮](https://v4.bootcss.com/docs/components/navbar/#responsive-behaviors)

------

## Bootstrap的栅格系统Grid system

> bootstrap的栅格系统是专门用来实现响应式布局的。栅格化是指，将整个网页的宽度分成12等份，算出每个盒子占用所占用的份数。如果我们在视口宽度不同的情况下，设置每个盒子占用不同的份数，那么就实现了响应式布局了。那么我们怎么划分不同的视口范围呢？栅格系统给我们规定好了：
>
> <576px是极小范围None；≥576px是小范围sm；≥768px是中等范围md；≥992px是大范围lg；≥1200px是超大范围xl；≥1400px是及其大范围xxl；
>
> 要做响应式布局，还需要做到以下2点：
>
> 1. 在不同的区间，版心要不同。要在版心中来使用栅格系统。（bootstrap写好了版心类【container】）
>
> 2. 在不同的屏幕范围，内部的直接子元素要加不同的类名。（例如类名【col-sm-4】）
>
>    （如果要想多个块元素在一行，需要给它们的父元素加类名【row】，row这个类有`display: flex`这个属性的）

------

## 全局样式（使用前先加类名清除掉默认样式）

- 按钮的样式：（使用前首先给按钮加【btn】类名来清除默认样式）

  > btn：清除默认样式
  >
  > btn-success：绿色成功按钮
  >
  > btn-warning：红色警告按钮
  >
  > btn-lg/btn-sm：按钮的尺寸
  >
  > ...

- 表格的样式：（使用前首先给表格加【table】类名来清除默认样式）

  > table：清除默认样式
  >
  > table-striped：隔行变色
  >
  > table-bordered：表格加边框
  >
  > ...

------

## 组件（如果组件用到了js代码，需要提前引入js库。bootstrap3还得提前引入jquery库）

> 组件就是对通用功能或效果进行了封装，封装内容有html、css和js。使用组件可以更快的开发网页。

------

## 字体图标

1. 首先在官网下载字体图标包【bootstrap-icons-1.11.3.zip】，里面有每个图标的svg图还有font目录，我们用的就是这个font。
2. 使用`link`引入`bootstrap-icons.min.css`文件。（也可以直接引入在线网址，这样就不用下载了）
3. 使用`i`或`span`标签，class属性设置为字体图标名即可。

------

接下来用`Bootstrap`做一个响应式的页面

