# 新版wap汽首
## Nuxt 介绍  
Nuxt.js 是一个基于 Vue.js 的通用应用框架。
通过对客户端/服务端基础架构的抽象组织，Nuxt.js 主要关注的是应用的 UI 渲染。
我们的目标是创建一个灵活的应用框架，你可以基于它初始化新项目的基础结构代码，或者在已有 Node.js 项目中使用 Nuxt.js。
## nuxt安装  
用Nuxt.js 脚手架工具 create-nuxt-app。
``` javascript
yarn create nuxt-app <project-name>
```  
## nuxt目录介绍
1. pages目录：Nuxt读取这个文件夹所有的.vue文件，并使用它们创建应用程序路由器。（自动）
2. components目录：放置所有导入到页面中的Vue.js组件  
3. assets目录： 放置未编译资产如（样式， 图片， 字体， js组件）
4. static目录： 这个目录映射到服务跟目录，用来放置不变的静态资源 如favicon  
5. nuxt.config.js文件：Nuxt配件文件
6. middleware目录： 存放应用的中间件 
7. plugins目录：  
8. Store 目录：用于组织应用的 Vuex 状态树 文件  
9. layouts： 布局目录 layouts 用于组织应用的布局组件
10. Modules目录： https://nuxtjs.org/docs/directory-structure/modules  
11. api目录： 存放页面js逻辑代码（serve端 或 client端）

## nuxt中的page配置如 head （优先级）
1. page中的配置（最高）  
2. layout中的配置（次之）  
3. nuxt.config.js中的配置（最低） 
``` javascript 
// Global page headers: https://go.nuxtjs.dev/config-head
  head: {
    title: '搜狐汽车',
    htmlAttrs: {
      lang: 'zh-CN',
    },
    meta: [
      { charset: 'utf-8' },
      {
        name: 'viewport',
        content:
          'width=device-width, initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no',
      },
      { hid: 'description', name: 'description', content: '' },
      { name: 'format-detection', content: 'telephone=no' },
      { name: 'referrer', content: 'origin-when-cross-origin' },
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: 'https://s.auto.itc.cn/auto-h5/favicon.ico' },
    ],
    script: [
      { src: `//s.auto.itc.cn/auto-h5/public/font-size.js` },
      { src: '//s.auto.itc.cn/auto-h5/public/track.js' },
      { src: '//js.sohu.com/pv.js', defer: true },
      { src: '//m3.auto.itc.cn/car/theme/auto_pv.js', defer: true },
      { src: '//m1.auto.itc.cn/car/theme/newwapdb/js/lib/zepto.min_a52cd15.js' },
    ],
  },
```  
## nuxt 生命周期  
![lifecycle](../images/lifecycle.svg)  

## server环境 or client环境  
``` javascript 
function (context) { // Could be asyncData, nuxtServerInit, ...
  // Always available
  const {
    app,
    store,
    route,
    params,
    query,
    env,
    isDev,
    isHMR,
    redirect,
    error,
    $config
  } = context

  // Only available on the Server-side
  if (process.server) {
    const { req, res, beforeNuxtRender } = context
  }

  // Only available on the Client-side
  if (process.client) {
    const { from, nuxtState } = context
  }
}

// 只在client加载外部api
if (process.client) {
  require('external_library')
}
// 在client渲染
 <client-only>
                    <div class="input-value pc">
                    <Calendar v-model="date" :date-format="dateFormat" lang="zh"/>
                    </div>

                </client-only>
// 在client端渲染组件：https://nuxtjs.org/docs/configuration-glossary/configuration-plugins
```  
## nuxt 构建  
``` javascript 
// 构建命令
nuxt build

// nuxt.config.js 中的构建配置

build: {
    // 只有在生产环境时才走 cdn，其他所有情况都走 /_nuxt/
    publicPath: APP_ENV === 'prod' ? 'https://s.auto.itc.cn/auto-h5/client/' : '/_nuxt/',
    //提取css
    extractCSS: {
        ignoreOrder: true
      }
  }
// 构建结果输出到.nuxt文件夹
```   
## nuxt head配置参数
```  javascript
head() { 
    return {
      // script 配置js， 配置js变量。
      __dangerouslyDisableSanitizers: ['script'],
      script: [
        {
          innerHTML: ` // 文章页信息配置
              var __autoArticleConfig = {
                  channel_id: ${this.articleInfo.channel_id},
                  news_id: ${this.articleId},
                  cms_id: '${''}',
                  media_id: ${this.authorInfo.media_id},
                  passport: '${''}',
                  weboUrl: '${'http://mp.sohu.com/profile?xpt=cWljaGVjaGFndWFuQHNvaHUuY29t'}',
                  title: '${this.articleInfo.title}',
                  channel_url:'${'//auto.sohu.com'}',
                  categoryId:${
                    this.articleInfo.categories ? this.articleInfo.categories[0].category_id : ''
                  },
                  modelId:'${
                    this.articleInfo.models && this.articleInfo.models[0]
                      ? this.articleInfo.models[0].model_id
                      : ''
                  }',
                  classId: '${''}'
              };
                var pagesConfig = {
                  newsId: ${this.articleId},  // 当前页面的id
                  mediaId: ${this.articleInfo.mp_author_id},
                  topicTitle: '${this.articleInfo.title}',
                };`,
          type: 'text/javascript',
          body: true,
        },
        {
          src: '//www.sohu.com/sohuflash_1.js',
          body: true,
        },
        {
          src: '//statics.itc.cn/web/static/js/lib-61587d9fb8.js',
          body: true,
        },
        {
          src: '/js/mpMain.js',
          body: true,
        },
        {
          src: '//images.sohu.com/bill/default/sohu-require.js',
          body: true,
        },
        {
          innerHTML:
            "  require(['sjs/matrix/ad/passion']);window.sohu_mp.article(__autoArticleConfig);var __autoRequire = require;",

          type: 'text/javascript',
          body: true,
        },
        {
          src: '//images.sohu.com/bill/s2015/jscript/lib/sjs/matrix/ad/form/delivery.js',
          body: true,
        },
        {
          src: '//images.sohu.com/bill/s2015/jscript/lib/sjs/matrix/pv/pagePVmonitor.js',
          body: true,
        },
      ],
    };
  },
```


