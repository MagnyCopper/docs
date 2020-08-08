# 介绍

这里是我平时积攒的一点心得体会，没有什么具体的格式，想到什么就写什么吧，留个纪念。

## 开发环境部署

1. 安装git工具；
2. 克隆工程到本地磁盘目录`git clone https://github.com/MagnyCopper/docs.git`;
3. 安装Node.js最好使用稳定版；
4. 执行npm安装命令，安装gitbook-cli`npm install -g gitbook-cli`;
5. 执行安装gitbook到本地`gitbook install`；
6. 运行本地调试命令`gitbook serve`后，使用浏览器访问`http://localhost:4000`

## Markdown语法

### 标题

```markdown
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题
```

### 字体

```markdown
**这是加粗的文字**
*这是倾斜的文字*`
***这是斜体加粗的文字***
~~这是加删除线的文字~~
```

### 引用

```markdown
> 这是引用内容
```

### 代码

```markdown
`代码内容`
```

### 分割线

```markdown
---
***
```

### 图片

```markdown
![图片alt](图片地址 图片title)
```

### 超链接

```markdown
[超链接名](超链接地址 "超链接title")
```

### 列表

- 无序列表

    ```markdown
    - 列表内容
    + 列表内容
    * 列表内容
    ```

- 有序列表

    ```markdown
    1. 列表内容
    2. 列表内容
    3. 列表内容
    ```

### 表格

```markdown
姓名|技能|排行
--|:--:|--:
刘备|哭|大哥
关羽|打|二哥
张飞|骂|三弟
```