# GitBook in Github
GitBook用于文本类的文章写作，使用markdown语法进行编写。通过安装gitbook命令，将markdown文件生成为html的站点。Github中的每个用户可以设置托管一个静态站点，结合Github自带的Travis CI，可以配置成提交markdown文件的时候自动触发的生成静态文件更新站点。   
GitBook结合Github，可以完成编写markdown -> 提交到github -> 几分钟之后站点自动更新的效果。另外一个最大优势是这种发布方式是完全免费的！！

## gitbook介绍
gitbook是一个命令行工具。按照上述介绍，该命令行工具可以生成静态站点，结合markdown的语法，很方便的用于电子写作。GitBook的托管网站https://www.gitbook.com/ 提供了丰富的功能和界面用于管理电子写作的整个流程。不过对于国内来说，该网站访问较慢，需要梯子才能流程使用相关功能。作为一家商业公司，gitbook.com也会有一些繁琐的收费策略，当然这些收费功能会让我们的gitbook易于使用，如果有需要可以尝试用一下。如果要求不高，使用gitbook命令行工具，可以自行搭建个人的站点，方式多种多样，这里介绍的是根据https://github.com/riskers/blog/issues/48 做的一个二次整理，感谢riskers的分享。希望这篇another tutorial可以帮你解决问题。

## gitbook环境准备
### 安装gitbook命令行
- gitbook基于node.js，首先需要安装npm，在MacOS中使用brew install安装npm
- 然后使用npm install gitbook。gitbook插件下载较慢时，可以通过修改国内的源提高速度，比如改为淘宝源：`npm config set registry http://registry.npm.taobao.org/`
- 创建SUMMARY.md文件。在创建一个目录my_gitbook，在该目录下创建SUMMARY.md文件，用于描述整个电子书的目录。使用你喜欢的文本编辑工具，最好支持md的格式，编写SUMMARY.md的内容如下：
```
 # Summary
 * [About](README.md)
```
- 编写README.md。按照上述SUMMARY.md的介绍，我们需要定义一个README.md的文件，在SUMMARY.md的同级目录下创建README.md。内容如下：
```
 # My GitBook
 take notes & share programming skills.
```
- 生成静态html项目。在my_gitbook目录下，使用`gitbook install`更新依赖生成。使用`gitbook serve`在当前的_book下自动生成静态html文件。打开http://localhost:4000 会看到具体网站效果。
- 为gitbook添加插件。现在展示的内容应该比较单调，创建一个book.json文件，添加如下内容，为站点提供丰富的内容：
```
{
  "title": "LoranceChen Blog",
  "author": "LoranceChen",
  "plugins": [
    "-lunr",
    "-search",
    "search-pro",
    "-sharing",
    "github-buttons",
    "editlink",
    "-highlight",
    "prism",
    "bibtex-indexed-cite",
    "anchor-navigation-ex",
    "page-footer-ex",
    "splitter",
    "todo",
    "copy-code-button",
    "expandable-chapters-small",
    "donate",
    "expandable-chapters"
  ],
  "pluginsConfig": {
    "search-pro": {
      "cutWordLib": "nodejieba",
      "defineWord" : ["Gitbook Use"]
    },
    "anchor-navigation-ex": {
      "showLevel": false
    },
    "github-buttons": {
      "buttons": [{
        "user": "lorancechen",
        "repo": "gitbook-source",
        "type": "star",
        "size": "small",
        "count": true
      }]
    },
    "editlink": {
      "base": "https://github.com/lorancechen/gitbook-source/blob/master",
      "label": "Edit",
      "multilingual": false
    },
    "prism": {
      "ignore": [
        "mermaid",
        "eval-js"
      ],
      "css": [
        "prismjs/themes/prism-solarizedlight.css"
      ]
    },
    "page-footer-ex": {
      "copyright": "By [LoranceChen](https://github.com/LoranceChen)，使用[知识共享 署名-相同方式共享 4.0协议](https://creativecommons.org/licenses/by-sa/4.0/)发布",
      "markdown": true,
      "update_label": "此页面修订于：",
      "update_format": "YYYY-MM-DD HH:mm:ss"
    },
    "bibtex-indexed-cite": {
      "path": "/"
    },
    "donate": {
      "wechat": "/image/wechatpay.jpeg",
      "alipay": "/image/alipay.jpeg",
      "title": "给UP主加个鸡腿~~",
      "button": "打赏",
      "alipayText": "支付宝打赏",
      "wechatText": "微信打赏"
    }
  }
}
```
将其中的用户名修改为你自己的即可。插件的安装和配置是基于json格式，具体插件的配置方式可以自行查询资料详细了解。
- 重新生成html文件。使用`gitbook install`和`gitbook serve`重新生成站点，并在4000端口查看效果。

## 创建用于编写的markdown代码库
上面我们在my_gitbook目录下创建了基本的gitbook文件结构，这里来看下如果配置git，这里假设对git、github有基本的使用经验。
- 初始化git，并配置gitignore。使用git init初始化该目录，编辑.gitignore文件：
```
 node_modules
 _book
```
- 在github中新建一个gitbook-source的repo，并使用`git remote add origin https://github.com/{username}/gitbook-source.git`添加关联该repo。
- 使用`git add .`和`git push origin master`命令将该目录提交到github即可。

## github的travis配置
目前配置了gitbook-source仓库，用于编写markdown文件。另外我们需要另外一个仓库用户托管生成的html静态文件。托管html的服务一般叫做Pages服务，除了github，笔者尝试了当前其他国内的托管平台，发现对Pages或者对后续Travis CI的的push功能支持有限。这里依然github的具体配置流程做了相关记录：
- 在github中创建名为`{your username}.github.io`的仓库，用于托管生成的静态html资源，该命名规范为github的Pages服务的要求。Pages服务在当前的github的使用master分支。
- 配置github Travis。因为配置大部分为页面操作，具体截图可以按照：https://github.com/riskers/blog/issues/48 中的Travis一节操作。细分一下操作：
    - 生成github的授权token，配置token的权限。
    - 给Travis授权。在Travis中关联gitbook-source代码库：https://github.com/marketplace/travis-ci。
    - 配置github token，用于生成静态html站点后，push到github的代码库。
    
- 在my_gitbook中，创建`.travis.yaml`文件，内容参考如下：  
注意，yaml文件中的CNAME配置可以自定义域名，但要提前在域名提供商中做好CNAME的配置。

```
language: node_js
node_js:
  - "10"
cache: npm

notifications:
  email:
    recipients:
      - xxxxx@xx.com # 设置通知邮件
    on_success: change
    on_failure: always

install:
  - npm install -g gitbook-cli
  - gitbook install

script:
  - gitbook build

after_script:
  - cd _book
#  echo "gitbook.youdomain.com" >> CNAME
  - git init
  - git remote add origin https://${REF}
  - git add .
  - git commit -m "Updated By Travis-CI With Build $TRAVIS_BUILD_NUMBER For Github Pages"
  - git push --force --quiet "https://${TOKEN}@${REF}" master:master

branches:
  only:
    - master

env:
  global:
    - REF=github.com/lorancechen/{your username}.github.io.git # 设置 github 地址

```
- 测试效果。提交代码后可以看到travis正在跑CI，过几分钟查看`{your username}.github.io.git`仓库，验证是否提交完成。github对静态资源的缓存为10分钟，过10分钟后访问该http地址，验证内容是否生效：`http://{your username}.github.io`

- 其他：
    - 关于Travis CI，后续可以在https://travis-ci.com/ 用github账号登陆，查看项目的配置。
    - 具体的配置，可以参考我的gitbook-source仓库：https://github.com/LoranceChen/gitbook-source
    - 关于‘打赏’插件，在支付宝和微信中把指定了金额的收款二维码截图为jpg即可，然后配置在`book.json`中。

## 配置个人域名
- 上面travis中提到了，在`.travis.yaml`中添加`echo "gitbook.youdomain.com" >> CNAME`语句即可支持CNAME自定义域名的功能。github的Pages服务会读取代码仓库中的CNAME文件作为自定义的域名。

## 上传图片资源
html生成的静态资源可以引用gitbook-source中的相对路径，我们可以创建images目录来维护相关的图片资源。
- 创建image目录，添加my-image.jpg文件
- 在README.md中添加，`![my-image](image/my-image.jpg)` 即可看到效果。

## Best Practice
关于这个流程的最佳实践，有如下几点：
- 该流程适合写博客文章等需要一定时间思考才能编写出来的流程，不适合简单的做todo list记录等临时的笔记需求。
- 编写文章时，先在本地打开`gitbook serve`，可以在本地实时预览效果，防止发布之后发现排版等问题。

## 总结
这里通过gitbook+github，实现了一个简单的个人文章展示平台。目前市面有很多博客平台，但都需要在各自平台上做内容编辑。类似于gitbook将这种模式更加尊重文章编辑本身，而把平台弱化了，期望后续各个博客平台能够支持gitbook，更好的把文章的编写交给作者自己，而平台自身也能更专注于展示内容。
