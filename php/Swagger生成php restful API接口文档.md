## Swagger生成php restful API接口文档
#### 背景
> 我们的restful api项目采用yaf框架， 整体结构简单， 我们只需要用swagger扫描 application目录即可。
下面简称我们的php项目为yaf_project.

### 搭建
先说下最终的文档生成流程会是什么样子,以便先有个整体的认识:   
搭建完成后, 整个流程, 从文档生成到前端展现, 大体如下:   

 1. 在php文件中写 swagger 格式的 /** 注释 */
 2. 用 swagger-php 内的 `bin/swagger.phar` 命令扫描 项目application 所在目录, 生成 `swagger.json` 文件
 3.  将`swagger.json`文件拷贝到swagger-ui中index.html指定的目录。
 4.  打开 swagger-ui 所在的 url, 就可以看到文档了. 文档中的各个 api 可以在该网址上直接访问得到数据.

实现此需求，只需要swagger如下的两个项目：

swagger-php: 扫描 php 注释的工具. 内含一个不错的例子.

swagger-ui: 用以将扫描工具生成的 swagger.json 文件内容展示在网页上.

```git
git clone https://github.com/zircote/swagger-php.git 
git clone https://github.com/swagger-api/swagger-ui.git
或者： npm install swagger-ui-dist
```

### 文档生成工具（swagger）部署
> NOTE: swagger-php 只是个工具,放在哪里都可以.

主要工作，解决composer依赖。

因为国内直接用 composer 比较蛋疼,所以最好设置下国内的那个 composer 源.


文档工具部署，需要以下三个命令：

```jsx
cd swagger-php
composer config repo.packagist composer https://packagist.phpcomposer.com 
composer update
```

只要中间不报错,就算部署完成了. 完成后可以生成一份文档试一下.
swagger-php 项目下的 Examples 目录下有一个示例php工程,里面已经用 swagger 格式写了各种接口注释, 我们来尝试生成一份文档.

执行下面命令：

```jsx
cd swagger-php
mkdir json_docs
php ./bin/swagger ./Examples -o json_docs/
```

上面命令会扫描 Examples 目录中的php文件注释, 然后在 `json_docs` 目录下生成 swagger.json 文件.
这个 swagger.json 文件就是前端 `swagger-ui` 用来展示的我们的api文档文件.


### 前端swagger-ui部署
1、将 `swagger-ui` 项目中的 dist 文件夹或 `node_modules`
中的`swagger-ui-dist`文件夹拷贝到 `yaf_project` 根目录下.

> 1、只需要拷贝dist这个文件夹就可以，最好重命名下，比如叫swagger.
> 
> 2、可以允许跨域。

2、参照： [swagger官网](http://swagger.io/docs/swagger-tools/#usage-34)

上述步骤完成后，访问即可。

### 编写PHP注释

`swagger-php` 项目的 Example 中已经有了很多相关例子,照着复制粘贴就可以了.

更具体的相关注释规则的文档,看这里:   http://bfanger.nl/swagger-explained/#/swaggerObject

参考项目： git@arp.gitlab.innertoon.com:PHP/PHP-Standard-Library.git    分支：feature-swagger

####  在yaf项目的application目录中建立Swagger.php文件
> 建立此文件的目的是为了在swagger.json文件中生成一些统一的配置， 该文件不包含任何php代码，只写相应的Swagger注释。

```php
<?php
/**
 * @SWG\Swagger(
 *      schemes={"http"},
 *      host="172.28.50.227:8080",
 *      consumes={"multipart/form-data"},
 *      produces={"application/json"},
 *      @SWG\Info(
 *         version="1.0.0",
 *         title="标准框架",
 *         description="标准框架，swagger应用基础准则",
 *         @SWG\Contact(
 *             email="lifuqiang@syswin.com"
 *         )
 *      ),
 *      
 *      
 *      @SWG\Tag(
 *          name="User",
 *          description="用户操作",
 *      ),
 *      
 *      @SWG\Tag(
 *          name="Forum",
 *          description="论坛操作"
 *      ),
 *      
 *      @SWG\Tag(
 *          name="Topic",
 *          description="话题操作"
 *      )
 * ),
 */
```

解释：   
   1. schemes: 使用协议， 可填多种协议，比如http， https   
   2.  host: 项目地址， 该地址会作为每个接口的人url base， 拼接起来一起作为访问地址  
   3. consumes: 接口默认接收的MIME类型。  
   4. produces: 接口默认的回复类型，比如application/json  
   5. @SWG\Tag:  tag是用来给文档分类，name字段必须唯一。

####  结构体（Model）定义

```php
<?php
/**
 * 论坛model
 * @author lifuqiang
 * @SWG\Definition(
 *      type="object",
 *      @SWG\Xml(name="Forum")
 * )
 */
class ForumModel extends BaseModel 
{
    /**
     * @SWG\Property(format="int32")
     * @var int
     */
    public $id;
    
    /**
     * @SWG\Property(format="string")
     * @var string
     */
    public $name;
}
```

#### Controller 中Action定义

```php
<?php
class TopicController extends Controller
{
    /**
     * @SWG\Post(path="/topic/create",
     *      summary="添加话题",
     *      consumes={"multipart/form-data"},
     *      description="",
     *      tags={"Topic"},
     *      operationId="createTopic",
     *      produces={"application/json"},
     *      @SWG\Parameter(
     *          in="formData",
     *          name="forumId",
     *          description="论坛Id",
     *          required=true,
     *          type="integer",
     *      ),
     *      @SWG\Parameter(
     *          in="formData",
     *          name="title",
     *          description="标题",
     *          required=true,
     *          type="string",
     *      ),
     *      @SWG\Parameter(
     *          in="formData",
     *          name="content",
     *          description="内容",
     *          required=true,
     *          type="string",
     *      ),
     *      @SWG\Parameter(
     *          in="formData",
     *          name="file",
     *          description="图片信息",
     *          required=true,
     *          type="string",
     *      ),
     *      @SWG\Response(response="default",description="操作成功")
     * )
     */
    public function createAction()
    {
    }
    
    /**
     * @SWG\Get(path="/topic/detail/id/{id}",
     *      summary="获取话题信息",
     *      description="根据话题ID获取话题信息",
     *      operationId="getTopicById",
     *      tags={"Topic"},
     *      @SWG\Parameter(
     *          name="id",
     *          in="path",
     *          description="话题id",
     *          required=true,
     *          type="integer",
     *          format="int64",
     *      ),
     *      @SWG\Response(
     *          response=200,
     *          description="操作成功",
     *          @SWG\Schema(ref="#/definitions/TopicModel"),
     *      ),
     *      @SWG\Response(
     *          response="400",
     *          description="参数错误",
     *      ),
     *      @SWG\Response(
     *          response="404",
     *          description="话题不存在"
     *      )
     * )
     */
    public function detailAction()
    {
    }
    
    /**
     * @SWG\Get(path="/forum/list",
     *      summary="获取话题列表",
     *      description="根据论坛ID获取话题列表",
     *      operationId="getTopicList",
     *      tags={"Topic"},
     *      @SWG\Parameter(
     *          name="forumId",
     *          in="query",
     *          description="论坛id",
     *          required=true,
     *          type="integer",
     *          format="int64",
     *      ),
     *      @SWG\Parameter(
     *          name="page",
     *          in="query",
     *          description="页码",
     *          required=true,
     *          type="integer",
     *          format="int64",
     *          default="1",
     *      ),
     *      @SWG\Parameter(
     *          name="pageLimit",
     *          in="query",
     *          description="每页显示数量，默认10",
     *          required=true,
     *          type="integer",
     *          format="int64",
     *          default="10"
     *      ),
     *      @SWG\Response(
     *          response=200,
     *          description="操作成功",
     *          @SWG\Schema(ref="#/definitions/TopicModel")
     *      ),
     *      @SWG\Response(
     *          response="400",
     *          description="参数错误",
     *      ),
     *      @SWG\Response(
     *          response="404",
     *          description="话题不存在"
     *      )
     * )
     */
    public function listAction()
    {
    }
    
    
}

```

### 常用字段简要说明
#### 接口描述（@SWG\Get, @SWG\Post等）常用字段：

```jsx
summary - string  
接口的简要介绍,会显示在接口标头上,不能超过120个字符  
  
description - string  
接口的详细介绍  
  
externalDocs - string  
外部文档链接  
  
operationId - string  
全局唯一的接口标识  
  
consumes - [string]  
接口接收的MIME类型  
  
produces - [string]  
接口返回的MIME类型,如 application/json  
  
schemes -   [string]  
接口所支持的协议,取值仅限: "http", "https", "ws", "wss"  
  
parameters -    [Parameter Object | Reference Object]  
参数列表 
```

#### 参数描述（@SWG\Parameter）常用字段：

```jsx
name - string  
参数名. 通过路径传参(in 取值 "path")时有注意事项,没用到,懒得看了...  
  
in - string  
参数从何处来. 必填. 取值仅限: "query", "header", "path", "formData", "body"  
  
description - string  
参数描述. 最好别太长  
  
type - string  
参数类型. 取值仅限: "string", "number", "integer", "boolean", "array", "file"  
  
required - boolean  
参数是否必须. 通过路径传参(in 取值 "path")时必须为 true.  
  
default - *  
默认值.   
```

> 更具体的相关注释规则的文档,看这里:
http://bfanger.nl/swagger-explained/#/swaggerObject