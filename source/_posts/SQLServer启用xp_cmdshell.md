title: SQLServer启用xp_cmdshell
tags:
  - windows
  - 收藏
  - 编程
id: 1220
categories:
  - 文章收藏
date: 2012-07-11 14:20:21
---

忘了当时是执行哪条命令的时候需要启动xp_cmdshell了。。反正是必须用的。。

默认情况下,sql server安装完后,xp_cmdshell是禁用的(可能是安全考虑),如果要使用它,可按以下步骤
<pre class="lang:pgsql decode:true">-- 允许配置高级选项
EXEC sp_configure 'show advanced options', 1
GO
-- 重新配置
RECONFIGURE
GO
-- 启用xp_cmdshell
EXEC sp_configure 'xp_cmdshell', 1
GO
--重新配置
RECONFIGURE
GO

--执行想要的xp_cmdshell语句
Exec xp_cmdshell 'query user'
GO

--用完后,要记得将xp_cmdshell禁用(出于安全考虑)
-- 允许配置高级选项
EXEC sp_configure 'show advanced options', 1
GO
-- 重新配置
RECONFIGURE
GO
-- 禁用xp_cmdshell
EXEC sp_configure 'xp_cmdshell', 0
GO
--重新配置
RECONFIGURE
GO</pre>
&nbsp;