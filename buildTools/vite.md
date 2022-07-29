# vite
## 使用vite  
1. 管理员打开powerShell命令行  
2. 搭建第一个 Vite 项目  
``` bash  
npm init vite@latest
```  
3. 在命令行中填写项目名称vite-project， 选择vue模板  
4. 进入viteproject文件夹，运行npm scripts  
## 搭建多页面项目
### 配置文件vite.config.js
``` javascript  
import { defineConfig } from 'vite'
const { resolve } = require('path')
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  //设置项目原文件跟路径为 ./resource
  root: './resource',
  plugins: [vue()],
  build: {
    // 构建多页面配置
    rollupOptions: {
      input: {
        // __dirname是vite.config.js所在路径
        main: resolve(__dirname, 'resource/index.html'),
        childPage1: resolve(__dirname, 'resource/childPage1/index.html')
      }
    }
  }
})
```   
### 多页面路径  
<pre>
<code>
├── package.json
├── vite.config.js
├── resource
    ├── index.html
    ├── main.js
    └── childPage1
        ├── index.html
        └── childPage1.js
</code>
</pre>

### 开发环境访问url  
主目录：http://localhost:3000/  
子页面：http://localhost:3000/childPage1/  

