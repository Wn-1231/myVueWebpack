## 1. 初始化项目
    1). 生成package.json
        yarn init -y

    2). 创建入口js: src/index.js
        console.log('Hello Webpack!')
        document.getElementById('root').innerHTML = '<h1>Hello222</h1>'

    3). 创建页面文件: index.html
        <div id="root"></div>
      
## 2. webpack基本使用   webpack 遵循comjs 配置对象属性是特定的名

    1). 下载依赖包  
        yarn add -D webpack webpack-cli    
        yarn add -D html-webpack-plugin     //解析最基础的html和js加载的包
        //在公司 通过依赖声明去下载包，如果有错，根据报错，去看依赖声明有没有这个包
        //下载包不写  yarn ad vue     不写-d就声明在运行依赖中
        //webpack-cli 才可以使用webpack的指令webpack-cli  提供的web 命令

    2). 创建webpack配置: webpack.config.js
        //node中自带的路径方法
        const path = require('path')    
        //引入解析和html和js的   
        const HtmlWebpackPlugin = require('html-webpack-plugin')  

        module.exports = {
          // 模式: 生产环境
          mode: 'production',
          // 入口
          entry: {
            app: path.resolve(__dirname, 'src/index.js')
          },
          // 出口(打包生成js)
          output: {
            filename: 'static/js/[name].bundle.js',
            path: path.resolve(__dirname, 'dist')
          },
          // 模块加载器
          module: {
            rules: [

            ]
          },
          // 插件
          plugins: [
            new HtmlWebpackPlugin({  //创建解析html的实例  生成对应的文件
              template: 'index.html',  //模板名
              filename: 'index.html'    //文件名
            })
          ]
        }
    
    3). 生成环境打包并运行
        配置打包命令:  "build": "webpack --mode production"
        打包项目: yarn build
        运行打包项目: serve dist
        如果是安装的局部的：用npx webpack 
        webpack用这个命令npx webpack  先找局部在找全局

## 3. 开发环境运行
    1). 现在的问题:
        每次修改项目代码后, 必须重新打包, 重新运行
    
    2). 下载依赖包
        yarn add -D webpack-dev-server
    
    3). 配置开发服务器
        devServer: {
          open: true, // 自动打开浏览器
          quiet: true, // 不做太多日志输出
        },
    
    4). 配置开启source-map调试
        devtool: 'cheap-module-eval-source-map',
        //在开发环境下webpack-dev-server 自动配置   source-map 查找那出问题
        //在生产模式中自己配置  source-map 查找那出问题

    5). 开发环境运行  在package.josn中配置 scrpt对象中配置
        配置命令: "dev": "webpack-dev-server --mode development"
        执行命令: yarn dev

## 4. 打包处理 ES6/CSS/图片
    1). 处理ES6
        a. 下载依赖包
            yarn add -D babel-loader @babel/core @babel/preset-env
        b. 配置
            {
              test: /\.js$/,
              //exclude: /(node_modules|bower_components)/,
              include: path.resolve(__dirname, 'src'),
              use: {
                loader: 'babel-loader',
                options: {
                  presets: ['@babel/preset-env']
                }
              }
            }
    
    2). 处理CSS
        a. 下载依赖包
            yarn add -D css-loader style-loader
        b. 配置
            {
              test: /\.css$/,
              use: ['style-loader', 'css-loader'], // 多个loader从右到左处理
            }

    3). 处理图片
        a. 下载依赖包
            yarn add -D url-loader@2.3.0 file-loader@4.3.0
        b. 配置
            {
              test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
              loader: 'url-loader',
              options: {
                limit: 1000,
                name: 'static/img/[name].[hash:7].[ext]' // 相对于output.path
              }
            }
    4). 测试
        a. 添加图片: src/assets/imgs/logo.png
        b. 添加css: src/assets/css/my.css
            img {
              width: 200px;
              height: 200px;
            }
        c. index.js
            import logo from './assets/imgs/logo.png'
            import  './assets/css/my.css'

            const image = new Image()
            image.src = logo
            document.body.appendChild(image)
            document.getElementById('root').innerHTML = '<h1>Hello222</h1>'

## 5. 搭建vue的环境
    0). 文档:
        https://vue-loader.vuejs.org/zh/

    1). 下载依赖包:
        yarn add vue
        yarn add -D vue-loader vue-template-compiler
    
    2). 配置
        const VueLoaderPlugin = require('vue-loader/lib/plugin')

        {
          test: /\.vue$/,
          include: path.resolve(__dirname, 'src'),
          loader: 'vue-loader'
        }

        {
          test: /\.css$/,
          use: ['vue-style-loader', 'css-loader'],
        }

        new VueLoaderPlugin()

        // 引入模块的解析
        resolve: {
          extensions: ['.js', '.vue', '.json'], // 可以省略的后缀名
          alias: { // 路径别名(简写方式)
            'vue$': 'vue/dist/vue.esm.js',  // 表示精准匹配
          }
        }
    
    3). 编码: 
        src/App.vue
        src/index.js
        
## 区分使用生产环境与开发环境
    使用生产环境:
        npm run build   ==> webpack
        1). 在内存中进行编译打包, 生成内存中的打包文件
        2). 保存到本地(在本地生成打包文件)   ===> 此时还不能通过浏览器来访问, 需要启动服务器运行
    使用开发环境
        npm run dev   ==> webpack-dev-server
        1). 在内存中进行编译打包, 生成内存中的打包文件
        2). 调动服务器, 运行内存中的打包文件   ===> 可以通过浏览器虚拟路径访问