##属性

###app.locals

```
app.locals.title
// => "My App"
```

只要设置了 *app.locals* 属性，那么他就会全局存在，与 *res.locals* 不同， *res.local* 只会在当前请求中有效。

你可以在模版（jade／ejs）中使用 **app.local** 中定义的值。

```
注意:
在中间件中你不能访问这个属性
```

##方法

**app.all(path, callback[, callback ...])**

这个方法与标准的 *app.METHOD()* 相似，只是它适配所有HTTP请求动作。
适合在指定的路径里面执行全局逻辑。

e.g. 

```
app.all('*', requireAuthentication, loadUser);
```

上面这句如果放在其他路由之前， 就会让所有请求都进行身份认证，并且获取一个user对象,
或者像这样：

```
app.all('*', requireAuthentication)
app.all('*', loadUser);
```

另一个例子是全局的白名单方法。这个方法只适配“／api”的路径：

```
app.all('/api/*', requireAuthentication);
```

**app.delete(path, callback [, callback ...])**

这是一个把请求path路径的请求发送给响应的回掉方法。
你可以提供多个回掉函数（像中间件那样），这些回掉方法要调用 *next('route)* 方法来执行后面的回掉函数。你可以用这个机制来做一些判断，如果如果当前路由不适合请求，可以跳转到其他路由上。

```
app.delete('/', function (req, res) {
  res.send('DELETE request to homepage');
});
```
**app.disable(name)**

如果 *name* 是 *app settings table* 中的属性，通过这个方法设置为false，方法同 `app.set('name', false)`。

```
app.disable('trust proxy');
app.get('trust proxy');
// => false
```

**app.engine(ext, callback)**





