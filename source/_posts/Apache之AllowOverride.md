title: Apache之AllowOverride
date: 2015-03-02 11:11:04
categories: 零碎知识
tags: [Apache]
---
AllowOverride参数就是指明Apache服务器是否去找.htaccess文件作为配置文件。如果设置为none，那么服务器将直接忽略.htaccess文件，如果设置为all，那么所有在.htaccess中的指令都将被重写。

下面是具体的一些参数的解释：
1. AuthConfig：允许使用所有的权限指令
2. FileInfo：允许使用文件控制类型的指令
3. Indexes：允许使用目录控制类型的指令
4. Limit：允许使用权限控制指令，Allow Deny和Order
5. Options：允许使用控制目录特征的指令，包括Options和XBitHack

对于Options：
1. All：允许以下除了MultiViews之外的所有功能
2. MultiViews：允许多重内容被浏览
3. Indexes：如果没有index文件，那么就显示目录
4. IncludesNOEXEC：允许SSI，但是不可以使用EXEC和INCLUDE指令
5. Includes：允许SSI
6. FollowSymLinks：跟踪符号链接
7. SymLinksIfOwnerMatch：仅仅跟踪本文件内的符号链接
8. ExecCGI：允许使用CGI