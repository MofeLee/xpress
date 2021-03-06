# xpress 


## What is xpress?

- **Api Framework** A node.js api framework base on express which provide api route version and channel control 
- **Web Framework** A node.js web framework base on express and art-templete, and expand many usefull view helper

## Install

   $ npm install xpress
   
## Sample code
> there are two full sample projects in /sample/api and /sample/web

### Run sample project
```
#cd ./sample/api
cd ./sample/web 
npm install 
node app.js
```
    
> open http://127.0.0.1:8001/, there are many template engine(base on artTemplate) view helper sample code
    

## APi    
### create an api server with xpress
```js
//import module
var Xpress = require('Xpress');
var config = require('./config');
```

```js
//create server
var server = new Xpress({
    host: null,
    key: null,
    cert: null,
    port: {
        http: 8001,
        https: null
    }
});
```

```js
//configure
server.conf('x-powered-by', false);
server.conf('trust proxy', true);
server.conf('views', config.public.server.view.path);
server.conf('view engine',config.public.server.view.engine);
server.conf('view cache', false);
server.engine('html', Xpress.engine.__express); 
```
> Xpress.engine is equal require('art-template/node/template-native.js')

```js
//use middleware
var body = require('body-parser');
var cookie = require('cookie-parser');
var timeout = require('connect-timeout');
var compression = require("compression");
var statics = require('express-static');
server.use(compression());
server.use(timeout('20s'));
server.use(cookie());
server.use(body.json());
server.use(body.urlencoded({extended: true}));
server.use(statics(config.public.server.statics.path));
```

```js
//register an api on a controller with version or channel control
//'v' represent version and 'c' represent channel
//if you register an api with version and channel control, 
//you must set 'X-Accept-Version' and 'X-Client-Channel' on request header, 
//xpress will get the two value from the header and compared with the register v and c
//if not equal, xpress will skip the controller and jump to the next controller which has registered the same route
//you can get and set the two headers on Xpress.defaults.versionHeader and Xpress.defaults.channelHeader
server.get('/user', {v:1.0, c: 'ios'}, function(req, res, next){
    res.json({
        users: []
    });
});
//register an api on a controller without version and channel control
server.get('/user/:id', function(req, res, next){
    res.json({
        name: 'synder',
        age : 29
    });
})
```
```js
//create a sub router and register on server
var Router = Xpress.Router;
var productRouter = new Router();
//register an api on subRouter without version or channel control  
productRouter.get('/', function(req, res, next){
    res.json({
        products: []
    });
})
//register an api on subRouter with version or channel control  
productRouter.get('/:id', {v:1, c:1}, function(req, res, next){
    res.json({
        products: []
    });
})
server.sub('/product', productRouter); 
```
```js
//error handler
server.error(404, function (err, req, res, next) {
    res.status(404).json('not found');
});
server.error(500, function (err, req, res, next) {
    res.status(500).json(err.stack);
});
```
```js
//listen on host and port
server.listen(function(message){
    console.log(message);
});
//listen on host and port with cluster
//server.cluster(0, function(msg){
//    console.log(msg);
//});
```
```js
//export
module.exports = server;
```

## View helper(base on [art-template](https://www.npmjs.com/package/art-template))
<table cellspacing="0" cellpadding="0">
    <tr>
        <td>&lt;%= $dateTime(new Date())%&gt;</td>
        <td>2016年06月07日 11时56分38秒</td>
    </tr>
    <tr>
        <td>&lt;%= $dateTime(Date.now())%&gt;</td>
        <td>2016年06月07日 11时56分38秒</td>
    </tr>
    <tr>
        <td>&lt;%=Math.random()%&gt;</td>
        <td>0.035149546630335315</td>
    </tr>
    <tr>
        <td>&lt;%= $toString([1,2,3])%&gt;</td>
        <td>[1,2,3]</td>
    </tr>
    <tr>
        <td>&lt;%= $parseInt('10', 0)%&gt;</td>
        <td>10</td>
    </tr>
    <tr>
        <td>&lt;%= $parseInt('NA0122', 0)%&gt;</td>
        <td>0</td>
    </tr>
    <tr>
        <td>&lt;%= $parseInt('NA0122')%&gt;</td>
        <td>null</td>
    </tr>
    <tr>
        <td>&lt;%= $parseFloat('12.3')%&gt;</td>
        <td>12.3</td>
    </tr>
    <tr>
        <td>&lt;%= $parseFloat('a12.3', 0)%&gt;</td>
        <td>0</td>
    </tr>
    <tr>
        <td>&lt;%= $format('%s %X', 'sam', 1000)%&gt;</td>
        <td>sam 0x3E8</td>
    </tr>
    <tr>
        <td>&lt;%= $format('%s %o', 'sam', 1000)%&gt;</td>
        <td>sam 01750</td>
    </tr>
    <tr>
        <td>&lt;%= $format('%s %d', 'sam', 1000)%&gt;</td>
        <td>sam 1000</td>
    </tr>
    <tr>
        <td>&lt;%= $format('%s %b', 'sam', 1000)%&gt;</td>
        <td>sam 1111101000</td>
    </tr>
    <tr>
        <td>&lt;%= $format('%c %c', 'sam', 98)%&gt;</td>
        <td>sam b</td>
    </tr>
    <tr>
        <td>&lt;%= $format('%s %2f', 'sam', 98)%&gt;</td>
        <td>sam 98.00</td>
    </tr>
    <tr>
        <td>&lt;%= $format('%s %j', 'sam', {name:1})%&gt;</td>
        <td>sam {"name":1}</td>
    </tr>
    <tr>
        <td>&lt;%= $toString(null)%&gt;</td>
        <td></td>
    </tr>
    <tr>
        <td>&lt;%= $toString({name: 1})%&gt;</td>
        <td>{"name":1}</td>
    </tr>
    <tr>
        <td>&lt;%= $toString([12,3])%&gt;</td>
        <td>[12,3]</td>
    </tr>
    <tr>
        <td>&lt;%= $join('10', '2', '6', '+')%&gt;</td>
        <td>10+2+6</td>
    </tr>
    <tr>
        <td>&lt;%= $join('10', null, '6', '9', '+')%&gt;</td>
        <td>10+6+9</td>
    </tr>
    <tr>
        <td>&lt;%= $join(null, null, '6', '+')%&gt;</td>
        <td>6</td>
    </tr>
    <tr>
        <td>&lt;%= $join(null, '6', '', '+')%&gt;</td>
        <td>6</td>
    </tr>
    <tr>
        <td>&lt;%= $trim('  name  ')%&gt;</td>
        <td>name</td>
    </tr>
    <tr>
        <td>&lt;%= $mask('18083489462', '*', 4, 5)%&gt;</td>
        <td>180*****462</td>
    </tr>
    <tr>
        <td>&lt;%= $mask('18083489462', '*', -1, 5)%&gt;</td>
        <td>18083******</td>
    </tr>
    <tr>
        <td>&lt;%= $pad('12222', 10, '0', 'left')%&gt;</td>
        <td>0000012222</td>
    </tr>
    <tr>
        <td>&lt;%= $pad('12222', 10, '0', 'right')%&gt;</td>
        <td>1222200000</td>
    </tr>
    <tr>
        <td>&lt;%= $clean(' 122 22 ')%&gt;</td>
        <td>122 22</td>
    </tr>
    <tr>
        <td>&lt;%= $toString($lines('122\r\n22132'))%&gt;</td>
        <td>[122,22132]</td>
    </tr>
    <tr>
        <td>&lt;%= $toString($lines('122\r22132'))%&gt;</td>
        <td>[122,22132]</td>
    </tr>
    <tr>
        <td>&lt;%= $truncate('122212313213132132', 13, '...')%&gt;</td>
        <td>1222123132...</td>
    </tr>
    <tr>
        <td>&lt;%= $chineseCurrency('92102600401.001')%&gt;</td>
        <td>玖佰贰拾壹亿零贰佰陆拾萬零肆佰零壹</td>
    </tr>
    <tr>
        <td>&lt;%= $currency(242605401.001, '$', 2)%&gt;</td>
        <td>&lt;%= $242,605,401.00</td>
    </tr>
    <tr>
        <td>&lt;%= $upperCase('AbddessSww')%&gt;</td>
        <td>ABDDESSSWW</td>
    </tr>
    <tr>
        <td>&lt;%= $lowerCase('AbddessSww')%&gt;</td>
        <td>abddesssww</td>
    </tr>
    <tr>
        <td>&lt;%= $capitalize('AbddessSww')%&gt;</td>
        <td>AbddessSww</td>
    </tr>
    <tr>
        <td>&lt;%= $capitalize('AbddessSww', true)%&gt;</td>
        <td>Abddesssww</td>
    </tr>
    <tr>
        <td>&lt;%= $bankCard('233546454633344332')%&gt;</td>
        <td>2335 4645 4633 3443 32</td>
    </tr>
    <tr>
        <td>&lt;%= $number(0.5, 3)%&gt;</td>
        <td>0.500</td>
    </tr>
    <tr>
        <td>&lt;%= $thousands(2783619263)%&gt;</td>
        <td>2,783,619,263</td>
    </tr>
    <tr>
        <td>&lt;%= $percentage(0.5)%&gt;</td>
        <td>50%&gt;</td>
    </tr>
    <tr>
        <td>&lt;%= $percentage(0.523366, 2)%&gt;</td>
        <td>52.34%&gt;</td>
    </tr>
    <tr>
        <td>&lt;%= $versionPath('/name', 10)%&gt;</td>
        <td>/name?version=10</td>
    </tr>
    <tr>
        <td>&lt;%= $joinPath('/name', '//age')%&gt;</td>
        <td>/name/age</td>
    </tr>
    <tr>
        <td>&lt;%= $normalizePath('///name/age')%&gt;</td>
        <td>/name/age</td>
    </tr>
    <tr>
        <td>&lt;%= $date('2016-06-01T07:05:36.838Z', '-')%&gt;</td>
        <td>2016-06-01</td>
    </tr>
    <tr>
        <td>&lt;%= $time('2016-06-01T07:05:36.838Z', ':')%&gt;</td>
        <td>15:05:37</td>
    </tr>
    <tr>
        <td>&lt;%= $dateTime('2016-06-01T07:05:36.838Z', '-', ':')%&gt;</td>
        <td>2016-06-01 15:05:37</td>
    </tr>
    <tr>
        <td>&lt;%= $dateTime('2016-06-01T07:05:36.838Z')%&gt;</td>
        <td>2016年06月01日 15时05分37秒</td>
    </tr>
    <tr>
        <td>&lt;%= $urlFormat('/home', {name:1}, 'http', '127.0.0.1')%&gt;</td>
        <td>http://127.0.0.1/home?name=1</td>
    </tr>
    <tr>
        <td>&lt;%= $urlFormat('/home', {name:1})%&gt;</td>
        <td>/home?name=1</td>
    </tr>
    <tr>
        <td>&lt;%= $encodeURIComponent('/测试 账号')%&gt;</td>
        <td>%2F%E6%B5%8B%E8%AF%95%20%E8%B4%A6%E5%8F%B7</td>
    </tr>
    <tr>
        <td>&lt;%= $decodeURIComponent($encodeURIComponent('/测试 账号'))%&gt;</td>
        <td>/测试 账号</td>
    </tr>
    <tr>
        <td>&lt;%= $encodeURI('/测试 账号')%&gt;</td>
        <td>/%E6%B5%8B%E8%AF%95%20%E8%B4%A6%E5%8F%B7</td>
    </tr>
    <tr>
        <td>&lt;%= $decodeURI($encodeURI('/测试 账号'))%&gt;</td>
        <td>/测试 账号</td>
    </tr>
    <tr>
        <td>&lt;%= $isNull(null)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isNull(undefined)%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isUndefined(undefined)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isUndefined(null)%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isNullOrUndefined(null)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isNullOrUndefined(undefined)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isArray([])%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isDate(new Date())%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isDate('2012-03-01')%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isString('name')%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isString({})%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isString(null)%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isNumber(1)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isBool(true)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isInt(1.1)%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isInt(1)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isFloat(1.1)%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isFloat(1)%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isObject(null)%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isObject({})%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isObject([])%&gt;</td>
        <td>true</td>
    </tr>
    <tr>
        <td>&lt;%= $isDictionary([])%&gt;</td>
        <td>false</td>
    </tr>
    <tr>
        <td>&lt;%= $isDictionary({})%&gt;</td>
        <td>true</td>
    </tr>
</table>

## art-template
> refer to [art-template](https://www.npmjs.com/package/art-template)