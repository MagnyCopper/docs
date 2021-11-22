# 介绍

这里是我平时积攒的一点心得体会，没有什么具体的格式，想到什么就写什么吧，留个纪念。

## 开发环境部署

1. 安装 git 工具；
2. 克隆工程到本地磁盘目录`git clone https://github.com/MagnyCopper/docs.git`;
3. 安装 Node.js；
4. 执行 npm 安装命令，安装 gitbook-cli`npm install -g gitbook-cli`;
5. 执行安装 gitbook 到本地`gitbook install`；
6. 若遇到以下错误:

   ```shell
   TypeError: cb.apply is not a function
       at C:\Users\hanch\AppData\Roaming\npm\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287:18
   ```

   则修改 polyfills.js 文件,注释掉以下内容:

   ```js
   fs.stat = statFix(fs.stat);
   fs.fstat = statFix(fs.fstat);
   fs.lstat = statFix(fs.lstat);
   ```

7. 运行本地调试命令`gitbook serve`后，使用浏览器访问`http://localhost:4000`
