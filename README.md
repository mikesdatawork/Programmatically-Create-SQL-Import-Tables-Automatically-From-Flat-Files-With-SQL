![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        


# Programmatically Create SQL Import Tables Automatically From Flat Files With SQL
**Post Date: February 16, 2018**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process


![Create SQL Import Tables Automatically]( https://mikesdatawork.files.wordpress.com/2018/02/image0012.png "SQL Import Process")
 
<p>The following logic automatically creates import tables from flat files. The system is numeric, and tables are created with the name [IMPORT00], and will upcount 01, 02, 03 for each import that is carried out. Be sure to add the proper path to your flat file, and designate the proper value to get the correct columns.
It first looks to the first single row of values to build your columns. In this example my first row betings with "Issue #" which I used to identify the column names. Columns that have no value where an empty string was separated by commmas ",," is removed.
Here's the logic:</p>



## SQL-Logic
```SQL
use [compliance];
set nocount on
 
if object_id('tempdb..#import_file') is not null
    drop table  #import_file
create table    #import_file ([alldata] varchar(max))
 
bulk insert #import_file from 'C:\SQLIMPORTS\FY18Q1.csv' with (rowterminator = '\n', fieldterminator = ',')
 
declare @new_table varchar(255) = (select top 1 isnull([table_name], 'IMPORT00') from information_schema.tables where [table_name] like 'import%[0-9][0-9]' order by [table_name] desc)
select  @new_table = upper(left(@new_table, 6)) + format(cast(right(@new_table, 2) as int) + 1, '00')
 
declare @get_columns    varchar(max) = (select top 1 * from #import_file where [alldata] like 'issue #%')
declare @create_table   varchar(max) = (select  'create table [' + @new_table + '] ' +  char(10) + '    ' + '([' + replace(replace(@get_columns, ',,', ','), ',', ']            nvarchar(max)' + char(10) + ',    [') + '] nvarchar(max))')
exec    (@create_table)
 
declare @sql    varchar(255)    = ('select * from [' + @new_table + ']')
exec    (@sql)
```

One point. The logic creates the table; but does not automatically populate it. You'll need to your import process (statement) to get the values across.
Here is an example of the build process which is run automatically.

![Create Import Table With SQL]( https://mikesdatawork.files.wordpress.com/2018/02/629.jpg "Create Import Table With SQL")
 
Here I've added an additional import to populate the table after the table is created, then subsequently selecting the data from it. 



## SQL-Logic
```SQL
use [compliance];
set nocount on
 
if object_id('tempdb..#import_file') is not null
    drop table  #import_file
create table    #import_file ([alldata] varchar(max))
 
bulk insert #import_file from 'C:\SQLIMPORTS\FY18Q1.csv' with (rowterminator = '\n', fieldterminator = ',')
 
declare @new_table varchar(255) = (select top 1 isnull([table_name], 'IMPORT00') from information_schema.tables where [table_name] like 'import%[0-9][0-9]' order by [table_name] desc)
select  @new_table = upper(left(@new_table, 6)) + format(cast(right(@new_table, 2) as int) + 1, '00')
 
declare @get_columns    varchar(max) = (select top 1 * from #import_file where [alldata] like 'issue #%')
declare @create_table   varchar(max) = (select  'create table [' + @new_table + '] ' +  char(10) + '    ' + '([' + replace(replace(@get_columns, ',,', ','), ',', ']            nvarchar(max)' + char(10) + ',    [') + '] nvarchar(max))')
exec    (@create_table)
 
declare @import_process varchar(max)
set     @import_process = (select 'bulk insert [' + @new_table + '] from ''C:\SQLIMPORTS\FY18Q1.csv'' with (rowterminator = ''\n'', fieldterminator = '','')')
exec    (@import_process)
 
declare @sql    varchar(255)    = ('select * from [' + @new_table + ']')
exec    (@sql)
```


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

     
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

