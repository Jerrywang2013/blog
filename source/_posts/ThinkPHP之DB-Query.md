---
title: ThinkPHP之DB::Query
date: 2024-04-27 17:16:13
tags:
---

Query是ThinkPHP数据库操作的一个非常重要的类，大部分查询相关的操作功能都是通过此类来实现的，本文主要记录ThinkPHP5.0 Query类的部分重要函数。



# Query文件路径

``` thinkphp\library\think\db\Query.php ```



# 重要函数
* **Query::table**
```php
    /**
     * 指定当前操作的数据表
     * @access public
     * @param mixed $table 表名
     * @return $this
     */
    public function table($table)
```


**描述：**设置当前操作的表，内部主要设置options的table属性：$this->options['table'] = $table;

**范例：**Db::table('fa_admin')->where("id", 1)->find();

* **Query::name**
```php
    /**
     * 指定默认的数据表名（不含前缀）
     * @access public
     * @param string $name
     * @return $this
     */
    public function name($name)
```


**描述**：设置当前操作的表，和Query::table函数不同的是$name是不带前缀(前缀配置dbconfig['prefix'])的表名。

**范例**：**Db::name('admin')->where("id", 1)->find();

**备注:** 也可以用helper函数db("admin")来获取dbconnection对象，例如：

```php
	db("admin")->where("id", 1)->find();
```

* **Query::value**
```php
    /**
     * 得到某个字段的值
     * @access public
     * @param string $field   字段名
     * @param mixed  $default 默认值
     * @param bool   $force   强制转为数字类型
     * @return mixed
     */
    public function value($field, $default = null, $force = false)
```


**描述：**查询符合条件的某个记录的某个字段的值，注意只查询一条记录(limit(1))

**范例：** Db::table('fa_admin')->where("id", 1)->value("uername");

* **Query::column**
```php
    /**
     * 得到某个列的数组
     * @access public
     * @param string $field 字段名 多个字段用逗号分隔
     * @param string $key   索引
     * @return array
     */
    public function column($field, $key = '')
```
**描述：**指定符合条件记录的指定字段
**范例：** Db::table('fa_admin')->column("nickname, id, nickname, username");
**备注：** $field是以逗号分隔的字段名字符串，形如"field1, field2..."。
column()函数会将$field去重；其次如果指定了$key，则以$key为返回记录的key；否则以$field字符串中第一个字段作为key

* **Query::find**
```php
    /**
     * 查找单条记录
     * @access public
     * @param array|string|Query|\Closure $data
     * @return array|false|\PDOStatement|string|Model
     * @throws DbException
     * @throws ModelNotFoundException
     * @throws DataNotFoundException
     */
    public function find($data = null)
```


**描述：**查找单挑记录，返回的是

**范例：** Db::name('admin')->where("id", 1)->find();

* **Query::chunk**
```php
    /**
     * 分批数据返回处理
     * @access public
     * @param integer  $count    每次处理的数据数量
     * @param callable $callback 处理回调方法
     * @param string   $column   分批处理的字段名
     * @param string   $order    排序规则
     * @return boolean
     * @throws \LogicException
     */
    public function chunk($count, $callback, $column = null, $order = 'asc')
```
**描述：**分批处理数据
**范例：**

```php
    Db::table('think_user')->chunk(100, function($users) use(&$outer) {
        foreach ($users as $user) {
            // 处理结果集...
            if($user->status==0){
                return false;
            }
        }
    });
```
**说明：** 

1.  可以通过callback函数return false终止后续数据集的处理。这里，这里我们使用use将外部变量$outer传递给
   匿名函数，php中变量按照值传递，而不是引用，所以匿名函数内部的修改不会影响到外部$outer变量。这里我们如果想在
   匿名函数内部修改$outer，需要用**&$outer**。
2.  一般用于命令行处理db数据，不适合web访问处理大量数据，容易造成超时。



* **Query::where**
```
    /**
     * 指定AND查询条件
     * @access public
     * @param mixed $field     查询字段
     * @param mixed $op        查询表达式
     * @param mixed $condition 查询条件
     * @return $this
     */
    public function where($field, $op = null, $condition = null)
    {
        $param = func_get_args();
        array_shift($param);
        $this->parseWhereExp('AND', $field, $op, $condition, $param);
        return $this;
    }
```
**描述：**设置AND查询条件，使用形式：where('字段名', '表达式', '查询条件')
**表达式：** 
        **数值关系比较：** = | <> | > | >= | < | <=

​	**时间：** > TIME | < TIME | >= TIME | <= TIME 

​        **其它：** [NOT] LIKE | [NOT] BETWEEN | [NOT] IN | [NOT] NULL | [NOT] EXISTS | [NOT] REGEXP | [NOT] BETWEEN TIME 

​	**EXP：** exp表达式

**范例：**

- **[NOT] LIKE** 字符串模糊查询，**支持数组**

  **示例：**where('name', 'like', '%ab%') | where('name', 'like', ['%ab', 'php%']) 

- **[NOT] BETWEEN**

  **示例：**where('id', 'between', '1,8') | where('id', 'between', [1,8])

- **[NOT] In**

  **示例：**where('id', 'in', '1,2,3') | where('id', 'in', [1,2,3]) | where('id', 'not in', [1,2,3])

- **[NOT]NULL**         

  **示例：**where('name', 'null') | where('name', 'not null')

- **时间查询**

  where('createtime', '> time', '2006-1-1');

  where('createtime', '< time', '2006-1-1'); 

  where('createtime', 'between time', ['2006-1-1','2006-2-1']);

​	**>time | <time**
​	和'>|<'类似，不同的是用>time | <time之类的操作符，后面的条件可以用形如**today** | **last week** | **-2 hour**之类的时间区间，

​	例如：where('createtime', '-2 hour')

​	**Query::whereTime**用法和where类似，不同的是当参数3($range)为null时，$op可以用特殊字段来表示时间区间，支持的特殊日期时间区间格式有：

```php
    where('birthday', 'today')表示日期范围是今天
    where('birthday', 'yesterday')表示日期范围是今天
    where('birthday', 'week')表示日期范围是本周
    where('birthday', 'last week')表示日期范围是上周
    where('birthday', 'month')表示日期范围是本月
    where('birthday', 'last month')表示日期范围是上月
    where('birthday', 'year')表示日期范围是本年
    where('birthday', 'last year')表示日期范围是上年
    whereTime('create_time','-2 hours') 表示两小时以内 等价于 where('create_time', '>', '-2 hours');
```

- **EXP**

  **使用：**

  ​	where('字段名', 'exp', '条件字符串')；

  ​	where('字段名', null, Expression())  可以用DB::raw("查询字符串")构造Expression形式的查询条件

  **示例：**

  ​	where('id', 'exp', 'in(1,2,3)') **[推荐使用]** ；等价于where('id', 'in', '1,2,3') ;

  ​	where('id', null, DB::raw("FIND_IN_SET(1, id)"))，其内部也是转为where('id', 'exp', 'in (1,2,3)');

  **说明：**exp查询条件不会被当做字符串，可以使用任何sql语法，包括函数和字段名称。

  

**注意：**使用字符串条件查找最好使用***预处理机制***，确保安全，例如：

```php
Db::table('think_user')->where("id=:id and username=:name")
	->bind(['id'=>[1,\PDO::PARAM_INT],'name'=>'thinkphp'])
 	->select();
```



* **Query::whereOr**
```
    /**
     * 指定OR查询条件
     * @access public
     * @param mixed $field     查询字段
     * @param mixed $op        查询表达式
     * @param mixed $condition 查询条件
     * @return $this
     */
    public function whereOr($field, $op = null, $condition = null)    
```
**描述：**设置Or查询条件




* **Query::whereXOR**
```
    /**
     * 指定XOR查询条件
     * @access public
     * @param mixed $field     查询字段
     * @param mixed $op        查询表达式
     * @param mixed $condition 查询条件
     * @return $this
     */
    public function whereXor($field, $op = null, $condition = null)
```
**描述：**设置XOR查询条件




* **Query::parseWhereExp**
```php
    /**
     * 分析查询表达式
     * @access public
     * @param string                $logic     查询逻辑 and or xor
     * @param string|array|\Closure $field     查询字段
     * @param mixed                 $op        查询表达式
     * @param mixed                 $condition 查询条件
     * @param array                 $param     查询参数
     * @param  bool                 $strict    严格模式
     * @return void
     */
    protected function parseWhereExp($logic, $field, $op, $condition, $param = [], $strict = false)
```


**描述：**查询条件表达式解析。

**示例：**

* **And条件**
    where("id", 25)
    where("id = 25")
    where("id", '=', 25)
    where("id", "in", [1,2,3])
    
* **Or条件**
    whereOr("id", 25)
    
* Xor条件
  
    whereXor
    
* null条件
  
    whereNull | whereNotNull
    
* time条件
  
    whereTime
    
* raw条件
  
    whereRaw
    
* Exists
  
    whereExists | whereNotExists
    
* In
  
    whereIn | whereNotIn
    
* like
  
    whereLike | whereNotLike
    
* between
  
    whereBetween | whereNotBetween
    
* EXP
  
    whereExp

**注意：**

​	同一级条件中，And和Or平级，如果想实现Or的关系，一般用闭包查询，将Or的条件包在闭包里头，然后整体和外围的条件And
例如：想实现condA and condB & (condC or condD)，就将condC/condD通过闭包（内部or）然后和condA/condB一起链如查询。

```php
$where_extra = function($query) {
    $query->where('id', '=', 3)
        ->whereOr('id', '=', 4);
}
$result = Db::table('fa_admin')->where("id", "=", 25)->where($where_extra)
    ->field(["id", "username" => "name"])
    ->fetchSql(true)
    ->select();      

// 生成的查询sql： "SELECT `id`,`username` AS `name` FROM `fa_admin` WHERE  `id` = 25  AND (  `id` = 3 OR `id` = 4 )"
```


* **Query::fetchSql**

```
    /**
     * 获取执行的SQL语句
     * @access public
     * @param boolean $fetch 是否返回sql
     * @return $this
     */
    public function fetchSql($fetch = true)
```
**描述：**$fetch为ture则不执行sql，仅仅返回querySql，方便调试sql语句。



- **Builder::select**

```
    /**
     * 生成查询SQL
     * @access public
     * @param array $options 表达式
     * @return string
     */
    public function select($options = [])
```
**描述：**生成查询SQL语句。



- **Builder::alias**

```
    /**
     * 指定数据表别名
     * @access public
     * @param mixed $alias 数据表别名
     * @return $this
     */
    public function alias($alias)
```
**描述：**设置数据库别名
**范例：**

```php
Db::table("fa_admin")->alias(['fa_admin'=>"admin", "fa_customer"=>"customer"])
    ->join('fa_customer', 'customer.id=admin.id')->select();
Db::table('fa_admin')->where("admin.id", "=", 25)
    ->field("admin.id, admin.username, admin.nickname, staff.mobile, staff.id, staff.post")
    ->alias([
        "fa_admin" => "admin",
        "fa_qingdong_staff" => "staff"
    ])->join('fa_qingdong_staff', 'staff.admin_id = admin.id')
    ->fetchSql(false)
    ->select();
```



# **高级查询**

- **快速查询**

  ​	快捷查询是多个字段使用同一个查询条件的简写，在多个字段之间用|或者&，表示Or和And查询，例如：

  ```php
  Db::table('think_user')
      ->where('name|title','like','thinkphp%')
      ->where('create_time&update_time','>',0)
      ->find();
  ```

  生成的SQL：

  ```
  SELECT * FROM `think_user` WHERE ( `name` LIKE 'thinkphp%' OR `title` LIKE 'thinkphp%' ) AND ( `create_time` > 0 AND `update_time` > 0 ) LIMIT 1
  ```

  

- **区间查询**

  ​	区间查询是同一个字段多个查询条件的简化写法，例如：

  ```php
  Db::table('think_user')
      ->where('name',['like','thinkphp%'],['like','%thinkphp'])
      ->where('id',['>',0],['<>',10],'or')
      ->find();
  ```

  生成的SQL：

  ```
  SELECT * FROM `think_user` WHERE ( `name` LIKE 'thinkphp%' AND `name` LIKE '%thinkphp' ) AND ( `id` > 0 OR `id` <> 10 ) LIMIT 1
  ```

  

- **批量查询**

  ​	批量查询可以进行多个条件的批量条件查询，例如：

  ```php
  Db::table('think_user')
      ->where([
          'name'  =>  ['like','thinkphp%'],
          'title' =>  ['like','%thinkphp'],
          'id'    =>  ['>',0],
          'status'=>  1
      ])
      ->select();
  ```

  生成的SQL：

  ```
  SELECT * FROM `think_user` WHERE `name` LIKE 'thinkphp%' AND `title` LIKE '%thinkphp' AND `id` > 0 AND `status` = '1'
  ```

  

- **闭包查询**

  ​	闭包查询可以很好的执行多个查询条件的与或关系。

  ```
  Db::table('think_user')->select(function($query){
      $query->where('name','thinkphp')
          ->whereOr('id','>',10);
  });
  ```

  生成的SQL：

  ```
  SELECT * FROM `think_user` WHERE `name` = 'thinkphp' OR `id` > 10
  ```

  

- **使用Query对象查询**

  ​	可以事先封装Query对象，并且传入select方法，例如：

  ```
  $query = new \think\db\Query;
  $query->name('user')
      ->where('name','like','%think%')
      ->where('id','>',10)
      ->limit(10);
  Db::select($query);    
  ```

  注意：使用Query对象的话，select方法之前的调用的任何链式操作都是无效的。

  

- **混合查询**

  ​	混合前面的查询方式，比如普通查询、快捷查询、批量查询、闭包查询。

  

- **字符串条件查询**

  ​	对于一些复杂的查询，可以直接使用原生SQL语句查询，例如：

  ```
  Db::table('think_user')
      ->where('id > 0 AND name LIKE "thinkphp%"')
      ->select();
  ```

  为了安全起见，我们可以对字符串查询条件使用参数绑定，例如：

  ```
  Db::table('think_user')
      ->where('id > 0 AND name LIKE "thinkphp%"')
      ->select();
  ```

  

- **快捷方法**

  | 方法              | 作用                 |
  | :---------------- | :------------------- |
  | `whereNull`       | 查询字段是否为Null   |
  | `whereNotNull`    | 查询字段是否不为Null |
  | `whereIn`         | 字段IN查询           |
  | `whereNotIn`      | 字段NOT IN查询       |
  | `whereBetween`    | 字段BETWEEN查询      |
  | `whereNotBetween` | 字段NOT BETWEEN查询  |
  | `whereLike`       | 字段LIKE查询         |
  | `whereNotLike`    | 字段NOT LIKE查询     |
  | `whereExists`     | EXISTS条件查询       |
  | `whereNotExists`  | NOT EXISTS条件查询   |
  | `whereExp`        | 表达式查询           |



# 视图查询

​	视图查询主要是进行多表查询，不需要数据库支持视图。例如：

```
Db::view('User','id,name')
    ->view('Profile','truename,phone,email','Profile.user_id=User.id')
    ->view('Score','score','Score.user_id=Profile.id')
    ->where('score','>',80)
    ->select();
```

生成的SQL语句类似于：

```
SELECT User.id,User.name,Profile.truename,Profile.phone,Profile.email,Score.score FROM think_user User 
INNER JOIN think_profile Profile ON Profile.user_id=User.id 
INNER JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```

默认是inner join，如果需要更改，可以使用

```
Db::view('User','id,name')
    ->view('Profile','truename,phone,email','Profile.user_id=User.id','LEFT')
    ->view('Score','score','Score.user_id=Profile.id','RIGHT')
    ->where('score','>',80)
    ->select();
```

生成的SQL语句类似于：

```
SELECT User.id,User.name,Profile.truename,Profile.phone,Profile.email,Score.score FROM think_user User 
LEFT JOIN think_profile Profile ON Profile.user_id=User.id 
RIGHT JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```

可以使用别名，例如：

```
Db::view('User',['id'=>'uid','name'=>'account'])
    ->view('Profile','truename,phone,email','Profile.user_id=User.id')
    ->view('Score','score','Score.user_id=Profile.id')
    ->where('score','>',80)
    ->select();
```

生成的SQL语句类似于：

```
SELECT User.id AS uid,User.name AS account,Profile.truename,Profile.phone,Profile.email,Score.score 
FROM think_user User INNER JOIN think_profile Profile ON Profile.user_id=User.id 
INNER JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```

可以使用数组的方式定义表名以及别名，例如：

```
Db::view(['think_user'=>'member'],['id'=>'uid','name'=>'account'])
    ->view('Profile','truename,phone,email','Profile.user_id=member.id')
    ->view('Score','score','Score.user_id=Profile.id')
    ->where('score','>',80)
    ->select();
```

生成的SQL语句类似于：

```
SELECT member.id AS uid,member.name AS account,Profile.truename,Profile.phone,Profile.email,Score.score 
FROM think_user member INNER JOIN think_profile Profile ON Profile.user_id=member.id 
INNER JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
```



# 子查询

- **使用select()**

  Query::select()参数为false时，不进行查询而是返回构建的SQL语句，例如：

  ```
  SELECT member.id AS uid,member.name AS account,Profile.truename,Profile.phone,Profile.email,
  Score.score FROM think_user member INNER JOIN think_profile Profile ON Profile.user_id=member.id 
  INNER JOIN think_socre Score ON Score.user_id=Profile.id WHERE Score.score > 80
  ```

  

- **使用fetchSql()**

  ```
  $subQuery = Db::table('think_user')
      ->field('id,name')
      ->where('id','>',10)
      ->fetchSql(true)
      ->select();
  ```

  

- **使用buildSql构造子查询**

  ```
  $subQuery = Db::table('think_user')
      ->field('id,name')
      ->where('id','>',10)
      ->buildSql();
  ```

  生成subQuery结果为：

  ```
  ( SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 )
  ```

  ***注意：此方法会自动加括号，前面两个方法需要手动添加括号。***

  然后使用子查询构造新的查询：

  生成的SQL语句为：

  ```
  SELECT * FROM ( SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 ) a WHERE a.name LIKE 'thinkphp'
  ORDER BY `id` desc
  ```

  

- **使用闭包构造子查询**

  `IN/NOT IN`和`EXISTS/NOT EXISTS`之类的查询可以直接使用闭包作为子查询，例如：

  ```
  Db::table('think_user')
  ->where('id','IN',function($query){
      $query->table('think_profile')->where('status',1)->field('id');
  })
  ->select();
  ```

  生成的SQL语句类似：

  ```
  SELECT * FROM `think_user` WHERE `id` IN ( SELECT `id` FROM `think_profile` WHERE `status` = 1 )
  ```

  又例如：

  ```
  Db::table('think_user')
  ->where(function($query){
      $query->table('think_profile')->where('status',1);
  },'exists')
  ->find();
  ```

  生成的SQL语句类似：

  ```
  SELECT * FROM `think_user` WHERE EXISTS ( SELECT * FROM `think_profile` WHERE `status` = 1 ) 
  ```

  

# 原生查询

- **Query::query方法**

  用于执行SQL查询操作，如果数据非法或者查询错误返回false，否则返回结果集。例如：

  ```
  Db::query("select * from think_user where status=1");
  ```

  

- **Query::execute()**

  execute用于更新和写入数据的sql操作，如果数据非法或者查询错误则返回false ，否则返回影响的记录数。例如：

  ```
  Db::execute("update think_user set name='thinkphp' where status=1");
  ```

  

- **参数绑定**

  支持在原生查询的时候使用参数绑定，包括问号占位符或者命名占位符，例如：

  ```
  Db::query("select * from think_user where id=? AND status=?",[8,1]);
  // 命名绑定
  Db::execute("update think_user set name=:name where status=:status",['name'=>'thinkphp','status'=>1]);
  ```



- **存储过程**

  ```
  $result = Db::query('call sp_query(8)');
  ```

  也可以用参数绑定：

  ```
  $result = Db::query('call sp_query(?)',[8]);
  // 或者命名绑定
  $result = Db::query('call sp_query(:id)',['id'=>8]);
  ```




# 关联查询

- 一对一关联

  ```
  hasOne('关联模型名','外键名','主键名',['模型别名定义'],'join类型');
  ```

  ```
  belongsTo('关联模型名','外键名','关联表主键名',['模型别名定义'],'join类型');
  ```

  例如：customer表通过owner_staff_id关联到staff表，外键名为staff表的主键id，主键名为customer表的owner_staff_id，customer表通过字段owner_staff_id与staff表的id外键关联起来。这里的本地的称之为主键，关联的外部模型的字段称之为外键，这么说可能更容易理解：

  hasOne(‘关联模型', '关联模型的关联字段名', '本地字段名')  其中'本地字段名'和'关联模型的关联字段名'是相关联的。

  ```
  public function ownerStaff() {
  
      return $this->hasOne(Staff::class, 'id', 'owner_staff_id')->field('id,name,group_ids');
  
  }
  ```

  

- 一对多关联

- 多对多关联

- 关联预载入

- 关联统计






# **ThinkPHP5 DB模块可能存在的问题**

- **参数绑定性能**

  比如设置了一个查询条件where('id', 'in', $idlist) 其中$idlist是一个数组格式的id集合，如果$idlist数据量比较大，在解析where参数的时候进行。当参数绑定耗时会比较多，此时可以用字符串的where代替，就是逻辑层直接用$idlist转为字符串$strids，然后where("id in (${strids})");
  **例如：**

  ```php
  $idlist = [1,2,3,4,5,6,7,8,9,10,20,21,22,23,24,25,26,27,28,29,30];
  $strids = implode(',', $idlist);
  $result = Db::table('fa_admin')
      // 当$idlist很大时，用字符串where条件替代
      //->where("id", 'in', $idlist)
      ->where("id in (${strids})")
      ->field(["id", "username" => "name"])
      ->fetchSql(false)
      ->select();
  ```


- **Model结果序列化性能**

  ​	通常情况下如果数据量比较大一般会采取分页机制来减少每次查询和处理的记录集。但是如果用户确实就想一次性查看大量数据比如(1000)条记录，测试发现通过Model查询db获取记录之后调用json(model)会触发Model::jsonSerialize()，进而调用Model::toArray()，此函数对Model中每个属性进行处理，很耗时。可行的优化方案如下：
  
  - **降低查询的fields**
  
    通过fields("xxx")来过滤需要的字段列表，或者通过设置模型的显示属性$visible和隐藏属性$hidden
  
  - **降低pageNum**
  
    毕竟都有分页了，没必要将每页记录数量设置太大
  
  - **优化Model::toArray()**
  
    测试从customer表中查出1000条件记录，代码如下：
  
    ```
    $result = Customer::where("id", 'exp', "> 0")->limit(1000)->select();
    
    $timeprofile[] = Debug::getUseTime();
    
    $result_json = json_encode($result);
    
    $timeprofile[] = Debug::getUseTime();
    ```
  
    打印出的时间：
  
    ​	select耗时：0.03s
  
    ​	json_encode耗时：6.95s
  
    由上可见Model::toArray()耗时非常大。
  
  - **优化Query::select()**
  
    select()会先准备要执行的最终查询sql，然后查询，查询完毕后还会对结果集进行处理，比如创建模型对象，并且用结果集中的每条记录数据来初始化模型对象，此过程也是非常耗时，对于大部分只需要数据并不会后续处理的应用场景而言，可以省略这一步操作。其实，Query::select()如果没有model对象，这一步本来就是可以省略的。

​	

