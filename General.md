# Trochilidae Doc

## 关于Trochilidae

### 什么是Trochilidae

Trochilidae是一个现代化、轻量级的基于组件的PHP框架，主要适用于中小型快速迭代的web应用程序的开发。

### 开发的初衷

之所以开发这个框架的目的是为了可以在方便国内快速开发的同时又能充分体现现代PHP特性，而不是在PHP 7都已经成熟的现在依旧使用着老式的PHP模式进行编程。因此这个框架的核心是力求麻雀虽小五脏俱全。

### 系统要求

- PHP version >= 7.0 

# 安装Trochilidae

## 安装 Composer

Trochilidae 需要使用 Composer 来管理其依赖性。所以，在你使用 Trochilidae 之前，请务必先安装 Composer。推荐使用[国内源](https://www.phpcomposer.com/)

## 创建Trochilidae项目

可以使用如下命令创建你的Trochilidae项目

```
composer create-project --prefer-dist Trochilidae/Trochilidae projectName
```

## 配置web服务器

如果服务器是Apache的话请将项目根目录设置为projectName/public，并开启rewrite模块。



# 基本功能

## 路由配置

Trochilidae的路由配置文件分为两种，一种是路由注册文件，一种是路由映射文件。其中注册文件为projectName/config/routes.json，而*只有注册在该文件中的路由映射文件才生效*。

其格式大体如下

```json
{
  "home_bundle": {
        "type":"json",
        "resource":"HomeBundle/Resources/config/routes.json",
        "prefix":"/"
  },
    ...
}
```

其中resource中的HomeBundle/Resources/config/routes.json即为路由映射文件，其格式如下

```json
{
  "index": {
    "method": "GET",
    "path": "/",
    "target":"indexAction@HomeBundle/Controller/IndexController"
  },
    ...
}
```

target的格式为 **方法@控制器**。

其中路由还支持 annotation 方式，具体可参照路由设置的详解。

注：在测试环境下所有路由将实时刷新，而生产环境将会产生缓存，因此当有新的路由添加到线上时请务必删除缓存文件。

## 基础控制器

Trochilidae中的所有代码都放于src目录下，操作必须声明在控制器中 ，而控制器目录则位于bundleName/Controller下。此处以默认的IndexController为例。

```php
<?php

namespace HomeBundle\Controller;

use Trochilidae\bin\Core\Controller;


class IndexController extends Controller
{

    public function indexAction(){
        $this->render('index.html.twig@HomeBundle:Index',['projectName'=>'Trochilidae']);}

}
```

而我们可以通过之前路由配置中定义的index项来找到该方法。

> 建议所有的控制器类名遵循大驼峰命名方式，而具体方法采用小驼峰方式，并且控制器继承Controller类。

> *注意* ：所有命名空间都是基于bundleName向下实现的，因此如果新增的bundle没有自动加载请查看composer.json配置是否正确，以及是否执行过`composer dump-autoload` 

## 视图

Trochilidae中采用Twig模板引擎，因此所有视图被保存在 `src/bundleName/Resources/views` 文件夹中。 以默认应用为例

layout.html.twig

```html
<!doctype html>
<html lang="en">
<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
    <link rel="stylesheet" href="/bundle/HomeBundle/css/index.css">
    <title>{% block title %}Hello Trochilidae!{% endblock %}</title>
</head>
<body>
    {% block container %}
    {% endblock %}
</body>
</html>
```

Index/index.html.twig

```html
{% extends "layout.html.twig" %}

{% block container %}
    <div class="container-fluid">
        <div class="row nav-box">
            <div class="col-md-4 nav-title">
                <a href="javascript:void(0);"><h2>{{ projectName }}</h2></a>
            </div>
            <div class="col-md-8 nav-item">
                <a href="javascript:void(0);">Home</a>
                <a href="javascript:void(0);">Features</a>
                <a href="javascript:void(0);">Contact</a>
            </div>
        </div>
        <div class="row">
            <div class="col-md-8  col-md-offset-2">
                <div class="jumbotron jumbotron-back-color">
                    <h1 class="display-4">Hello, {{ projectName }}!</h1>
                    <p class="lead">This is a simple &  modern PHP framework. You can quickly build a project according to your own favorite way.</p>
                    <hr class="my-4">
                    <p>Don't think too much, start now!</p>
                    <p class="lead">
                        <a class="btn btn-default btn-lg" href="#" role="button">Learn more</a>
                    </p>
                </div>
            </div>
        </div>
    </div>
{% endblock %}
```

你的视图文件看上去可能类似这样，其中layout.html.twig文件为父类模板文件，而index.html.twig为具体操作的视图，其中子视图通过重写父类模板中的{% block blockName %}{% endblock %}标签块中的内容来展现相关内容。

如上所示，其中{{ projectName }}将被替换为indexAction中的projectName的值。

更多详细内容可以参考[twig官网](https://twig.symfony.com/)

## 基础数据库操作

Trochilidae中数据库的配置文件在config/database.json文件中，主要内容如下

```json
{
  "default":{
    "dbtype":"yourdbtype",
    "dbhost": "yourdbhost",
    "dbname": "yourdbname",
    "dbuser": "yourdbuser",
    "dbpass": "yourdbpassword",
    "dbport":"yourdbport",
    "prefix":"",
    "charset":"utf8"
  }
}
```

你可以在Trochilidae中直接使用SQL语句来操作数据库，例如：

```php
	$model=new Model();
    $result=$model->query('select * from user')->fetchAll();	// select data
	...
    // insert data    
	$result=$model->query("insert into user(user_name,password) VALUES ('john',md5(123456))")->errorCode();
    if($result=='00000'){
		//do something
    }
```

当然你也可以采用orm的方式来处理数据，具体请参看数据库操作详解部分。

## 路由详解

之前提到过，在Trochilidae中的路由配置有两种方式，一种是路由配置文件方式，另一种是 annotation 方式。接下来分别介绍

### 路由注册文件

假设projectName/config/routes.json如下

```json
{
  "home_bundle": {
        "type":"json",
        "resource":"HomeBundle/Resources/config/routes.json",
        "prefix":"/"
  },
    ...
}
```

其由以下几部分组成：

bundle_name：即上面的 home_bundle，表示路由注册名称，可以随意取，只要不重复即可。

type：表示路由类型，目前支持 json 和 annotation 两种方式。

resource：表示具体资源所在位置，可以是一个json文件（当使用配置文件时）或一个controller文件夹（当使用 annotation 方式时）。

prefix：路由前缀，会自动拼接到具体路由配置中的path前面并会去除开头重复的/，但需要注意的是不会去除后面的/，例如prefix为/home,你的path为/则访问localhost/home为404，必须访问localhost/home/。

### 路由配置文件方式

假设指定resource中的HomeBundle/Resources/config/routes.json为具体的路由映射文件

```json
{
  "index": {
    "method": "GET",
    "path": "/",
    "target":"indexAction@HomeBundle/Controller/IndexController"
  },
    ...
}
```
同样其由以下几部分组成：

route_name：即上面的 index，表示路由配置名称，可以随意取，只要不重复即可。

method：表示当前URL的访问方式，例如GET，POST和PUT等。

path：表示当前URL路径，支持正则表达式，会自动拼接注册文件中的prefix字段，并去除开头重复的/。

target：路由具体对应的控制器，格式为方法名@控制器名，需要注意的是控制器名需要写src下的全路径。

### Annotation 方式

假设projectName/config/routes.json如下

```json
{
  "home_bundle": {
    	"type":"annotation",
    	"resource":"HomeBundle/Controller",
    	"prefix":"/home"
  },
    ...
}
```

其中resource指定HomeBundle下的Controller文件夹中使用 annotation 方式。

假设IndexController中内容如下

```php
	/**
     *  @Route(path="/user/id/\d+",method="GET")
     */
    public function indexAction(Route $route){
        print_r('The User Id is '.$route->get('id'));
    }
```

我们通过`@Route(path="/user/id/\d+",method="GET")`这行注解注册了一个路由，可以匹配使用GET请求/home/user/id/1这样的数据。

其中path和method含义与配置文件方式相同，而目标方法即为注解所对应的方法。

以上两种方式都可以配置路由，配置文件方式相对比较传统，比较适合模块多，参与人数也多的项目；而 annotation 方式相对于代码会更加紧密，同时合理的规划bundle和控制器可以避免 annotation 可能造成路由混乱的问题。

> 注意：强烈不建议在同一个bundle内混用这两种方式，这样容易导致不容易发现的错误。

## 控制器拦截

当你需要做一些权限控制的时候可以使用自带的简易拦截器完成这项工作。

首先实现一个BaseController继承Controller控制器，内容如下

```php
class BaseController extends Controller
{
    protected function __after()
    {
        parent::__after(); // TODO: Change the autogenerated stub
        // do something controller after
    }

    protected function __before()
    {

        parent::__before(); // TODO: Change the autogenerated stub
        $route=new Route();
        $action=$route->getAction();
        if(isAllow($action['classFileName'],$action['methodName'])){
            // do something controller before
        }
    }
}
```

然后IndexController继承BaseController控制器，即可完成对控制器的拦截。

## 数据库操作

Trochilidae继承了Medoo的数据库操作，并在此基础上增加了一些自己的操作，因此可以使用两种方法来操作数据库。

### 使用Model类

Model类是完全继承Medoo类的，因此Medoo类中的所有操作都是可以执行的，例如select ，update 等，具体可以参看[Medoo官网](https://medoo.in/)

### 使用ORM

以下假设以操作user表为例

首先在bundle文件夹下建立一个Entity文件夹，并假设我们在其中建立一个user.json，内容如下

src/bundle/Entity/user.json

```json
{
  "id":{
    "type":"int",
    "auto":true,
    "key":true
  },
  "user_name": {
    "type":"varchar",
    "length":"50",
    "not_null":true
  },
  "password": {
    "type":"varchar",
    "length":"50",
    "not_null":true
  },
  "avatar": {
    "type":"varchar",
    "length":"100"
  },
  "description": {
    "type":"text"
  },
  "email": {
    "type":"varchar",
    "length":"50"
  },
  "login_time": {
    "type":"datetime"
  },
  "status": {
    "type":"int"
  }
}
```

然后在网站根目录下执行命令行

> php bin/console createEntity yourProjectFolder\src\HomeBundle\Entity

并使用以下命令更新数据库
> php bin/console updateDataBase  //`强烈不建议在生产环境中这样做`

执行完成之后将会自动生成相应的Ectity文件，如下

```php
<?php
namespace HomeBundle\Entity;
class User {
	private $id;
	private $user_name;
	private $password;
	private $avatar;
	private $description;
	private $email;
	private $login_time;
	private $status;

	public function getId(){
		return $this->id;
	}

	public function setId($id){
		$this->id = $id;
	}

	public function getUserName(){
		return $this->user_name;
	}

	public function setUserName($user_name){
		$this->user_name = $user_name;
	}

	public function getPassword(){
		return $this->password;
	}

	public function setPassword($password){
		$this->password = $password;
	}

	public function getAvatar(){
		return $this->avatar;
	}

	public function setAvatar($avatar){
		$this->avatar = $avatar;
	}

	public function getDescription(){
		return $this->description;
	}

	public function setDescription($description){
		$this->description = $description;
	}

	public function getEmail(){
		return $this->email;
	}

	public function setEmail($email){
		$this->email = $email;
	}

	public function getLoginTime(){
		return $this->login_time;
	}

	public function setLoginTime($login_time){
		$this->login_time = $login_time;
	}

	public function getStatus(){
		return $this->status;
	}

	public function setStatus($status){
		$this->status = $status;
	}

}

```

然后在src/bundle下建立一个Model文件夹，建立两个文件，一个UserModelIfs 接口文件，一个UserModel文件，内容如下

UserModelIfs.php

```php
<?php
namespace HomeBundle\Model;


use HomeBundle\Entity\User;

interface UserModelIfs
{
    public function save(User $user);
    public function findByUser(User $user);
    public function findByUserId($id);
}
```

UserModelIfs.php
```php
<?php
namespace HomeBundle\Model;

use HomeBundle\Entity\User;
use Trochilidae\bin\Core\Model;

class UserModel extends Model implements UserModelIfs
{
    public function save(User $user)
    {
        // TODO: Implement save() method.
        if(intval($user->getId())==0){
            $ret = $this->insertByEntity($user);
            $user->setId($ret);
        }else{
            $this->updateByEntity($user,['id'=>$user->getId()]);
        }
        return $user;
    }
    public function findByUser(User $user)
    {
        // TODO: Implement findByUser() method.
    }
}
```

然后建立src/bundle/Service文件夹以及一个UserService.php

```php
<?php
    
namespace HomeBundle\Service;


use HomeBundle\Entity\User;
use Trochilidae\bin\Core\Entity;
use Trochilidae\bin\Core\Model;

class UserService
{
    public function getUser($userId){
        $entity=new Entity();
        /**
         * @var $user User
         */
        $user=$entity->get('User@HomeBundle')->findOne('*',['id'=>$userId]);
        // you can get same result like this 
        // $user=$entity->EntityToArray($entity->get('User@HomeBundle')->findById($userId));
        
        return $user;
    }
    public function saveUser($userId){
        $entity=new Entity();
        /**
         * @var $user User
         */
        $user=$entity->get('User@HomeBundle')->findOne(['id'=>$userId]);
        //  you can get a empty User Object,when you want only insert 
        //  $user=$em->get('User@HomeBundle')->create();
       	$user->setUserName('john');
        $user->setStatus(1);
        $user->setPassword(md5('123456'));
        $user->setLoginTime(date('Y-m-d H:i:s'));
        $ret=$em->save($user);
    }
}
```

其中$entity->get('User@HomeBundle')为选择一个Entity实体类，格式为entityName@bundleName，上例即在HomeBundle下的User的Entity类。

其中你可以使用魔术方法findById也可以使用findOne(col,where)方法，findOne中的where与Medoo的select中的where完全兼容，你可以参看[这里](https://medoo.in/api/where),另外还有find(col,where,join)可用于返回一个列表,用法与findOne相同。

注意：魔术方法返回的是对象，findOne和find返回的是数组，两者可以通过EntityToArray和ArrayToEntity进行互换。

### 关联表操作

假设你的用户可以上传文件，则可假设有一个File的Entity，格式如下

```json
{
  "id":{
    "type":"int",
    "auto":true,
    "key":true
  },
  "name": {
    "type":"varchar",
    "length":"50",
    "not_null":true
  },
  "size": {
    "type":"varchar",
    "length":"50",
    "not_null":true
  },
  "type": {
    "type":"varchar",
    "length":"50"
  },
  "description": {
    "type":"text"
  },
  "suffix": {
    "type":"varchar",
    "length":"20"
  },
  "status": {
    "type":"int"
  },
  "users":{
    "type":"o2o",
    "target":"User.id"
  }
}
```

同样我们在之前的User.json中添加

```json
 ... 
 "files":{
    "type":"o2m",
    "target":"File.id"
  }
...
```

我们可以看到File里的users字段，其中的type设置为了o2o，并且添加了一个target字段，其代表从File实体的角度看，每个文件都与一个User实体所对应，关联的字段为User实体的id。

同理，在User实体中的files字段表示从User实体角度看，每个User实体都可以对应多个文件。

Trochilidae中只有o2o和o2m两种关系，m2m可以视为o2m的扩展，因此假设需要m2m，只需要在users中的格式设置为o2m即可。当执行updateDataBase时如果两者关系中有o2m，则会自动生成一张关联表，表名为o2m所在实体名_表名为o2o所在实体名，当两个都为o2m时，则是按字母顺序排列，字段分别是两个target的内容，如上所示会生成一张user_file关联表，表内字段为user_id和file_id。

因此当我们有关联表时操作可以使用如下方式

```php
     public function save(User $user)
     {
         ...
         // TODO: Implement save() method.
  		$this->updateRelationEntity(["User.id"=>[1,2,3]],["File.id"=>['1','2','3']]);
     	...
     }
```

其中updateRelationEntity包含两个实体，以上这行命令将会生成3*3条关联信息，其中User.id内容会更新到user_id字段，File.id也相同。

另外需要注意的是o2o并不会生成关联表，因此当你需要更新或新增时可采用之前的方法。

需要注意的是，你也完全可以直接生成一个user_file.json的实体文件进而采用之前的方式开发也是可以的，这种方式适用于需要多个表同时关联或者需要加入一些其他功能例如关联的排序等记录时使用。

### 使用xml格式

有时有些过于复杂的SQL无法使用ORM来实现，但直接放在代码里又不容易管理，此时就可以使用xml方式。

首先在/Resources下新建一个Mapping文件夹，然后新建一个UserMapping.xml文件，格式如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Mappings>
    <Mapping id="getUserList" db="other">
        SELECT * FROM user
        WHERE
        {if:$status!=0}
            status=$status
        {else:}
            1=1
        {/if}
    </Mapping>
    <Mapping id="updateUser" db="other">
        UPDATE user
        set
        {if:isset($user_name)}
            user_name=CONCAT('$user_name',id),
        {/if}
        {if:isset($password)}
            password='$password',
        {/if}
        status=1
        WHERE status={$status};
    </Mapping>
</Mappings>
```

然后在service文件中使用如下方法

```php
		$entity=new Entity();
		$result=$entity->doMapping('HomeBundle\UserMapping\updateUser',['user_name'=>'huajie','status'=>1]);

```

其中doMapping包含两个参数，第一个是id，第二个是传入参数，其中id由三部分构成，格式为bundleName\mappingFile\mappingId。其中参数会自动渲染生成相应的sql，目前可以支持if语句和变量渲染操作。

同时你也可以使用getMappingSql方法来获取sql而不执行，其参数与doMapping相同。

### 事务处理

Trochilidae本身是不做事务处理的，但你可以在继承的Model类中手动开启事务处理，如在UserModel中我们可以如下操作

```php
     public function save(User $user)
     {
		$this->pdo->beginTransaction();
		// do something
		$this->pdo->commit();
		return $user;
     }
```

同样当你在service中直接使用实例化Model类来操作数据库的时候，也可以直接开启事务，如下

```php
		$model=new Model();
		$model->pdo->beginTransaction();
		//do something
		$model->pdo->commit();
```

当某些比较复杂的操作需要牵涉多个Model时，你也可以在service中像如下这种方式使用事务

```php
		$entity=new Entity();
		$entity=$entity->get('User@HomeBundle');
		$entity->beginTransaction();
		// do something
		if($result==SUCCESS)
			$entity->commit();
		else
			$entity->rollback();
```

> **beginTransaction方法必须在get之后使用**

但需要格外注意的是每个Entity只对应一个Model事务，因此当你此时需要用多个Entity处理多个数据的时候就必须使用多个Entity对象，且同时只能由其中的一个对象来处理事务，不可交叉。例如：

```php
		$entity=new Entity();
		$entity=$entity->get('User@HomeBundle');
		$entity->beginTransaction();
		//do something
		$entity=$entity->get('File@HomeBundle');  // is error,becuse the model is change

		$entity2=new Entity();
		$entity2=$entity2->get('File@HomeBundle');  // is right
		// do something
		if($result==SUCCESS)
			$entity2->commit();  // error 
			$entity->commit();   // right
		else
			$entity->rollback();
```



###切换数据库

当你有多个数据库需要操作时首先需要添加数据库配置文件，假设如下

```json
{
  "default":{
    "database_type":"yourdbtype",
    "server": "yourdbhost",
    "database_name": "yourdbname",
    "username": "yourdbuser",
    "password": "yourdbpassword",
    "port":"yourdbport",
    "prefix":"",
    "charset":"utf8"
  },
  "other":{
    "database_type":"yourdbtype2",
    "server": "yourdbhost2",
    "database_name": "yourdbname2",
    "username": "yourdbuser2",
    "password": "yourdbpassword2",
    "port":"yourdbport2",
    "prefix":"",
    "charset":"utf8"
  }
}
```

在Trochilidae切换数据库极其简单，只需要在Entity实例get方法后直接使用switchDataBase即可，如下

```php
        $entity=new Entity();
        /**
         * @var $user User
         */
        $user=$entity->get('User@HomeBundle')->switchDataBase('other')->findOne('*',['id'=>$userId]);
```

当不使用switchDataBase时即使用第一个配置文件中的数据库。

如果你使用Model类，可以使用如下命令切换数据库

```php
	$database=(array)Config::getConfig('database');
	$model=new Model($database['other']);
```



## 错误页面

默认的错误页面在/Lib/Http/views目录下，你可以通过修改site.json中的http_exception_path字段来修改目录。其中404为自动调用，其余均可手动通过HTTP库手动调用。

##使用HTTP协议

大多数情况下你都不需要使用HTTP协议，但如果当你需要构建一个RESTful API的时候也许需要用到，因此Trochilidae有简单的HTTP协议使用库提供。例如

```php
    public function indexAction(){
        $view=$this->renderOnly('index.html.twig@HomeBundle:Index',['string'=>'Hello World !']);
        Http::response(200,$view,['Cache-Control'=>'private, max-age=60']);
    }
```

以上功能与示例的Hello World 程序完全一样，唯一有所不同的是你可以自己定义响应头，如上面的Cache-Control，从而可以达到更多的功能。

## 命令行功能

Trochilidae提供了一些内置的命令行，它可以帮助你更好的完成你的项目，主要有如下几个

createEntity <path|file>：生成Entity实体类

updateDataBase：更新数据库结构

clear：清除缓存和路由表

suggest：检测你的代码，并提出可优化的地方。