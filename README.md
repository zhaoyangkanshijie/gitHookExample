# gitHookExample

## 参考链接

* [Git英文文档](https://git-scm.com/)
* [约定式提交](https://www.conventionalcommits.org/zh-hans/v1.0.0-beta.4/#%e7%ba%a6%e5%ae%9a%e5%bc%8f%e6%8f%90%e4%ba%a4%e8%a7%84%e8%8c%83)
* [手写 git hooks 脚本（pre-commit、commit-msg）](https://juejin.cn/post/6986426226248253476)
* [规范化团队 git 提交信息](https://juejin.cn/post/6844904080276455437)
* [GitHook 工具 —— husky（格式化代码）](https://juejin.cn/post/6947200436101185566)
* [一文带你彻底学会 Git Hooks 配置](https://juejin.cn/post/6844904194634153992)
* [你真的懂package.json吗](https://www.jianshu.com/p/ceaa3c265895)
* [NPM：常用命令的生命周期脚本](https://www.cnblogs.com/f1194361820/p/12509761.html)
* [husky](https://typicode.github.io/husky)
* [[前端采坑]lint-staged就是匹配不到文件](https://zhuanlan.zhihu.com/p/102104085)

## 目录

* [GitHook介绍](#GitHook介绍)
* [提交规范](#提交规范)
* [工具](#工具)
* [husky](#husky)
* [lint-staged](#lint-staged)
* [commitlint](#commitlint)

---

## GitHook介绍

* 有什么用？

    Git 能在特定的重要动作发生时触发自定义脚本，其中比较常用的有：pre-commit、commit-msg、pre-push 等钩子（hooks）。我们可以在 pre-commit 触发时进行代码格式验证，在 commit-msg 触发时对 commit 消息和提交用户进行验证，在 pre-push 触发时进行单元测试、e2e 测试等操作。

* 怎么用？

    Git 在执行 git init 进行初始化时，会在 .git/hooks 目录生成一系列的 hooks 脚本，每个脚本的后缀都是以 .sample 结尾的，在这个时候，脚本是不会自动执行的。我们需要把后缀去掉之后才会生效，即将 pre-commit.sample 变成 pre-commit 才会起作用。

    用于编写 git hooks 的脚本语言是没有限制的，你可以用 nodejs、shell、python、ruby等脚本语言，非常的灵活方便。

* git钩子列表

```txt
Git Hook                 调用时机                                           说明    
pre-applypatch           git am执行前                              
applypatch-msg           git am执行前                              
post-applypatch          git am执行后                                       不影响git am的结果             
pre-commit               git commit执行前                                   可以用git commit --no-verify绕过             
commit-msg               git commit执行前                                   可以用git commit --no-verify绕过             
post-commit              git commit执行后                                   不影响git commit的结果             
pre-merge-commit         git merge执行前                                    可以用git merge --no-verify绕过。             
prepare-commit-msg       git commit执行后，编辑器打开之前                              
pre-rebase               git rebase执行前                              
post-checkout            git checkout或git switch执行后                      如果不使用--no-checkout参数，则在git clone之后也会执行。             
post-merge               git commit执行后                                   在执行git pull时也会被调用             
pre-push                 git push执行前                              
pre-receive              git-receive-pack执行前                              
update                                               
post-receive             git-receive-pack执行后                             不影响git-receive-pack的结果             
post-update              当 git-receive-pack对 git push 作出反应并更新仓库中的引用时                              
push-to-checkout         当git-receive-pack对git
push做出反应并更新仓库中的引用时，以及当推送试图更新当前被签出的分支且receive.denyCurrentBranch配置被设置为updateInstead时       
pre-auto-gc              git gc --auto执行前                              
post-rewrite             执行git commit --amend或git rebase时                              
sendemail-validate       git send-email执行前                              
fsmonitor-watchman       配置core.fsmonitor被设置为.git/hooks/fsmonitor-watchman或.git/hooks/fsmonitor-watchmanv2时
p4-pre-submit            git-p4 submit执行前                                可以用git-p4 submit --no-verify绕过             
p4-prepare-changelist    git-p4 submit执行后，编辑器启动前                    可以用git-p4 submit --no-verify绕过             
p4-changelist            git-p4 submit执行并编辑完changelist message后        可以用git-p4 submit --no-verify绕过             
p4-post-changelist       git-p4 submit执行后                              
post-index-change        索引被写入到read-cache.c do_write_locked_index后   
```

* npm钩子

```txt
发布npm包：npm publish 命令的生命周期会执行的脚本顺序：prepublish > prepare > prepublishOnly > publish > postpublish
将可安装内容提取到缓存：npm pack 命令的生命周期会执行的脚本顺序：prepare > prepack > postpack
安装：npm install 命令的生命周期会执行的脚本顺序：prepare > preinstall > install > postinstall
卸载：npm uninstall 命令的生命周期会执行的脚本顺序：preuninstall > uninstall > postuninstall
查看版本：npm version 命令的生命周期会执行的脚本顺序：preversion > version > postversion
运行程序包测试脚本：npm test 命令的生命周期会执行的脚本顺序：pretest > test > posttest
启动：npm start 命令的生命周期会执行的脚本顺序：prestart > start > poststart
停止：npm stop 命令的生命周期会执行的脚本顺序：prestop > stop > poststop
重启：npm restart 命令的生命周期会执行的脚本顺序：prerestart > restart > postrestart
锁定包版本：npm shinkwrap 命令的生命周期会执行的脚本顺序：preshinkwrap > shinkwrap > postshinkwrap
```

## 提交规范

1. 规范

    * 分类type
        * build：修改项目的的构建系统（xcodebuild、webpack、gulp等）的提交
        * ci：修改项目的持续集成流程（Kenkins、Travis等）的提交
        * chore：构建过程或辅助工具的变化
        * docs：文档提交（documents）
        * feat：新增功能（feature）
        * fix：修复 bug
        * pref：性能、体验相关的提交
        * refactor：代码重构
        * revert：回滚某个更早的提交
        * style：不影响程序逻辑的代码修改、主要是样式方面的优化、修改
        * test：测试相关的开发

    * 影响范围scope

        * route
        * component
        * utils
        * build
        * 等等

    * 概述subject

    * 具体修改内容body

    * 备注footer

2. 例子

    * feat

    ```txt
    feat(browser): onUrlChange event (popstate/hashchange/polling)

    Added new event to browser:
    - forward popstate event if available
    - forward hashchange event if popstate not available
    - do polling when neither popstate nor hashchange available

    Breaks $browser.onHashChange, which was removed (use onUrlChange instead)
    ```

    * fix

    ```txt
    fix(compile): couple of unit tests for IE9

    Older IEs serialize html uppercased, but IE9 does not...
    Would be better to expect case insensitive, unfortunately jasmine does
    not allow to user regexps for throw expectations.

    Closes #392
    Breaks foo.bar api, foo.baz should be used instead
    ```

    * style

    ```txt
    style(location): add couple of missing semi colons
    ```

    * chore

    ```txt
    chore(release): v3.4.2
    ```

[更多](https://github.com/zhaoyangkanshijie/record/blob/master/%E5%89%8D%E7%AB%AF%E5%9F%BA%E5%BB%BA%E9%97%AE%E9%A2%98.md)

## 工具

1. 手写见hooks文件夹代码

2. husky：Git hooks 工具

    对git执行的一些命令，通过对应的hooks钩子触发，执行自定义的脚本程序

3. lint-staged：检测文件插件

    只检测git add . 中暂存区的文件，对过滤出的文件执行脚本

4. eslint：插件化JavaScript代码检测工具

    js编码规范，检测并提示错误或警告信息

5. prettier：代码格式化工具

    代码风格管理，更好的代码风格效果

6. editorconfig：文件代码规范

    保持多人开发一致编码样式

7. commitlint：代码提交检测

    检测git commit 内容是否符合定义的规范

8. commitizen：代码提交内容标准化

    提示定义输入标准的git commit 内容

9. eslint-plugin-vue

    vue.js的Eslint插件（查找vue语法错误，发现错误指令，查找违规风格指南）

10. eslint-plugin-prettier

    运行更漂亮的Eslint，使prettier规则优先级更高，Eslint优先级低

11. eslint-config-prettier

    让所有可能与prettier规则存在冲突的Eslint rules失效，并使用prettier进行代码检查

12. @babel/eslint-parser

    * 该解析器允许你使用Eslint校验所有babel code
    * 仅支持最新的最终ECMAScript标准，不支持实验性语法
    * 该编译器会将code解析为Eslint能懂的EsTree（ES2021语法等等）

## husky

* 安装：npm install husky --save-dev

* 创建文件：npx husky install

* 建立命令：npm set-script prepare "husky install"

* 创建hook和命令：npx husky add .husky/pre-commit "npm test"

* 卸载：npm uninstall husky && git config --unset core.hooksPath

* 自定义安装路径：husky install .config/husky

* git跳过检查：git commit -m "yolo!" --no-verify

* husky跳过检查：HUSKY=0 git push # yolo!

* 测试hook：exit 1

* 设置hook生效目录：git config core.hooksPath

* mac添加权限：chmod +x .husky/{hookname}

## lint-staged

* 安装：npm install lint-staged --save-dev

* package.json配置

  ```json
  "scripts": {
    "precommit": "lint-staged"
  },
  "lint-staged": {
    "src/*.{js,jsx,ts,tsx}": [//要处理的文件路径和执行命令
      "prettier --write",
      "eslint --fix"
    ],
    "src/*.{css,scss}": [
      "prettier --write"
    ]
  }
  ```

* husky pre-commit 添加代码

  ```shell
  npm run precommit
  ```

* 注意

  lint-staged只对git add后暂存区有变化的文件进行检测

## commitlint

* 安装：npm install --save-dev @commitlint/config-conventional @commitlint/cli

* 配置

  根目录commitlint.config.js
  ```js
  module.exports = {
    extends: [
      "@commitlint/config-conventional"
    ],
    rules: {
      'type-enum': [2, 'always', [
        'upd', 'feat', 'fix', 'refactor', 'docs', 'chore', 'style', 'revert'
      ]],
      'type-case': [0],
      'type-empty': [0],
      'scope-empty': [0],
      'scope-case': [0],
      'subject-full-stop': [0, 'never'],
      'subject-case': [0, 'never'],
      'header-max-length': [0, 'always', 72]
    }
  };
  ```

* husky commit-msg 添加代码

  ```shell
  ./node_modules/.bin/commitlint --edit
  ```

* 提交样例

  ```git
  git commit -m 'feat: 增加 xxx 功能'
  git commit -m 'bug: 修复 xxx 功能'
  ```

