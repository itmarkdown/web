## 背景
>俗话说，工欲善其事，必先利其器，使用一些优秀的工具，能起到事半功倍的效果。
在开发团队中，每个人的编码风格都不一样，如何让代码看上去规范统一，像出自一人之手呢？

----
### 准备
VSCode安装如下插件
- Vetur
- ESClient
- Prettier

1. **安装方式1**
   VSCode的插件市场里搜索相关插件，安装即可使用
![prettier插件](https://upload-images.jianshu.io/upload_images/8533392-52713b566e4a942f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

2. **安装方式2**
   通过命令行，局部安装``` npm install eslint ```或全局安装 ```npm install -g eslint```
----
### 配置
- 保存时自动格式化配置
  - Ctrl+Shift+P
![settings.json](https://upload-images.jianshu.io/upload_images/8533392-3e0f645813330673.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  - 直接修改settings.json添加一条记录，或通过UI界面修改
    ``` "editor.formatOnSave": true```

![UI界面修改配置图](https://upload-images.jianshu.io/upload_images/8533392-c61eb2d3d6504045.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 格式化配置
  1. 新建.prettierrc.js文件，放置在project根目录
![.prettierrc.js](https://upload-images.jianshu.io/upload_images/8533392-d812c5549a3faad2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

      ```json
      module.exports = {
         printWidth: 120, // 每行代码最大长度
         tabWidth: 4, //一个tab代表几个空格数，默认为80
         useTabs: false, //是否使用tab进行缩进，默认为false，表示用空格进行缩减
         semi: true, // 声明后带分号
         singleQuote: false, // 使用单引号
         jsxSingleQuote: true, // 使用单引号
         jsxBracketSameLine: true, // 启用jsx语法，> 放在末尾
         trailingComma: 'all',
      };
      ```
   2. 修改vue等类型文件的默认format方式
![vue.png](https://upload-images.jianshu.io/upload_images/8533392-85dbd649154e26ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      ```json
      "[vue]": {
         "editor.defaultFormatter": "esbenp.prettier-vscode"
      },
      "[html]": {
         "editor.defaultFormatter": "esbenp.prettier-vscode"
      },
      "[javascript]": {
         "editor.defaultFormatter": "esbenp.prettier-vscode"
      },
      "[typescript]": {
         "editor.defaultFormatter": "esbenp.prettier-vscode"
      },
      ```
----
### 效果
![screen.gif](https://upload-images.jianshu.io/upload_images/8533392-414428016f05adfb.gif?imageMogr2/auto-orient/strip)