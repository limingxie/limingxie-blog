---
author: "li_mingxie"
title: "【TLS】TLS握手协议简介"
date: 2922-03-23T07:28:49+08:00
tags: [
    "tls",
    "security",
    "tcp",
    "transport",
]
categories: [
    "network",
]
---

### RDC Manager表结构

> RDC Manager系统中所有表结构及字段注解

### 企业AD信息表(active_directory)

+ **用途**
  + 记录企业AD信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |company_id|guid|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |base_dn|base DN|VARCHAR(255)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |address|ad地址|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |port|ad端口号|SMALLINT(5)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|‘389’|
    |account|AD认证账户|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |password|密码|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |filter||VARCHAR(255)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |email_field|邮箱|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |telephone_field|手机|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |user_logon_name_field|登录名|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |rdn_field||VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |title_field|标题|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |first_name_field|firstname|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |last_name_field|lastname|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |is_ssl_used|是否使用ssl|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|‘0’|
    |sync_start_time|开始同步时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |sync_interval|AD同步间隔时间(ms)|INT(10)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |is_sync_started|是否启动同步(0: false; 1: true)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|‘0’|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 管理员帐号表(admin)

+ **用途**
  + 记录系统管理员基本信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|admin id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业ID|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|管理员姓名|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |login_name|登录帐号|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |password|密码|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |email|管理员邮箱|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |mobile|管理员手机号|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |failed_login_times|认证失败次数|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |is_locked|是否被锁定(0: false; 1: true)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |unlock_time|解锁时间|DATETIME|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |is_deleted|是否被删除(0: false; 1: true)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|
    |is_first_login|是否第一次登录默认是true|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|

### 管理员角色表(admin_role)

+ **用途**
  + 记录系统管理员角色信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |admin_id|管理员id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |role_id|角色id|MEDIUMINT(8)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||

### 应用表(application)

+ **用途**
  + 记录平台上的应用信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|应用id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |application_group_id|应用组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|应用名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|应用描述|VARCHAR(255)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |identifier|唯一标识(scope or 二级应用id or 在线应用标识)|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |logo_url|应用图标地址|VARCHAR(200)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |package_url|应用包上传地址|VARCHAR(200)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |package_name|应用包的名称(包括扩展名)|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |address|在线应用,适配应用,企业小程序地址|VARCHAR(200)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |level|应用级别(适配应用有二级应用)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |type|应用类型(1:在线应用; 2: 适配应用; 3:小程序; 4: 邮箱), 不同的类型有可能存在子表|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |index|排序从1开始|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |parent_application_id|父应用id, 默认为''0''。 如果是集团下发到子公司的应用，此id为集团应用的id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |is_deleted|是否删除|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |is_market_application|是否为市场应用（0：正常策略应用；1：市场应用）|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 适配应用信息表(application_adapter)

+ **用途**
  + 记录应用程序的适配信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |application_id|应用id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|公司id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |scope|studio中为适配包设置的唯一名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |manifest|manifest文件内容|JSON|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |incremental_no|适配包增量更新版本号|INT(11)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |runtime_primary_version|适配包对应的运行时主版本号|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |runtime_version|适配包对应的运行时具体版本|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |is_specific_runtime_used|是否使用指定的运行时（0：不是，如果有新的运行时，客户端需要更新；1：是，不进行更新）|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 应用组表(application_group)

+ **用途**
  + 记录应用程序分组信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|应用组id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |type|分组类型(1: 默认; 2: 标准)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |name|应用组描述|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|应用组名称|VARCHAR(255)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |index|排序从1开始|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |is_deleted|是否删除|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 应用与平台对应关系表(application_platform)

+ **用途**
  + 记录应用程序与平台对应关系信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |application_id|应用id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |platform_id|平台id|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||

### 用户认证表(authorization)

+ **用途**
  + 用户认证表， 存储token等认证信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |user_id|用户id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |refresh_token|客户端与RDCManager的认证refresh_token|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |expiry_date|过期时间|DATETIME|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|
    |device_id|设备id|VARCHAR(50)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 浏览器模式及内核表(browser_mode)

+ **用途**
  + 记录支持的浏览器模式及对应的内核
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id，不是自增的|SMALLINT(5)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |name|浏览器名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |value|浏览器内核|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 企业信息表(company)

+ **用途**
  + 记录企业信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|uuid|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|公司名称|VARCHAR(50)|FALSE|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |domain_name|企业域名|VARCHAR(100)|FALSE|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |manager_server|企业部署地址|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |is_private_development|是否私有部署(0: SAAS; 1: Private)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |edition|企业版本(1.普通版 2.高级版 3.SDk 4.普通+移动 5.高级+移动)|TINYINT(2)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |type|企业类型(1: 正式; 2: POC; 3: 试用) |TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |original_manager_server|hub创建企业时，添加的RDCManager的地址， 用于运行时解密|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |is_deleted|是否被删除|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |parent_company_id|父公司id，顶级公司为''0''|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
     |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|
    |create_date_at_hub|在hub上的创建时间，用于运行时解密|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 公司与设置关联表(company_configuration)

+ **用途**
  + 记录公司与设置的而关联信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |configuration_id|configuration id|INT(10)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |value|值|JSON|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 设置项表(configuration)

+ **用途**
  + 记录设置项的信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|固定id,不AI. 规则: 第一位为category, 第二位为platform, 后面3位为配置号|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |name|名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|描述|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |platform|PC为1, mobile为2，PC&Mobile为9|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |category|分类(1: 企业2: 策略)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |default_value|默认值|JSON|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|

### 部门表(department)

+ **用途**
  + 记录部门信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|部门ID|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|部门名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|部门描述|VARCHAR(255)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |parent_department_id|父部门ID|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |source|数据来源(1: 手动创建; 2: 批量导入; 3: 定制导入; 4: AD导入)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |is_im_group_created|是否默认创建IM聊天组(0: false; 1: true)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |full_department_id|部门全路径(e.g. 云适配id/北京id/产品部id)|VARCHAR(300)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |incremental_no|增量更新版本号|MEDIUMINT(8)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |incremental_operation|增量更新操作(1: 创建; 2: 编辑; 3: 删除)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |user_device_num|部门内用户最大设备数|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'3'|
    |is_limit_user_device_num|是否开启最大设备限制数|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |is_deleted|是否删除(0:false; 1: true)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 部门排序表(department_department_order)

+ **用途**
  + 记录部门排序信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(11)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE||
    |department_id|部门id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |sub_department_id|子部门id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |index|排序|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|

### 部门表中的用户排序(department_user_order)

+ **用途**
  + 记录部门表中的用户排序信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |department_id|部门id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |user_id|用户id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |index|排序|INT(11)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 设备基础表(device)

+ **用途**
  + 记录设备详细信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|设备唯一id，目前看都是<= 36， 设置50， 以备扩展|VARCHAR(50)|TRUE|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |user_id|所属用户id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |platform|平台(1: PC; 2: Mobile)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |model|设备型号|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|设备名称|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |operating_system|操作系统版本|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |operating_system_architecture|操作系统架构(1: 32位; 2: 64位)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |client_version|客户端版本|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |is_disabled|是否被禁用|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |is_deleted|是否被删除|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |is_deleted_by_end_user|用户是否在客户端删除了此设备|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |is_activated|是否被激活|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |activated_time|激活时间|DATETIME|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |last_login_time|上次登录时间|DATETIME|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|
    |is_temp|是否是临时设备|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|

### 文档模式(documentation_mode)

+ **用途**
  + 记录文档模式信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id|SMALLINT(5)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |browser_mode_id|浏览器模式id|SMALLINT(5)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |name|浏览器名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |value|浏览器内核值|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 业务实体表(entity_version)

+ **用途**
  + 记录业务实体的版本号，增量更新时使用(策略组, 应用, 用户, 部门, 配置等)
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |company_id|企业id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |type|业务实体类型(1: 策略; 2: PC设置; 3: 应用; 4:部门; 5: 用户; 6: SWA)|TINYINT(3)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |version|业务试题的版本号|MEDIUMINT(9)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 用户虚拟组表(group)

+ **用途**
  + 记录用户虚拟组(e.g. 云监会)
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|虚拟组guid|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|虚拟组名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|虚拟组描述|VARCHAR(255)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |is_deleted|是否删除(0: false; 1: true)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 虚拟组与用户对应关系(group_user)

+ **用途**
  + 记录虚拟组与用户对应关系
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id自增|INT(11)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |group_id|虚拟组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |user_id|用户id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 公司证书表(license)

+ **用途**
  + 记录公司对应的证书信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |company_id|企业id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |content|证书加密字段|MEDIUMTEXT|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |is_valid|是否有效(0: 无效; 1: 有效)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |license_number|授权数|INT(10)|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|NULL|
    |start_time|服务开始日期|DATETIME|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |expiration_time|过期时间|DATETIME|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 日志表(log)

+ **用途**
  + 日志表, 后续需要定时删除(删除逻辑需要考虑报表模块)
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|log自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |category|日志分类(管理员还是客户端)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |operator_id||VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |type|日志具体类型|SMALLINT(5)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |log|日志信息|VARCHAR(1000)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|

### 客户端登录日志表(log_client_login)

+ **用途**
  + 记录客户端登录日志信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|log自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |device_type|设备类型 (1: 常用设备; 2: 新设备)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |device_id|设备id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |device_model|设备型号|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |device_operating_system|设备操作系统|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |device_client_version|设备客户端版本|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |device_platform|设备平台|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |ip|客户ip地址|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |location|登录地点|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |location_type|登录地点类型(1: 常驻地点; 2: 非常驻地点)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |failed_times_per_day|每天登陆失败次数|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |status|登录结果(1: 成功; 2: 失败)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |user_id|登录用户id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |username|用户姓名|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |email|用户email|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |mobile|用户手机号码|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |risk_type|登录风险(1: 低; 2: 一般; 3: 敏感)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |department_id|部门id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |department_name|部门名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|

### 应用支持的平台表(platform)

+ **用途**
  + 记录应用支持的平台信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id| PC：1； mobile：2|INT(3)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |name|PC or Iot or Android Pad or Android Phone or iPhone or iPad|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### rdc配置表(rdc_configuration)

+ **用途**
  + 记录rdc配置信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|固定id,不自增|SMALLINT(5)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |name|配置项|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |value|配置|VARCHAR(255)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |type|配置类型(1: string; 2: boolean; 3: number; 4:json)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||  

### 角色表(role)

+ **用途**
  + 记录角色信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|不自增（规则第一位是type）|MEDIUMINT(8)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |type|类型（1代表管理员角色）|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |name|角色名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 私有部署运行时表(runtime)

+ **用途**
  + 私有部署运行时表
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |version|运行时版本|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|描述信息|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |package_name|运行时包的名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |package_url|运行时上传地址|VARCHAR(300)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|

### IE安全级别模版表(security_level_template)

+ **用途**
  + 记录IE安全级别模版信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|安全级别id. 1: 高; 2: 中高; 3: 中; 4以上都是自定义|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |level|安全级别(信任站点. 1: 低, 2: 中低, 3: 中)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |value|安全级别json|JSON|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |type|类型(1: 信任站点)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||  

### sessions表(sessions)

+ **用途**
  + 记录sessions信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |session_id|session_id|VARCHAR(128)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |expires||INT(11)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |data||TEXT|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
  
### 短信表(sms)

+ **用途**
  + 记录系统短信信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |type|类型: 1是验证码； 2以后的待定|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |content|短信内容，###隔开多个内容|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |mobile|手机号码|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||  
    |expiration_time|过期时间|DATETIME|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||  
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 单点登录表(sso)

+ **用途**
  + 记录单点登陆信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |is_enabled|是否启用(0: 关闭; 1: 启用)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |request_method|请求方式(1: get; 2: post)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |url|请求地址|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |request_content_type|请求数据格式(1: JSON; 2: XML)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |xml_template|请求xml|VARCHAR(1000)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |username_identity|json格式用户名标识|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |password_identity|json格式密码标识|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |response_content_type|数据返回部分数据格式(1: JSON; 2: XML)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |token_path|token路径|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |token_position|token存在位置(1: url; 2: header; 3: body)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### sso客户端相关配置表(sso_client)

+ **用途**
  + 记录sso客户端相关配置信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |sso_id|sso主表id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |expire_second|认证标识过期时间|INT(10)|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|NULL|
    |type|类型(1: cookie; 2: url; 3: header)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |frequency_type|1: 仅登录时获取一次; 2: 每次访问web应用时都重新获取; 3: 每隔n秒重新获取|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |interval|时间间隔(每隔n秒获取)|INT(10)|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|NULL|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### cookie属性表(sso_client_cookie)

+ **用途**
  + sso_client表中选择cookie类型时, 需要记录cookie的属性
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |sso_id|sso主表id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|name|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |domain|domain|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |path|path|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |is_http_only|http only(0: false; 1: true)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |is_secure|secure(0: false; 1: true)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|

### sso_client中host表(sso_client_host)

+ **用途**
  + sso_client表中选择url或者header类型时, 需要记录host列表集合
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |sso_id|sso_client表id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|应用系统或者网站名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |url|应用系统或网站的host地址|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |parameter||VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 策略组表(strategy)

+ **用途**
  + 记录用户策略信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|策略id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|策略名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|描述|VARCHAR(255)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |weight|权重|SMALLINT(5)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |platform|平台(PC or 移动 or pad)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |current_step|1: 基本信息; 2: 功能配置; 3: 应用配置; 4: 用户配置|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |is_deleted|是否删除|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 策略组应用关联表(strategy_application)

+ **用途**
  + 记录策略组应用关联信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id,自增|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |strategy_id|策略组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |application_id|应用id(如果选了部分二级应用需要包括父应用id)|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 策略配置信息表(strategy_configuration)

+ **用途**
  + 记录与策略相关的配置信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id,自增|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |strategy_id|策略组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |configuration_id|配置id|INT(10)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |value|值|JSON|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 策略与部门关联表(strategy_department)

+ **用途**
  + 记录策略与部门的关联信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id,自增|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |strategy_id|策略组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |department_id|部门id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 策略与用户虚拟组关联表(strategy_group)

+ **用途**
  + 记录策略与用户虚拟组的关联信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id,自增|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |strategy_id|策略组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |group_id|虚拟组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 策略组与用户关联表(strategy_user)

+ **用途**
  + 记录策略与用户的关联信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id,自增|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |strategy_id|策略组id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |user_id|用户id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 密码代填表(swa)

+ **用途**
  + 记录密码等信息，帮助用户代填
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id|VARCHAR(36))|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |application_id|应用id|VARCHAR(36)|FALSE|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |type|swa类型(1: 密码管家; 2: AD代理认证)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |login_type|用户登录方式(1: 统一认证; 2: 用户自定义)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |login_field|登录字段(1: 用户登录名; 2: 邮箱; 3: 邮箱前缀; 4: 手机号)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |url|登录地址或者host地址|VARCHAR(500)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |is_customized_char_used|是否存在自定义前缀或者后缀(0: 否; 1: 是)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |prefix|前缀|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |suffix|后缀|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### SWA-密码管家(swa_keeper)

+ **用途**
  + 记录SWA-密码信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |swa_id|swa外键|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |username_xpath|用户名的xpath|VARCHAR(300)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |password_xpath|密码的xpath|VARCHAR(300)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |login_button_xpath|登录按钮的xpath|VARCHAR(300)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |login_page_type|页面登录类型（1：常规登录；2：iframe嵌套登录）|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |iframe_xpath|iframe的xpath|VARCHAR(300)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|

### 用户表(user)

+ **用途**
  + 用户信息表
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |department_id|部门id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|用户姓名|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |password|用户密码|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |email|用户email|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |mobile|用户手机|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |login_name|用户登录账户|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |title|岗位名称|VARCHAR(200)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |avatar_path|头像路径|VARCHAR(200)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |employee_no|员工工号|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |person_level|人员级别|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |locality|工作地点|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |telephone|办公电话|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |source|数据来源(1: 手动创建; 2: 批量导入; 3: 定制导入; 4: AD导入)|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |is_deleted|是否删除(0: false; 1: true)|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |is_activated|是否已经激活|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |activated_time|激活时间|DATETIME|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |is_disabled|是否被禁用|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |last_login_time|最近登录时间|DATETIME|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |failed_login_times|失败认证次数|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |device_num|用户最大设备数|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'3'|
    |is_limit_device_num|是否启用用户最大设备数|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'3'|
    |temp_device_num|用户临时设备数|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|'0'|
    |temp_device_num_expire_time|用户临时设备数失效时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|
    |ad_dn|ad域中的distinguishName属性|VARCHAR(200)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |extension1|扩展字段1|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |extension2|扩展字段2|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |extension3|扩展字段3|VARCHAR(100)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |extension4|扩展字段3|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |extension5|扩展字段3|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|

### 用户生效的策略表(user_active_strategy)

+ **用途**
  + 记录用户生效的策略(权重最高的)
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |user_id|用户id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |platform|用户策略属于的平台|TINYINT(3)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |strategy_id|策略组id|VARCHAR(36)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |entity_version||MEDIUMINT(9)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 用户与市场应用对应关系表(user_market_application)

+ **用途**
  + 记录用户与市场应用对应关系信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |user_id|用户id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |application_id|市场应用id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 产品版本表(version)

+ **用途**
  + 记录产品(显示在产品中)与dev(开发版本主要用于兼容)版本号
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|自增id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |version|产品版本号,显示在产品上|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |dev_version|研发版本, 兼容处理|INT(11)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|

### mac设备表(device_mac)

+ **用途**
  + 记录登陆设备得Mac信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |record_id|自增列|INT(11)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE||
    |id|MAC地址.由于iOS要用device id故保持同步|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |device_id|MAC地址.由于iOS要用device id故保持同步|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |company_id|企业id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |platform|平台(1: PC;2:iOS;3:Android)|TINYINT(3)|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|NULL|
    |is_disabled|是否被禁用|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |is_deleted|是否被删除|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 设备限制规则(device_limit_rule)

+ **用途**
  + 记录设备限制规则
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |record_id|自增列|INT(11)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE||
    |id|规则ID|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |name|规则名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |type|规则类型;0:部门;1:用户|TINYINT(1)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |user_id|用户ID|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|NULL|
    |company_id|公司id|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE||
    |department_id|部门ID,也有可能为公司ID|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|NULL|
    |device_num|设备数量|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |is_temp|是否为临时规则1:临时;0:非临时|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'0'|
    |seq|规则顺序|TINYINT(3)|FALSE|TRUE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|'1'|
    |expire_time|失效时间|VARCHAR(50)|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|FALSE|FALSE|NULL|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 执行设备限制策略时的临时表(temp_device)

+ **用途**
  + 执行设备限制策略时的临时表
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |user_id|用户id|VARCHAR(36)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|NULL|
    |department_id|部门id|VARCHAR(36)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|NULL|  
    |fixed_num|固定设备数量|SMALLINT(6)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|'0'|
    |temp_num|临时设备数量|SMALLINT(6)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|'0'|
    |company_id|企业id|VARCHAR(36)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|NULL|
    |session_id|当前自定义的session_id|VARCHAR(36)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|TRUE|FALSE|NULL|
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|

### 网关(gateway)

+ **用途**
  + 记录网关信息的表
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|网关id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|单位id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||  
    |name|网关名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|描述|VARCHAR(255)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |host|网关地址|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |state|网关状态 0 正常 1 离线 2 异常|TINYINT(4)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'0'|
    |admin_id|创建人id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 网关应用关联表(gateway_application)

+ **用途**
  + 记录网关和应用的关联信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |gateway_id|网关id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||  
    |application_id|应用id|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||

### 业务服务器地址(gateway_host)

+ **用途**
  + 记录网关信息的表
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|网关id|VARCHAR(36)|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |company_id|单位id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||  
    |name|网关名称|VARCHAR(50)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |protocol|http or https|VARCHAR(10)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |description|描述|VARCHAR(255)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |host|服务器地址|VARCHAR(100)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |application_id|应用id|VARCHAR(36)|FALSE|TRUE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |admin_id|创建人id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |create_date|创建时间|DATETIME|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP|
    |last_update|更新时间|TIMESTAMP|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP|

### 网关通用应用关联表(gateway_host)

+ **用途**
  + 记录网关和通用应用的关联信息
+ **字段属性含义及注解**

    |ColumnName|Describe|DataType|PrimaryKey|NotNull|Unique|Binary|Unsigned|ZeroFill|AutoIncrement|Generated|Default|
    |:-----|:-----|:-----|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
    |id|记录id|INT(10)|TRUE|TRUE|FALSE|FALSE|TRUE|FALSE|TRUE|FALSE||
    |gateway_id|网关id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||  
    |application_generic_id|通用应用id|VARCHAR(36)|FALSE|TRUE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE||
    |internal_link|内部链接 xxx.test.com,192.168.2.97|VARCHAR(500)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|NULL|
    |isExternalLinkEnabled|外网下，内部链接和内网地址是否走网关|INT(4)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|
    |isInternalLinkEnabled|内网下，内部链接和内网地址是否走网关|INT(4)|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|FALSE|'1'|

### 备注
