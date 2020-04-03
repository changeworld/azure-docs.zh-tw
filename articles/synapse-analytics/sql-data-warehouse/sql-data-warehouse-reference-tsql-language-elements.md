---
title: T-SQL 語言項目
description: 指向 Synapse SQL 池中支援的 T-SQL 語句文件的連結。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/13/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f27a0a351e4a446dc950ac13f850bd14b2b3c65a
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586403"
---
# <a name="t-sql-language-elements-supported-in-synapse-sql-pool"></a>Synapse SQL 池中支援的 T-SQL 語言元素

指向 Synapse SQL 池中支援的 T-SQL 語言元素的文件的連結。

## <a name="core-elements"></a>核心元素

* [語法慣例](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)
* [物件命名規則](https://msdn.microsoft.com/library/ms175874.aspx)
* [保留關鍵字](https://msdn.microsoft.com/library/ms189822.aspx)
* [排序規則](https://msdn.microsoft.com/library/ff848763.aspx)
* [評論](https://msdn.microsoft.com/library/ms181627.aspx)
* [常數](https://msdn.microsoft.com/library/ms179899.aspx)
* [資料類型](https://msdn.microsoft.com/library/ms187752.aspx)
* [執行 CREATE 陳述式之前，請先執行](https://msdn.microsoft.com/library/ms188332.aspx)
* [運算式](https://msdn.microsoft.com/library/ms190286.aspx)
* [KILL](https://msdn.microsoft.com/library/ms173730.aspx)
* [IDENTITY 屬性因應措施](https://msdn.microsoft.com/library/ms186775.aspx)
* [列印](https://msdn.microsoft.com/library/ms176047.aspx)
* [使用](https://msdn.microsoft.com/library/ms188366.aspx)

## <a name="batches-control-of-flow-and-variables"></a>批次、流程控制和變數

* [BEGIN...END](https://msdn.microsoft.com/library/ms190487.aspx)
* [打破](https://msdn.microsoft.com/library/ms181271.aspx)
* [宣佈@local_variable](https://msdn.microsoft.com/library/ms188927.aspx)
* [如果。。。還](https://msdn.microsoft.com/library/ms182717.aspx)
* [RAISERROR](https://msdn.microsoft.com/library/ms178592.aspx)
* [SET@local_variable](https://msdn.microsoft.com/library/ms189484.aspx)
* [扔](https://msdn.microsoft.com/library/ee677615.aspx)
* [嘗試。。。抓住](https://msdn.microsoft.com/library/ms175976.aspx)
* [而](https://msdn.microsoft.com/library/ms178642.aspx)

## <a name="operators"></a>運算子

* [+ (加)](https://msdn.microsoft.com/library/ms178565.aspx)
* [* (串串)](https://msdn.microsoft.com/library/ms177561.aspx)
* [- (負)](https://msdn.microsoft.com/library/ms189480.aspx)
* [- (減)](https://msdn.microsoft.com/library/ms189518.aspx)
* [* (乘)](https://msdn.microsoft.com/library/ms176019.aspx)
* [/ (除)](https://msdn.microsoft.com/library/ms175009.aspx)
* [模](https://msdn.microsoft.com/library/ms190279.aspx)

## <a name="wildcard-characters-to-match"></a>相配的萬用字元

* [= (等於)](https://msdn.microsoft.com/library/ms175118.aspx)
* [> (大於)](https://msdn.microsoft.com/library/ms178590.aspx)
* [< (小於)](https://msdn.microsoft.com/library/ms179873.aspx)
* [>= (大於或等於)](https://msdn.microsoft.com/library/ms181567.aspx)
* [<= (小於或等於)](https://msdn.microsoft.com/library/ms174978.aspx)
* [<> (不等於)](https://msdn.microsoft.com/library/ms176020.aspx)
* [!* (不等於)](https://msdn.microsoft.com/library/ms190296.aspx)
* [和](https://msdn.microsoft.com/library/ms188372.aspx)
* [之間](https://msdn.microsoft.com/library/ms187922.aspx)
* [存在](https://msdn.microsoft.com/library/ms188336.aspx)
* [在](https://msdn.microsoft.com/library/ms177682.aspx)
* [為 [非] NULL](https://msdn.microsoft.com/library/ms188795.aspx)
* [LIKE](https://msdn.microsoft.com/library/ms179859.aspx)
* [不](https://msdn.microsoft.com/library/ms189455.aspx)
* [OR](https://msdn.microsoft.com/library/ms188361.aspx)

### <a name="bitwise-operators"></a>位元運算子

* [& (位元 AND)](https://msdn.microsoft.com/library/ms174965.aspx)
* [|( 從位或)](https://msdn.microsoft.com/library/ms186714.aspx)
* [^ (位元互斥 OR)](https://msdn.microsoft.com/library/ms190277.aspx)
* [~ (位元 NOT)](https://msdn.microsoft.com/library/ms173468.aspx)
* [^= (位元互斥 OR EQUALS)](https://msdn.microsoft.com/library/cc627413.aspx)
* [|= (位元 OR EQUALS)](https://msdn.microsoft.com/library/cc627409.aspx)
* [&= (位元 AND EQUALS)](https://msdn.microsoft.com/library/cc627427.aspx)

## <a name="functions"></a>函式

* [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
* [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
* [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
* [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
* [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
* [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
* [Abs](https://msdn.microsoft.com/library/ms189800.aspx)
* [ACOS](https://msdn.microsoft.com/library/ms178627.aspx)
* [Ascii](https://msdn.microsoft.com/library/ms177545.aspx)
* [阿辛](https://msdn.microsoft.com/library/ms181581.aspx)
* [阿坦](https://msdn.microsoft.com/library/ms181746.aspx)
* [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
* [BINARY_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
* [情況 下](https://msdn.microsoft.com/library/ms181765.aspx)
* [CAST 和 CONVERT](https://msdn.microsoft.com/library/ms187928.aspx)
* [CEILING](https://msdn.microsoft.com/library/ms189818.aspx)
* [字元](https://msdn.microsoft.com/library/ms187323.aspx)
* [CHARINDEX](https://msdn.microsoft.com/library/ms186323.aspx)
* [校驗](https://msdn.microsoft.com/library/ms189788.aspx)
* [合併](https://msdn.microsoft.com/library/ms190349.aspx)
* [COL_NAME](https://msdn.microsoft.com/library/ms174974.aspx)
* [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
* [Concat](https://msdn.microsoft.com/library/hh231515.aspx)
* [COS](https://msdn.microsoft.com/library/ms188919.aspx)
* [嬰兒 床](https://msdn.microsoft.com/library/ms188921.aspx)
* [計數](https://msdn.microsoft.com/library/ms175997.aspx)
* [COUNT_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
* [CUME_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
* [CURRENT_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
* [CURRENT_USER](https://msdn.microsoft.com/library/ms176050.aspx)
* [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
* [DATALENGTH](https://msdn.microsoft.com/library/ms173486.aspx)
* [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
* [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
* [從零件日期](https://msdn.microsoft.com/library/hh213228.aspx)
* [DATENAME](https://msdn.microsoft.com/library/ms174395.aspx)
* [日期部分](https://msdn.microsoft.com/library/ms174420.aspx)
* [DATETIME2FROMPARTS](https://msdn.microsoft.com/library/hh213312.aspx)
* [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
* [DATETIMEOFFSETFROMPARTS](https://msdn.microsoft.com/library/hh231077.aspx)
* [DAY](https://msdn.microsoft.com/library/ms176052.aspx)
* [DB_ID](https://msdn.microsoft.com/library/ms186274.aspx)
* [DB_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
* [度](https://msdn.microsoft.com/library/ms178566.aspx)
* [DENSE_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
* [DIFFERENCE](https://msdn.microsoft.com/library/ms188753.aspx)
* [EOMONTH](https://msdn.microsoft.com/library/hh213020.aspx)
* [ERROR_MESSAGE](https://msdn.microsoft.com/library/ms190358.aspx)
* [ERROR_NUMBER](https://msdn.microsoft.com/library/ms175069.aspx)
* [ERROR_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
* [ERROR_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
* [ERROR_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
* [EXP](https://msdn.microsoft.com/library/ms179857.aspx)
* [FIRST_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
* [FLOOR](https://msdn.microsoft.com/library/ms178531.aspx)
* [取得日期](https://msdn.microsoft.com/library/ms188383.aspx)
* [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
* [HAS_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
* [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
* [INDEXPROPERTY](https://msdn.microsoft.com/library/ms187729.aspx)
* [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
* [ISNULL](https://msdn.microsoft.com/library/ms184325.aspx)
* [ISNUMERIC](https://msdn.microsoft.com/library/ms186272.aspx)
* [滯後](https://msdn.microsoft.com/library/hh231256.aspx)
* [LAST_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
* [導致](https://msdn.microsoft.com/library/hh213125.aspx)
* [離開](https://msdn.microsoft.com/library/ms177601.aspx)
* [萊恩](https://msdn.microsoft.com/library/ms190329.aspx)
* [記錄](https://msdn.microsoft.com/library/ms190319.aspx)
* [紀錄10](https://msdn.microsoft.com/library/ms175121.aspx)
* [降低](https://msdn.microsoft.com/library/ms174400.aspx)
* [LTRIM](https://msdn.microsoft.com/library/ms177827.aspx)
* [麥克斯](https://msdn.microsoft.com/library/ms187751.aspx)
* [最小](https://msdn.microsoft.com/library/ms179916.aspx)
* [月](https://msdn.microsoft.com/library/ms187813.aspx)
* [NCHAR](https://msdn.microsoft.com/library/ms182673.aspx)
* [內蒂爾](https://msdn.microsoft.com/library/ms175126.aspx)
* [NULLIF](https://msdn.microsoft.com/library/ms177562.aspx)
* [OBJECT_ID](https://msdn.microsoft.com/library/ms190328.aspx)
* [OBJECT_NAME](https://msdn.microsoft.com/library/ms186301.aspx)
* [OBJECTPROPERTY](https://msdn.microsoft.com/library/ms176105.aspx)
* [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
* [ODBCS 純量函數](https://msdn.microsoft.com/library/bb630290.aspx)
* [OVER 子句](https://msdn.microsoft.com/library/ms189461.aspx)
* [PARSENAME](https://msdn.microsoft.com/library/ms188006.aspx)
* [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
* [PERCENTILE_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
* [PERCENTILE_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
* [PERCENT_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
* [PI](https://msdn.microsoft.com/library/ms189512.aspx)
* [權力](https://msdn.microsoft.com/library/ms174276.aspx)
* [QUOTENAME](https://msdn.microsoft.com/library/ms176114.aspx)
* [RADIANS](https://msdn.microsoft.com/library/ms189742.aspx)
* [RAND](https://msdn.microsoft.com/library/ms177610.aspx)
* [排名](https://msdn.microsoft.com/library/ms176102.aspx)
* [取代](https://msdn.microsoft.com/library/ms186862.aspx)
* [REPLICATE](https://msdn.microsoft.com/library/ms174383.aspx)
* [反向](https://msdn.microsoft.com/library/ms180040.aspx)
* [對](https://msdn.microsoft.com/library/ms177532.aspx)
* [輪](https://msdn.microsoft.com/library/ms175003.aspx)
* [ROW_NUMBER](https://msdn.microsoft.com/library/ms186734.aspx)
* [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
* [SCHEMA_ID](https://msdn.microsoft.com/library/ms188797.aspx)
* [SCHEMA_NAME](https://msdn.microsoft.com/library/ms175068.aspx)
* [SERVERPROPERTY](https://msdn.microsoft.com/library/ms174396.aspx)
* [SESSION_USER](https://msdn.microsoft.com/library/ms177587.aspx)
* [標誌](https://msdn.microsoft.com/library/ms188420.aspx)
* [罪](https://msdn.microsoft.com/library/ms188377.aspx)
* [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
* [SOUNDEX](https://msdn.microsoft.com/library/ms187384.aspx)
* [空間](https://msdn.microsoft.com/library/ms187950.aspx)
* [SQL_VARIANT_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
* [SQRT](https://msdn.microsoft.com/library/ms176108.aspx)
* [廣場](https://msdn.microsoft.com/library/ms173569.aspx)
* [STATS_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
* [STDEV](https://msdn.microsoft.com/library/ms190474.aspx)
* [STDEVP](https://msdn.microsoft.com/library/ms176080.aspx)
* [Str](https://msdn.microsoft.com/library/ms189527.aspx)
* [STUFF](https://msdn.microsoft.com/library/ms188043.aspx)
* [子](https://msdn.microsoft.com/library/ms187748.aspx)
* [和](https://msdn.microsoft.com/library/ms187810.aspx)
* [SUSER_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
* [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
* [SYSDATETIME](https://msdn.microsoft.com/library/bb630353.aspx)
* [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
* [SYSTEM_USER](https://msdn.microsoft.com/library/ms179930.aspx)
* [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
* [潭](https://msdn.microsoft.com/library/ms190338.aspx)
* [TERTIARY_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
* [TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
* [TODATETIMEOFFSET](https://msdn.microsoft.com/library/bb630335.aspx)
* [TYPE_ID](https://msdn.microsoft.com/library/ms181628.aspx)
* [TYPE_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
* [TYPEPROPERTY](https://msdn.microsoft.com/library/ms188419.aspx)
* [Unicode](https://msdn.microsoft.com/library/ms180059.aspx)
* [UPPER](https://msdn.microsoft.com/library/ms180055.aspx)
* [使用者](https://msdn.microsoft.com/library/ms186738.aspx)
* [USER_NAME](https://msdn.microsoft.com/library/ms188014.aspx)
* [無 功](https://msdn.microsoft.com/library/ms186290.aspx)
* [VARP](https://msdn.microsoft.com/library/ms188735.aspx)
* [年](https://msdn.microsoft.com/library/ms186313.aspx)
* [XACT_STATE ](https://msdn.microsoft.com/library/ms189797.aspx)

## <a name="transactions"></a>交易

* [交易](https://msdn.microsoft.com/library/mt204031.aspx)

## <a name="diagnostic-sessions"></a>診斷工作階段

* [CREATE DIAGNOSTICS SESSION](https://msdn.microsoft.com/library/mt204029.aspx)

## <a name="procedures"></a>程序

* [sp_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
* [sp_columns](https://msdn.microsoft.com/library/ms176077.aspx)
* [sp_configure](https://msdn.microsoft.com/library/ms188787.aspx)
* [sp_datatype_info_90](https://msdn.microsoft.com/library/mt204014.aspx)
* [sp_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
* [sp_execute](https://msdn.microsoft.com/library/ff848746.aspx)
* [sp_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
* [sp_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
* [sp_pdw_add_network_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
* [sp_pdw_database_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
* [sp_pdw_database_encryption_regenerate_system_keys](https://msdn.microsoft.com/library/mt204033.aspx)
* [sp_pdw_log_user_data_masking](https://msdn.microsoft.com/library/mt204023.aspx)
* [sp_pdw_remove_network_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
* [sp_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
* [sp_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
* [sp_spaceused](https://msdn.microsoft.com/library/ms188776.aspx)
* [sp_special_columns_100](https://msdn.microsoft.com/library/mt204025.aspx)
* [sp_sproc_columns](https://msdn.microsoft.com/library/ms182705.aspx)
* [sp_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
* [sp_tables](https://msdn.microsoft.com/library/ms186250.aspx)
* [sp_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)

## <a name="set-statements"></a>SET 陳述式

* [SET ANSI_DEFAULTS](https://msdn.microsoft.com/library/ms188340.aspx)
* [SET ANSI_NULL_DFLT_OFF](https://msdn.microsoft.com/library/ms187356.aspx)
* [SET ANSI_NULL_DFLT_ON](https://msdn.microsoft.com/library/ms187375.aspx)
* [SET ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx)
* [SET ANSI_PADDING](https://msdn.microsoft.com/library/ms187403.aspx)
* [SET ANSI_WARNINGS](https://msdn.microsoft.com/library/ms190368.aspx)
* [SET ARITHABORT](https://msdn.microsoft.com/library/ms190306.aspx)
* [SET ARITHIGNORE](https://msdn.microsoft.com/library/ms184341.aspx)
* [SET CONCAT_NULL_YIELDS_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
* [SET DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx)
* [SET DATEFORMAT](https://msdn.microsoft.com/library/ms189491.aspx)
* [SET FMTONLY](https://msdn.microsoft.com/library/ms173839.aspx)
* [SET IMPLICIT_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
* [SET LOCK_TIMEOUT](https://msdn.microsoft.com/library/ms189470.aspx)
* [SET NUMBERIC_ROUNDABORT](https://msdn.microsoft.com/library/ms188791.aspx)
* [SET QUOTED_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
* [SET ROWCOUNT](https://msdn.microsoft.com/library/ms188774.aspx)
* [SET TEXTSIZE](https://msdn.microsoft.com/library/ms186238.aspx)
* [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx)
* [SET XACT_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)

## <a name="next-steps"></a>後續步驟

有關詳細資訊,請參閱[Synapse SQL 池中的 T-SQL 語句](sql-data-warehouse-reference-tsql-statements.md),以及[Synapse SQL 池中的系統檢視](sql-data-warehouse-reference-tsql-system-views.md)。

