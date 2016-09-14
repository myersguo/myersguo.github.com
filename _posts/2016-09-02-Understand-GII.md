---
layout: wp
title: YII中的GII工作原理
---

### controlmap ###  

Gii模块的流程入口在controller map，由findGenerators生成：  

````
protected function findGenerators()
    {
        $generators=array();
        $n=count($this->generatorPaths);
        for($i=$n-1;$i>=0;--$i)
        {
            $alias=$this->generatorPaths[$i];
            $path=Yii::getPathOfAlias($alias);
            if($path===false || !is_dir($path))
                continue;

            $names=scandir($path);
            foreach($names as $name)
            {
                if($name[0]!=='.' && is_dir($path.'/'.$name))
                {
                    $className=ucfirst($name).'Generator';
                    if(is_file("$path/$name/$className.php"))
                    {
                        $generators[$name]=array(
                            'class'=>"$alias.$name.$className",
                        );
                    }

                    if(isset($generators[$name]) && is_dir("$path/$name/templates"))
                    {
                        $templatePath="$path/$name/templates";
                        $dirs=scandir($templatePath);
                        foreach($dirs as $dir)
                        {
                            if($dir[0]!=='.' && is_dir($templatePath.'/'.$dir))
                                $generators[$name]['templates'][$dir]=strtr($templatePath.'/'.$dir,array('/'=>DIRECTORY_SEPARATOR,'\\'=>DIRECTORY_SEPARATOR));
                        }
                    }
                }
            }
        }
        return $generators;
    }

````

结果生成： 

````
gii.generators.controller.ControllerGenerator
gii.generators.crud.CrudGenerator
gii.generators.form.FormGenerator
gii.generators.model.ModelGenerator
gii.generators.module.ModuleGenerator
````

以上几个控制器入口。下面的模板生成以ModelGenerator为例介绍.  



### 登录验证  ###

GiiModule中ip和用户密码的限制：  

```
protected function allowIp($ip)
    {
        if(empty($this->ipFilters))
            return true;
        foreach($this->ipFilters as $filter)
        {
            if($filter==='*' || $filter===$ip || (($pos=strpos($filter,'*'))!==false && !strncmp($ip,$filter,$pos)))
                return true;
        }
        return false;
    }

public function beforeControllerAction($controller, $action)
    {
        if(parent::beforeControllerAction($controller, $action))
        {
            $route=$controller->id.'/'.$action->id;
            if(!$this->allowIp(Yii::app()->request->userHostAddress) && $route!=='default/error')
                throw new CHttpException(403,"You are not allowed to access this page.");

            $publicPages=array(
                'default/login',
                'default/error',
            );
            if($this->password!==false && Yii::app()->user->isGuest && !in_array($route,$publicPages))
                Yii::app()->user->loginRequired();
            else
                return true;
        }
        return false;
    }


```


TODO 



### 模板生成 ###  



### 附录  ###

####  CWebApplication 注释 ####  
 

```

/**
 * CWebApplication extends CApplication by providing functionalities specific to Web requests.
 *
 * CWebApplication manages the controllers in MVC pattern, and provides the following additional
 * core application components:
 * <ul>
 * <li>{@link urlManager}: provides URL parsing and constructing functionality;</li>
 * <li>{@link request}: encapsulates the Web request information;</li>
 * <li>{@link session}: provides the session-related functionalities;</li>
 * <li>{@link assetManager}: manages the publishing of private asset files.</li>
 * <li>{@link user}: represents the user session information.</li>
 * <li>{@link themeManager}: manages themes.</li>
 * <li>{@link authManager}: manages role-based access control (RBAC).</li>
 * <li>{@link clientScript}: manages client scripts (javascripts and CSS).</li>
 * <li>{@link widgetFactory}: creates widgets and supports widget skinning.</li>
 * </ul>
 *
 * User requests are resolved as controller-action pairs and additional parameters.
 * CWebApplication creates the requested controller instance and let it to handle
 * the actual user request. If the user does not specify controller ID, it will
 * assume {@link defaultController} is requested (which defaults to 'site').
 *
 * Controller class files must reside under the directory {@link getControllerPath controllerPath}
 * (defaults to 'protected/controllers'). The file name and the class name must be
 * the same as the controller ID with the first letter in upper case and appended with 'Controller'.
 * For example, the controller 'article' is defined by the class 'ArticleController'
 * which is in the file 'protected/controllers/ArticleController.php'.
 *
 * @property IAuthManager $authManager The authorization manager component.
 * @property CAssetManager $assetManager The asset manager component.
 * @property CHttpSession $session The session component.
 * @property CWebUser $user The user session information.
 * @property IViewRenderer $viewRenderer The view renderer.
 * @property CClientScript $clientScript The client script manager.
 * @property IWidgetFactory $widgetFactory The widget factory.
 * @property CThemeManager $themeManager The theme manager.
 * @property CTheme $theme The theme used currently. Null if no theme is being used.
 * @property CController $controller The currently active controller.
 * @property string $controllerPath The directory that contains the controller classes. Defaults to 'protected/controllers'.
 * @property string $viewPath The root directory of view files. Defaults to 'protected/views'.
 * @property string $systemViewPath The root directory of system view files. Defaults to 'protected/views/system'.
 * @property string $layoutPath The root directory of layout files. Defaults to 'protected/views/layouts'.
 *
```
