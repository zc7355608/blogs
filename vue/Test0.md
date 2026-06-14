# 项目的配置（基于Vite）

> 以下配置以后会有变动，用的时候去对应官网查即可。

- ### ESLint的配置：

  > Eslint是可组装的 JavaScript 和 JSX 检查工具（Facebook公司的）。我们用Eslint关键是写Eslint的配置文件，里面写上各种rules规则，将来运行Eslint进行语法检查时就可以根据写的规则对JS/TS代码进行检查了。

  1. 安装：`pnpm create @eslint/config@latest`（或`npm init @eslint/config@latest`）

     > 运行之后，项目根目录下会初始化一个eslint的配置文件`eslint.config.js`：（这是最新的配置文件格式。忽略某些文件不进行语法检查也可以在该文件中配置，或者用`.eslintignore`文件进行配置）
     >
     > ```js
     > import globals from "globals";
     > import pluginJs from "@eslint/js";
     > import tseslint from "typescript-eslint";
     > import pluginVue from "eslint-plugin-vue";
     > 
     > export default [
     >       {files: ["**/*.{js,mjs,cjs,ts,vue}"]},
     >       {languageOptions: { globals: globals.browser }},
     >       pluginJs.configs.recommended,
     >       ...tseslint.configs.recommended,
     >       ...pluginVue.configs["flat/essential"],
     >       {files: ["**/*.vue"], languageOptions: {parserOptions: {parser: tseslint.parser}}},
     >       {rules: {"no-var": "error", "quotes": ["error","single"]}}
     > ];
     > ```
     >
     > 其中`rules`配置了2个规则，分别是不能使用var声明变量、以及字符串必须用单引号。其他规则参考官网。

  2. package.json中配置运行脚本：（也可以直接执行`pnpm exec eslint 文件1 文件2`）

     ```json
     "scripts": {
         "lint": "eslint src/**", // 校验src下的代码
         "fix": "eslint --fix src/**" // --fix参数是检查并纠正代码
     }
     ```
     
     > 运行ESLint进行语法检查：`npx eslint [options] [file|dir|glob]*`

- ### Prettier的配置：

  > - 有了ESLint，为什么还需要Prettier？
  > - ESLint针对的是JS，它是一个检测工具，包含JS语法检查以及少部分格式检查，在ESLint看来，语法对了就能保证代码正常运行，格式问题属于其次；而Prettier属于格式化工具，它看不惯格式不统一，所以它就把ESLint没干好的事接着干，它是专业的格式化工具。
  > - 并且Prettier支持多种语言：JS/JSX/VUE/TS/CSS/LESS/SCSS/HTML/JSON/YAML/MD/MDX....
  > - 总之：ESLint保证代码质量，Prettier保证代码美观。
  > - 通过使用Prettier，可以使代码库中的所有成员的代码保持同一种风格，方便维护及后续的工作。

  1. 安装依赖：`pnpm add --save-dev --save-exact prettier`

  2. 编写Prettier的配置文件`.prettierrc.json`：

     ```json
     {
         "trailingComma": "all",
         "htmlWhitespaceSensitivity": "ignore",
         "endOfLine": "auto",
         "bracketSpacing": true,
         "tabWidth": 2,
         "semi": false, // 不要分号
         "singleQuote": true // 单引号
     }
     ```

  3. 编写Prettier的忽略文件`.prettierignore`：

     ```tex
     /dist/*
     .local
     /node_modules/*
     **/*.svg
     **/*.sh
     *.html
     /public/*
     ```

  4. package.json中配置运行脚本：（也可以直接执行`pnpm exec prettier --write src/`）

     ```json
     "scripts": {
         "format": "prettier --write ." // 格式化项目中所有内容，项目大时会需要点时间
     }
     ```

     > `--check`参数类似于`--write`，但只是检查文件是否已经格式化，不会进行格式化。（这俩参数最常用）

- ### Stylelint的配置：

  > Stylelint是一个强大、先进的CSS代码检查器（linter），可以帮助你检查CSS代码中的错误、格式化CSS、指定CSS的书写顺序，它就是一个CSS的语法检查及格式化工具。

  1. 安装：`pnpm create stylelint`，此时项目根目录下会生成`.stylelintrc.json`配置文件。
  2. 执行命令去检查CSS样式：`pnpm exec stylelint "**/*.css"`，（加`--fix`参数可以格式化CSS代码）

  > Stylelint的其他用法参考官网即可。

- ### Husky的配置：

  > Husky可以触发git程序的钩子，让git在提交前帮我们执行`pnpm format`等格式化命令。使用Husky编写脚本，在代码提交之前，自动对代码进行格式化。这样远程仓库中的代码就非常规范了。

  1. 安装：`pnpm add --save-dev husky`

  2. 执行：`pnpm exec husky init`

     > `init`命令简化了项目中的 husky 设置。它会在 `.husky/` 中创建 `pre-commit` 脚本（就是git在客户端保留的钩子），并更新 `package.json` 中的 `prepare` 脚本。其中`pre-commit` 脚本文件中编写提交前要执行的命令。

- ### commitlint的配置：

  > 对于我们的提交信息，也是有统一的规范的，不能随便写，要按照标准来写提交信息。可以通过commitlint来实现：

  1. 安装：`pnpm add @commitlint/config-conventional @commitlint/cli -D`

  2. 编写commitlint的配置文件`commitlint.config.cjs`：

     ```js
     export default {
         extends: ['@commitlint/config-conventional'],
         rules: {
         	'type-enum': [
         		2,
         		'always',
         		['feat','fix','docs','style','refactor','perf','test','chore','revert','build',]
     	    ],
     		'type-case': [0],
     		'type-empty': [0],
     		'scope-empty': [0],
     		'scope-case': [0],
     		'subject-full-stop': [0, 'never'],
     		'subject-case': [0, 'never'],
     		'header-max-length': [0, 'always', 72],
         },
     };
     ```

  3. 配置Husky：`echo "pnpm dlx commitlint --edit \$1" > .husky/commit-msg`，此时`.husky/`中多了一个 `commit-msg` 脚本。（内容为：`pnpm dlx commitlint --edit \`，且该文件必须为utf8编码格式）

     > 这样在提交时，前面就必须加上上面配置的关键字（feat: xxx）了，否则就会提交失败。（其他查官网即可）

- ### 强制使用某个包管理器：

  > 团队开发项目时，需要统一包管理器工具，因为不同的包管理器下载的依赖版本可能不同，最终导致项目跑不起来。因此包管理工具需要统一管理。

  1. 在项目根目录下新建`scripts/preinstall.js`文件，添加以下内容：

     ```js
     if (!/pnpm/.test(process.env.npm_execpath || '')) {
         console.warn(
             `\u001b[33mThis repository must using pnpm as the package manager`+
             ` for scripts to work property.\u001b[39m\n`
         )
         process.exit(1)
     }
     ```

  2. package.json中配置脚本：

     ```json
     "scripts": {
     	"preinstall": "node ./scripts/preinstall.js"
     }
     ```

     > 此时我们在使用npm或yarn来安装包的时候，就会报错了。原因是在install安装时会触发preinstall脚本的执行（这是包管理器工具提供的生命周期钩子）。

------

