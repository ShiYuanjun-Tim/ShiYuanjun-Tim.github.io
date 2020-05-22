[TOC]

# 关于node运行环境中`process.env.xxx`各种变量来源的解释

> 以mac作为运行环境 win上只是语法差异本质一样

## 案例

`package.json` 中的脚本  `npm run x`

```json
...
"name":"foo",
"version": "0.1.1",
"license": "ISC",
"scripts": {
    "x":"node x.js",
  },
"config" : { "port" : "8080" }
 "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ]
  },
...

```

脚本`x.js`中内容如下

```javascript
console.log(process.env) // 环境变量
```

如果我们执行脚本  `npm run x` 打印的`process.env` 内容有很多，摘要如下

```js
{
 // 第1部分
  TERM_PROGRAM: 'vscode',
  NODE: '/Users/syj/.nvm/versions/node/v12.9.0/bin/node',
  INIT_CWD: '/Users/syj/WS/sample-h5-project-in-mobile-app',
  NVM_CD_FLAGS: '',
  TERM: 'xterm-256color',
  SHELL: '/bin/bash',
  HOMEBREW_BOTTLE_DOMAIN: 'https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles',
  TMPDIR: '/var/folders/gh/4gs9hn0106vfxgmvknyjst3w0000gn/T/',
  TERM_PROGRAM_VERSION: '1.44.2',
  NVM_DIR: '/Users/syj/.nvm',
  USER: 'syj',
  SSH_AUTH_SOCK: '/private/tmp/com.apple.launchd.MbDiVFegdw/Listeners',
  __CF_USER_TEXT_ENCODING: '0x1F5:0x19:0x34',
  PATH: '/Users/syj/.nvm/versions/node/v12.9.0/lib/node_modules/npm/node_modules/npm-lifecycle/node-gyp-bin:/Users/syj/WS/sample-h5-project-in-mobile-app/node_modules/.bin:/Applications/Android Studio.app/Contents/jre/jdk/Contents/Home/bin:/usr/local/opt/ruby/bin:/Users/syj/.nvm/versions/node/v12.9.0/bin:/usr/local/bin:....',
  _: '/Users/syj/.nvm/versions/node/v12.9.0/bin/node',
  JAVA_HOME: '/Applications/Android Studio.app/Contents/jre/jdk/Contents/Home',
  LANG: 'zh_CN.UTF-8',
  XPC_FLAGS: '0x0',
  XPC_SERVICE_NAME: '0',
  SHLVL: '4',
  HOME: '/Users/syj',
  LOGNAME: 'syj',
  NVM_BIN: '/Users/syj/.nvm/versions/node/v12.9.0/bin',
.......
// 第2部分
  npm_package_version: '0.1.1',
  npm_package_license: 'ISC',
  npm_package_scripts_x: 'node x.js fromnode "fromnpm"',
  npm_package_config_port: '8080',
  npm_package_browserslist_production_1: 'not dead',
  npm_package_browserslist_production_0: '>0.2%',
  npm_package_browserslist_production_2: 'not op_mini all',
.......
// 第3部分
  npm_config_https_proxy: '',
  npm_config_registry: 'http://registry.npmjs.org/',
  npm_config_globalconfig: '/Users/syj/.nvm/versions/node/v12.9.0/etc/npmrc',
  npm_config_userconfig: '/Users/syj/.npmrc',
  npm_config_init_module: '/Users/syj/.npm-init.js',
  npm_config_save_dev: '',
  npm_config_legacy_bundling: '',
  npm_config_dry_run: '',
  npm_config_save_exact: '',
  npm_config_group: '20',
  npm_config_fetch_retry_factor: '10',
  npm_config_dev: '',
  npm_config_version: '',
  npm_config_prefer_offline: '',
  npm_config_cache_lock_stale: '60000',
  npm_config_otp: '',
  npm_config_cache_min: '10',
  npm_config_searchexclude: '',
  npm_config_cache: '/Users/syj/.npm',
  npm_config_color: 'true',
  npm_config_proxy: '',
  npm_config_package_lock: 'true',
  npm_config_package_lock_only: ''
  npm_config_viewer: 'man',
  npm_config_only: '',
  npm_config_commit_hooks: 'true',
  npm_config_browser: '',
  npm_config_also: '',
  npm_config_sign_git_commit: '',
  npm_config_rollback: 'true',
  npm_config_usage: '',
  npm_config_audit: 'true',
  npm_config_globalignorefile: '/Users/syj/.nvm/versions/node/v12.9.0/etc/npmignore',
  npm_config_shell: '/bin/bash',
  npm_config_maxsockets: '50',
  npm_config_init_author_url: '',
  npm_config_shrinkwrap: 'true',
    ... 
}
```

### 注意

如果不是通过 执行`package.json`中脚本运行，而是直接运行`node x.js`的话输出内容会大不一样

> 只有输出上面  *第1部分* 的内容，想想为什么

## 内容分3大类：

### Node环境变量 （ 特征是全大写）

#### 如何设置环境变量 

> 按照灵活性来排序的话

##### 单次环境变量

`temp=1 npm run x`

##### 临时环境变量

```
export tempvar=2
unset tempvar
npm run x
```

#####  .env

> dotenv npm package支持将环境变量写入.env 文件

##### 系统级环境变量

通过修改各种系统级配置 文件 来达到目的：

```bash
# 所有用户都生效
vim /etc/profile
# 当前用户生效
vim ~/.bash_profile
```



### package参数npm_package_xxx

#### 变量来源

`package.json` 中的内容会被转换成这些参数,`npm_package_`打头，把属性访问符替换为下   **_** 

#### `package.json`中`config`有不一样的意义

当前配置来说 process.env中会存在这么一个值

```js
npm_package_config_port: '8080'
```

他是根据package.json中config而来 

#### 修改

修改格式

```js
npm config set <package name>:<property in config> newValue
```

```shell
//一上面这个port举例举例
npm config set foo:port 80
```

```js
bash-3.2$ npm config ls -l
...
; userconfig /Users/syj/.npmrc
foo:port = "80"
...
```

```js
 //process.env 内容为
npm_config_foo_port: '80',
npm_package_config_port: '80',
   
```

### npm配置参数npm_config_xxx 

一般来说`npm config`命令就是用来读取删除更新这些变量的。也可用用`npm config ls -l`查看所有默认的config集合,  

#### 变量来源 [npmrc](https://docs.npmjs.com/configuring-npm/npmrc.html)

有4处来源， 优先级由高到低可以覆盖配置：

- 命令行级别 --flag=val  

  >```
  >$npm run x --zzz=876
  >可以有如下变量
  >npm_config_zzz: '876'
  >```

- 项目级别 (`/path/to/my/project/.npmrc`) - 一般都是在醒目的根路径下面，和`package.json`同级

  > 全局模式下，该配置是不被读取的，也就是生效， 比如 `npm install -g`

- 用户级配置 (`~/.npmrc`)

  > 默认值可以通过option `--userconfig` 或环境变量 `$NPM_CONFIG_USERCONFIG` 改变

- 全局配置  (`$PREFIX/etc/npmrc`)

  > 默认值可以通过option `--globalconfig` 或环境变量 `$NPM_CONFIG_GLOBALCONFIG`改变

- npm内建的配置 (`/path/to/npm/npmrc`)



###  

