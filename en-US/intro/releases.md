---
name: Release Notes
sort: 2
---
# beego 1.2.0

Hi guys! After the hard working for one month, we released the new awesome version 1.2.0. beego is the fastest Go framework in the latest [Web Framework Benchmarks](http://www.techempower.com/benchmarks/#section=data-r9&hw=i7&test=json) alreadh though our goal is to make beego the best and easiest framework. In this new release, we improved even more in both usability and performance which is closer to native Go.

### New Features:

#### 1. `namespace` Support

```
    beego.NewNamespace("/v1").
        Filter("before", auth).
        Get("/notallowed", func(ctx *context.Context) {
        ctx.Output.Body([]byte("notAllowed"))
    }).
        Router("/version", &AdminController{}, "get:ShowAPIVersion").
        Router("/changepassword", &UserController{}).
        Namespace(
        beego.NewNamespace("/shop").
            Filter("before", sentry).
            Get("/:id", func(ctx *context.Context) {
            ctx.Output.Body([]byte("notAllowed"))
        }))
```

The code above supports the URL requests below:
- GET       /v1/notallowed
- GET       /v1/version
- GET       /v1/changepassword
- POST     /v1/changepassword
- GET       /v1/shop/123

`namespace` also supports pre-filters, conditions checking and unlimited nested `namespace`

#### 2. Supporting more flexible router modes

Custom functions from RESTFul router

- beego.Get(router, beego.FilterFunc)
- beego.Post(router, beego.FilterFunc)
- beego.Put(router, beego.FilterFunc)
- beego.Head(router, beego.FilterFunc)
- beego.Options(router, beego.FilterFunc)
- beego.Delete(router, beego.FilterFunc)

```
beego.Get("/user", func(ctx *context.Context) {
    ctx.Output.Body([]byte("Get userlist"))
})
```

More flexible Handler

- beego.Handler(router, http.Handler)

Integrating other services easily

```
import (
    "http"
    "github.com/gorilla/rpc"
    "github.com/gorilla/rpc/json"
)

func init() {
    s := rpc.NewServer()
    s.RegisterCodec(json.NewCodec(), "application/json")
    s.RegisterService(new(HelloService), "")
    beego.Handler("/rpc", s)
}
```

#### 3. Binding request parameters to object directly


For example: this request parameters

    ?id=123&isok=true&ft=1.2&ol[0]=1&ol[1]=2&ul[]=str&ul[]=array&user.Name=astaxie

```
var id int  
ctx.Input.Bind(&id, "id")  //id ==123

var isok bool  
ctx.Input.Bind(&isok, "isok")  //isok ==true

var ft float64  
ctx.Input.Bind(&ft, "ft")  //ft ==1.2

ol := make([]int, 0, 2)  
ctx.Input.Bind(&ol, "ol")  //ol ==[1 2]

ul := make([]string, 0, 2)  
ctx.Input.Bind(&ul, "ul")  //ul ==[str array]

user struct{Name}  
ctx.Input.Bind(&user, "user")  //user =={Name:"astaxie"}
```

#### 4. Optimized the form parsing flow and improved the performance

#### 5. Added more testcases

#### 6. Added links for admin monitoring module

#### 7. supporting saving struct into session

#### 8.httplib supports file upload interface

```
b:=httplib.Post("http://beego.me/")
b.Param("username","astaxie")
b.Param("password","123456")
b.PostFile("uploadfile1", "httplib.pdf")
b.PostFile("uploadfile2", "httplib.txt")
str, err := b.String()
if err != nil {
    t.Fatal(err)
}
```
httplib also supports custom protocol version

#### 9. ORM supports all the unexport fields of struct

#### 10. Enable XSRF in controller level. XSRF can only be controlled in the whole project level. However, you may want to have more control for XSRF, so we let you control it in Prepare function in controller level. Defult is true which means using the global setting.

```
func (a *AdminController) Prepare(){
       a.EnableXSRF = false
}
```

#### 11. controller supports ServeFormatted function which supports calling ServeJson or ServeXML based on the request's Accept

#### 12. session supports memcache engine

#### 13. The Download function of Context supports custom download file name

## Bug Fixes

1. Fixed the bug that session's Cookie engine can't set expiring time
2. Fixed the bug of saving and parsing flash data
3. Fixed all the problems of `go vet`
4. Fixed the bug of ParseFormOrMulitForm
5. Fixed the bug that only POST can parse raw body. Now all the requests except GET and HEAD support raw body.
6. Fixed the bug that config module can't parse `xml` and `yaml`



# beego 1.1.4

Release an emergency version for beego has a serious security problem, please update to the latest version. By the way released all changes together

1. fixed a security problem. I will show the detail in beego/security.md later.

2. statifile move to new file.

3. move dependence of the third libs,if you use this module in your application: session/cache/config, please import the submodule of the third libs:

	```
	import (
	     "github.com/astaxie/beego"
	   _ "github.com/astaxie/beego/session/mysql"
	)
	```
4. modify some functions to private.

5. improve the FormParse.

released data: 2014-04-08

# beego 1.1.3
this is a hot fixed:

1. console engine for logs.It will not run if there's no config.

2. beego 1.1.2 support `go run main.go`, but if `main.go` bot abute the beego's project rule,use own AppConfigPath or not exist app.conf will panic.

3. beego 1.1.2 supports `go test` parse config,but actually when call TestBeegoInit still can't parseconfig

released data: 2014-04-04

# beego 1.1.2
The improvements:

1. Added ExceptMethodAppend fuction which supports filter out some functions while run autorouter
2. Supporting user-defined FlashName, FlashSeperator
3. ORM supports user-defined types such as type MyInt int
4. Fixed validation module return user-defined validating messages
5. Improved logs module, added Init processing errors. Changed some unnecessory public fucntion to private
6. Added PostgreSQL engine for session module
7. logs module supports output caller filename and line number. Added EnableFuncCallDepth function, closed by default.
8. Fixed bugs of Cookie engine in session module
9. Improved the error message for templates parsing error
10. Allowing modifing Context by Filter to skip beego's routering rules and using uder-defined routering rules. Added parameters RunController and RunMethod
11. Supporting to run beego APP by using `go run main.go` 
12. Supporting to run test cases by using `go test`. Added TestBeegoInit function.

released data: 2014-04-03

# beego 1.1.1
Added some new features and fixed some bugs in this release.

1. File engine can't delete file in session module which will raise reading failure.
2. File cache can't read struct. Improved god automating register
3. New couchbase engine for session module
4. httplib supports transport and proxy
5. Improved the Cookie function in context which support httponly by default as well as some other default parameters.
6. Improved validation module to support different cellphone No. 
7. Made getstrings function to as same as getstring which doesn't need parseform
8. Redis engine in session module will return error while connection failure
9. Fixed the bug of unable to add GroupRouters
10. Fixed the bugs for multiple static files, routes matching bug and display the static folder automatically
11. Added GetDB to get connected *sql.DB in ORM
12. Added ResetModelCache for ORM to reset the struct which has already registered the cache in order to write tests easily
13. Supporting between in ORM
14. Supporting sql.Null* type in ORM
15. Modified auto_now_add which will skip time setting if there is default value.

released data: 2014-03-12

# beego 1.1.0
Added some new features and fixed some bugs in this release.

New features

1. Supporting AddAPPStartHook function
2. Supporting plugin mode; Supporting AddGroupRouter for configuring plugin routes.
3. Response supporting HiJacker interface
4. AddFilter supports batch matching
5. Refactored session module, supporting Cookie engine
6. Perfermance benchmark for ORM
7. Added strings interface for config which allows configuration
8. Supporting template render control in controller level
9. Added basicauth plugin which can implement authentication easily
10. #436 insert multiple objects
11. #384 query map to struct

bugfix

1. Fixed the bug of FileCache
2. Fixed the import lib of websocket
3. Changed http status from 200 to 500 when there are internal error.
4. gmfim map in memzipfile.go file should use some synchronization mechanism (for example sync.RWMutex) otherwise it errors sometimes.
5. Fixed #440 on_delete bug that not getting delted automatically 
6. Fixed #441 timezone bug

released data: 2014-02-10

# beego 1.0 release
After four months code refactoring, we released the first stable version of Beego. We did a lot of refactoring and improved a lot in detail. Here is the list of the main improvements:

1. Modular design. Right now Beego is a light weight assembling framework with eight powerful stand alone modules including cache, config, logs, sessions, httplibs, toolbox, orm and context. It might have more in the future. You can use all of these stand alone modules in your other applications directly no matter it’s web applications or any other applications such as web games and mobile games.

2. Supervisor module. In the real world engineering, after the deployment of the application, we need to do many kinds of statistics and analytics for the application such as QPS statistics, GC analytics, memory and CPU monitoring and so on. When the live issue happends we also want to debug and profile our application on live. All of these real world engineering features are included in Beego. You can enable the supervisor module in Beego and visit it from default port 8088.

3. Detailed document. We rewritten all the document. We improved the document based on many advices from the users. To make it communicate easier for different language speakers, now the comments of the document in each language are separated.

4. Demos. We provided three examples, chat room, url shortener and todo list. You can understand and use Beego easier and faster by learning the demos.

5. Redesigned Beego website. Nice people from Beego community helped Beego for logo design and website design.

6. More and more users. We listed our typical users in our homepage. They are all big companies and they are using Beego for their products already. Beego already tested by those live applications.

7. Growing active communities. There are more than 390 issues on github, more than 36 contributors and more than 700 commits. Google groups is also growing.

8. More and more applications in Beego. There are some open source applications as well. E.g.: CMS system: https://github.com/insionng/toropress and admin system: https://github.com/beego/admin

9. Powerful assistance tools. bee is used to assist the development of Beego applications. It can create, compile, package the Beego application easily.

released data: 2013-12-19