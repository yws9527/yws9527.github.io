
## VScode 丢失类型提示

### 原因

>问题出现在自动生成的 components.d.ts 文件中的 declare module '@vue/runtime-core' 声明，在 pnpm 中只能访问项目的顶级依赖，而 @vue/runtime-core 是 vue 模块下的依赖，不是顶级依赖，导致声明语句失效。（yarn 和 npm 的 node_modules 平铺目录结构允许访问所有依赖）

### 解决方案

》》》方案一：
- Volar 同时也读取了 vue 模块中的 GlobalComponents 类型，如果你愿意手动维护 components.d.ts 文件，直接将声明改成 declare module 'vue' 即可。

》》》方案二：
- 可以直接引入 pnpm add @vue/runtime-core -D 依赖，让声明语句生效即可，但是要注意引入的依赖版本应该与 vue 的版本相同，声明语句才会生效在正确的模块。

》》》方案三（推荐）：
- 推荐在项目根目录添加 .npmrc 配置文件，在文件里写入\
`public-hoist-pattern[]=@vue/runtime-core` \
将@vue/runtime-core提升至根目录，也可以写入\
`shamefully-hoist=true`\
将所有依赖都提升至根目录（不推荐），\
然后执行 `pnpm i`

》》》方案四：
- 使用Volar 0.18.5 版本

》》》方案五：
- 修改 `tsconfig.json` ，\
  对于NaviUI的修改：`"types": [..., "naive-ui/volar"]`;\
  对于ElementPlus的修改：`"types": [..., "element-plus/global"]`;