#### Webpack对性能优化的方法有哪些？

1. 使用tree-sharking

2. 优化babel,对babel优化过的代码进行缓存

   ```js
   loader: 'babel-loader?cacheDirectory=true'
   ```

   