---
layout: wp
title: Role-Based Access Control
---

这里以YII的`authmanger`模块为例来介绍。   

权限表：  


```
#定义操作
create table `AuthItem`
(
   `name`                 varchar(64) not null,
   `type`                 integer not null,
   `description`          text,
   `bizrule`              text,
   `data`                 text,
   primary key (`name`)
) engine InnoDB;
#定义操作所属角色
create table `AuthItemChild`
(
   `parent`               varchar(64) not null,#上级权限，一般为角色名
   `child`                varchar(64) not null,#
   primary key (`parent`,`child`),
   foreign key (`parent`) references `AuthItem` (`name`) on delete cascade on update cascade,
   foreign key (`child`) references `AuthItem` (`name`) on delete cascade on update cascade
) engine InnoDB;
```

角色表(权限分配表):   

```
create table `AuthAssignment`
(
   `itemname`             varchar(64) not null,#角色名，权限名
   `userid`               varchar(64) not null,#用户ID,
   `bizrule`              text,#
   `data`                 text,
   primary key (`itemname`,`userid`),
   foreign key (`itemname`) references `AuthItem` (`name`) on delete cascade on update cascade
) engine InnoDB;
```

权限检查：   

1. 获取用户的所有角色；
2. 获取权限的信息，如果改权限指定了特定规则(bizrule），则检查该规则；   
   获取权限所在角色，如果用户具有该角色，则放行。   

用PHP简单描述就是：  

```
#权限描述
$auth_item = array(
    'AModule.BController.CAction' => 'A Action',
    'AModule.BController.CBction' => 'B Action',
    'AModule.BController.CCction' => 'C Action',
    'AModule.BController.CDction' => 'D Action',
    'AModule.BController.CEction' => 'E Action',
    'AModule.BController.CFction' => 'F Action',
);
#权限-角色关系
$auth_item_child = array(
    'AModule.BController.CAction' => 'admin,leader',
    'AModule.BController.CBction' => 'admin',
    'AModule.BController.CCction' => 'admin',
    'AModule.BController.CDction' => 'admin',
    'AModule.BController.CEction' => 'admin',
    'AModule.BController.CFction' => 'admin',
);
#用户角色
$auth_assignment = array(
    'user_1' => 'admin,leader',
    'user_2' => 'admin'
);
```



假如，我们有一个用户表：   

```
user_id,  
roles
```

我们需要做的就是定义一个权限检查规则：   

roles下对应哪些action,怎么检查：     

```
public function checkPermission($data) 
{
    //1. 获取用户所有的权限
    $cur_roles = $user->getUserRoles();
    $cur_roles = explode(',', cur_roles);
    //2. 获取当前的权限信息
    $permission = $module. $controller. $action;
    //特定规则检查
    $this->doCheck($data);
    //获取权限所属的角色
    $roles = $this->getRole($permission);

    foreach($roles as $role){
        if($role == '*')//一般走不到这里
            return true;
        if(isset($cur_roles[$role])){
            return true;
        }
    }
    return false;
}
//特殊处理，比如，检查是否在DB中
public function doCheck($data)
{
    
}


```



