# ipgw

通过vue实现东北大学ip控制网关
## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report

# run unit tests
npm run unit

# run e2e tests
npm run e2e

# run all tests
npm test
```
### 技术实现：
  1.由于浏览器的同源策略，我们无法直接访问在http:\\ipgw.neu.edu.cn下的PHP文件，所以首先解决的是跨域问题，在开发环境下，通过简单的配置`http-proxy-middleware`即可实现跨域。
  ```
   proxyTable: {
        '/include': {
      target: 'https://ipgw.neu.edu.cn',
      changeOrigin: true,
      headers: {
          Referer: 'https://ipgw.neu.edu.cn/srun_portal_phone.php?url=&ac_id=1'
      }
  ```
  在实际环境中，可能就需要另外配置。
  2.然后为了减少工作量，直接使用了原ip网关的函数，由于原ip网关使用的使jquery，在vue中引入jquery总是感觉怪怪的。
  3.前端UI使用的mint-UI，一套适用于移动设配的vue前端UI组件。
### 效果截图：
![效果截图](https://github.com/xujingguo58/ipgw_neu/blob/master/localhost-8080-(Galaxy%20S5).png)

For detailed explanation on how things work, checkout the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

