# Git 提交规范



##### 1. 提交格式

```bash
type(<scope>): subject

<Body>

<Footer>
```



##### 2. type

用于说明 commit 的提交类型（必须是以下几种之一）。

- feat 新增功能
- fix 修复 bug
- docs 文档变更
- style 代码格式（不影响功能，例如空格、分号等格式修正）
- refactor 代码重构
- perf 改善性能
- test 测试
- build 变更项目构建或外部依赖（例如 scopes: webpack、gulp、npm 等）
- ci 更改持续集成软件的配置文件和 package 中的 scripts 命令，例如 scopes: Travis, Circle 等
- chore 变更构建流程或辅助工具
- revert 代码回退



##### 3. scope

- scope 用于指定本次 commit 影响的范围（可省略）。



##### 4.subject

- subject 是本次 commit 的简洁描述，长度约定在 50 个字符以内，通常遵循以下几个规范：
- 用动词开头，第一人称现在时表述，例如：change 代替 changed 或 changes
- 第一个字母小写
- 结尾不加句号（.）



##### 5. Body

- body 是对本次 commit 的详细描述，可以分成多行。（body 可省略）
- 跟 subject 类似，用动词开头，body 应该说明修改的原因和更改前后的行为对比。



##### 6. Footer

- 如果本次提交的代码是**突破性的变更**或**关闭缺陷**，则 Footer 必需，否则可以省略。



##### 突破性的变更

- 当前代码与上一个版本有突破性改变，则 Footer 以 BREAKING CHANGE 开头，后面是对变动的描述、以及变动的理由。



##### 关闭缺陷

- 如果当前提交是针对特定的 issue，那么可以在 Footer 部分填写需要关闭的单个 issue 或一系列 issues。



##### 可用`scope`

- Terminal（终端模块）
- CRUD（一键CRUD相关）
- baTable（通用表格）
- baInput（通用表单）
- install（安装）
- utils（工具）
- layout（布局）
- lang（语言包）