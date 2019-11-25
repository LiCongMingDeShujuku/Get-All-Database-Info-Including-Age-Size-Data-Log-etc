![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 获取所有数据库信息，包括年龄大小数据日志等。
#### Get All Database Info Including Age Size Data Log etc.
**发布-日期: 2017年06月23日 (评论)**

![#](images/get-all-database-info-including-size-data-log-etc-a.png?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
这里有一些SQL逻辑可以为你提供所有数据库信息，例如服务器名称，数据库名称，大小，年龄，恢复模型，包括数据和日志文件信息。这并不难写，但是当你需要快速获取信息时，在你的首选脚本中使用这些内容总是不会错的。


## English
Here’ s some SQL logic to give you all database info such as Server Name, Database Name, Size, Age, Recovery Model, including data and log file information. This isn’t that difficult to write, but it’s always nice to have these in your go-to scripts whenever you need information quickly.

---
## Logic
```SQL
use master;
 set nocount on
 if object_id('tempdb..#database_info') is not null
 drop table #database_info
 create table #database_info
 (
 [database_name] varchar(255)
 , [database_size] float
 , [remarks] varchar(255)
 )
 
insert #database_info exec ('exec master..sp_databases');
 select
 'server_name' = upper(@@servername)
 , 'database' = upper(database_name)
 , 'created_on' = left(sd.create_date, 19)
 , 'database_age' =
 cast(datediff(second, sd.create_date, getdate()) / 60 / 60 / 24 / 30 / 12 as nvarchar(50)) + ' years, '
 + cast(datediff(second, sd.create_date, getdate()) / 60 / 60 / 24 / 30 % 12 as nvarchar(50)) + ' months, '
 + cast(datediff(second, sd.create_date, getdate()) / 60 / 60 / 24 % 30 as nvarchar(50)) + ' days, '
 + cast(datediff(second, sd.create_date, getdate()) / 60 / 60 % 24 as nvarchar(50)) + ' hours, '
 + cast(datediff(second, sd.create_date, getdate()) / 60 % 60 as nvarchar(50)) + ' minutes '
 + cast(datediff(second, sd.create_date, getdate()) % 60 as nvarchar(50)) + ' seconds '
 , 'status' = sd.state_desc
 , 'in_gb' = round((database_size / 1024) / 1024, 2)
 , 'in_mb' = round(database_size / 1024, 2)
 , 'in_kb' = database_size
 , 'recovery_model' = sd.recovery_model_desc
 , 'data_logical_name' = smf_data.name
 , 'data_path' = smf_data.physical_name
 , 'log_logical_name' = smf_log.name
 , 'log_path' = smf_log.physical_name
 , 'compatability_level' = case sd.compatibility_level
 when 60 then '60 = SQL 6.0 (Works on 6)'
 when 65 then '65 = SQL 6.5 (Works on 6.5)'
 when 70 then '70 = SQL 7.0 (Works on 7)'
 when 80 then '80 = SQL 2000 (Works on 2000)'
 when 90 then '90 = SQL 2005 (Works on 2005, 2000)'
 when 100 then '100 = SQL 2008 (Works on 2008R2, 2005)'
 when 110 then '110 = SQL 2012 (Works on 2012, 2008R2, 2005)'
 when 120 then '120 = SQL 2014 (Works on 2014, 2012, 2008R2)'
 when 130 then '130 = SQL 2016 (Works on 2016, 2014, 2012, 2008R2)'
 when 140 then '140 = SQL 2017 (Works on 2017, 2016, 2014, 2012, 2008R2)'
 end
 
from
 #database_info dbi
 inner join sys.databases sd on dbi.database_name = sd.name
 inner join (select smf.database_id, smf.name, smf.physical_name from sys.master_files smf where smf.file_id = 1) smf_data on smf_data.database_id = sd.database_id
 inner join (select smf.database_id, smf.name, smf.physical_name from sys.master_files smf where smf.file_id = 2) smf_log on smf_log.database_id = sd.database_id
 where
 sd.database_id > 4
 order by
 database_size desc;

```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

