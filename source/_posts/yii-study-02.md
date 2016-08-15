
---
title: Yii2学习笔记『二』Hello world
date: 2016-08-12 18:48:39
categories: PHP
tags: yii2
---

> 目录
> 1. Yii应用的结构
> 2. Hello world！
> 3. 一个更复杂的应用：表单

<!-- more -->

------

### 1. Yii应用的结构
```
basic/              应用根目录
    composer.json   Composer 配置文件, 描述包信息
    config/         包含应用配置及其它配置
        console.php     控制台应用配置信息
        web.php         Web 应用配置信息
    commands/       包含控制台命令类
    controllers/    包含控制器类
    models/         包含模型类
    runtime/        包含 Yii 在运行时生成的文件,例如日志和缓存文件
    vendor/         包含已经安装的 Composer 包,包括 Yii 框架自身
    views/          包含视图文件
    yii             Yii 控制台命令执行脚本
    
    web/            Web 应用根目录,包含 Web 入口文件
        assets/         包含 Yii 发布的资源文件(javascript 和 css)
        index.php       应用入口文件
```
根据目录结构可以看出，Yii实现了 MVC 设计模式。一般来说，应用中的文件被分为两类，basic/web 下的和其它。前者可直接通过HTTP访问，后者不能也不应该被直接访问。

* 一个应用的静态结构
![应用的静态结构](/img/8c1e501d04f6fac2c45c6526e.png)

  每个应用都有一个入口脚本web/index.php，这是整个应用中唯一可以访问的php脚本。过程如下：入口脚本接受一个web请求并创建应用实例去处理它，应用在它的组件辅助下解析请求，并分派请求至MVC单元，视图使用小部件去创建复杂和动态的用户界面。

* 应用处理请求的生命周期
![应用处理请求的生命周期](/img/1ebd064b0c29334994ba9e7a8.png)
    1. 用户向入口脚本 web/index.php 发起请求。
    2. 入口脚本加载应用配置并创建一个应用实例去处理请求。
    3. 应用通过请求组件解析请求的路由。
    4. 应用创建一个控制器实例去处理请求。
    5. 控制器创建一个操作实例并针对操作执行过滤器。
    6. 如果任何一个过滤器返回失败,则操作退出。
    7. 如果所有过滤器都通过，操作将被执行。
    8. 操作会加载一个数据模型，或许是来自数据库。
    9. 操作会渲染一个视图，把数据模型提供给它。
    10. 渲染结果返回给响应组件。
    11. 响应组件发送渲染结果给用户浏览器。

------

### 2. Hello world！
按照惯例来写一个Hello world。

#### - 第一步，创建操作（controller）

> 操作是用户可以直接访问并执行的对象，操作被组织在控制器中，一个操作的执行结果就是最终用户收到的响应内容。

简单起见，我们在 **controllers/SiteController.php** 里创建，在class里添加如下的方法。

```
public function actionSay($message = 'Hello')
{
    return $this->render('say', ['message' => $message]);
}
```

* 操作ID / action-name： **say**
* 方法名 / actionActionName： **actionSay**
* 路由： site/say


#### - 第二步，创建视图（view）

视图是你用来生成响应内容的脚本，say 视图应该存为 **views/site/say.php** 文件。
```
<?php
use yii\helpers\Html;
?>

<?= Html::encode($message) ?>
```

>当一个操作中调用了 **[[yii\web\Controller::render()|render()]]** 方法时，它将会按 **views/控制器ID/视图名.php** 路径加载 PHP 文件。

#### - 第三步，访问页面

http://localhost:8080/index.php?r=site/say&message=Hello+World
当当当~，通过这个链接访问页面就可以看到我们刚刚创建的Hello World了！

参数解释：

* **r** 代表 **路由**，它是整个应用级的，指向特定操作的独立ID，格式是 **控制器ID/操作ID**，通过路由即可判断出请求由哪个控制器来处理，具体工作由哪个方法来执行。
* **message** 作为参数传入 **actionSay()** 方法，省略时按形参处的默认值处理。

------

### 3. 一个更复杂的应用：表单

#### - 第一步，创建模型（model）

```
<?php

namespace app\models;

use Yii;
use yii\base\Model;

/**
 * EntryForm is the model behind the entry form.
 */
class EntryForm extends Model
{
    public $name;
    public $email;

    /**
     * @return array the validation rules.
     */
    public function rules()
    {
        return [
            // name, email are required
            [['name', 'email'], 'required'],
            // email has to be a valid email address
            ['email', 'email'],
        ];
    }
}
```
解释：

* **yii\base\Model** 是Yii提供的一个模型基类，通常用来标识数据，且与数据表无关。如与数据表有关联，推荐使用 **yii\db\ActiveRecord**，该类继承自 yii\base\Model，增加了数据库处理。
* **rules()** 返回数据验证规则的集合。如果你有一个处理用户提交数据的 EntryForm 对象，可通过 **validate()** 方法触发数据验证，如数据验证失败，**hasErrors()** 将为 true，失败信息可通过 **getErrors()** 获取。

#### - 第二步，创建操作（controller）

依然是在 SiteController 中创建。

```
public function actionEntry()
{
    $model = new EntryForm;
    if ($model->load(Yii::$app->request->post())
        && $model.validate()
    ) {
        return $this->render('entry-confirm', ['model' => $model]);
    } else {
        return $this->render('entry', ['model' => $model]);
    }
}
```

* 首先创建一个 EntryForm 对象。然后尝试从 \$_POST 获取用户提交的数据，由 Yii 的 [[yii\web\Request::post()]] 方法负责获取。
* **Yii::$app** 代表应用实例，它是一个全局可访问的单例，同时也是一个**服务定位器**，能提供request、reponse、db鞥等特定功能的组件。

#### - 第三步，创建视图（view）

视图1 views/site/entry.php
```
<?php
use yii\helpers\Html;
use yii\widgets\ActiveForm;
?>

<?php $form = ActiveForm::begin(); ?>
<?= $form->field($model, 'name') ?>
<?= $form->field($model, 'email') ?>
<div class="form-group">
    <?= Html::submitButton('Submit', ['class' => 'btn btn-primary']) ?>
</div>
<?php ActiveForm::end(); ?>

```

视图2 views/site/entry-confirm.php
```
<?php
use yii\helpers\Html;
?>

<p>You have entered the following information:</p>
<ul>
    <li>
        <label>Name</label>: <?= Html::encode($model->name) ?>
    </li>
    <li>
        <label>Email</label>: <?= Html::encode($model->email) ?>
    </li>
</ul>
```

* 强大的表单部件  [[yii\widgets\ActiveForm|ActiveForm]] ， **begin()** 和 **end()** 分别渲染表单的开始和结束。

#### - 第四步，访问页面

http://localhost:8080/index.php?r=site/entry
当当当~，通过这个链接访问页面就可以看到一个简单的表单了，当不输入或email输入有误的情况下点击提交按钮时，表单会有相应的提示；验证通过后会跳转到成功页面，并将输入的内容打印出来。amazing~

接下来，就可以学习一下数据库操作啦。

--------

学到这里之后发现，Yii的view部分的小部件太强大了好么，作为写过很多smarty的童鞋来说，为什么不早点学学这些 ,,Ծ‸Ծ,,
学无止境~~hoho

