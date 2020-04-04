---
title: 資料倉儲排序規則類型
description: Azure 同步分析 SQL 池中支援的排序規則類型。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 202bbaf4ea53dd6ba285e79dfa9e6ce782c0903e
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633093"
---
# <a name="database-collation-support-for-azure-synapse-analytics-sql-pool"></a>Azure 同步分析 SQL 池的資料庫排序規則支援

創建新的 Azure Synapse SQL 池資料庫時,可以從 Azure 門戶更改預設資料庫排序規則。 此功能使得使用 3800 支援的資料庫排序規則之一創建新資料庫變得更加容易。

排序規則為基於字元的數據類型提供區域設置、代碼頁、排序順序和字元敏感度規則。 選擇後,需要排序規則資訊的所有列和運算式都將從資料庫設置繼承所選排序規則。 可以通過顯式聲明基於字元的數據類型的不同排序規則來覆蓋預設繼承。

## <a name="changing-collation"></a>變更排序規則

要更改預設排序規則,請更新到預配體驗中的「拼貼」欄位。

例如,如果要將預設排序規則更改為區分大小寫,只需將排序規則從SQL_Latin1_General_CP1_CI_AS重命名為SQL_Latin1_General_CP1_CS_AS。

## <a name="list-of-unsupported-collation-types"></a>不支援的排序規則類型清單

* Japanese_Bushu_Kakusu_140_BIN
* Japanese_Bushu_Kakusu_140_BIN2
* Japanese_Bushu_Kakusu_140_CI_AI_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI
* Japanese_Bushu_Kakusu_140_CI_AI_WS
* Japanese_Bushu_Kakusu_140_CI_AI_KS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CI_AS
* Japanese_Bushu_Kakusu_140_CI_AS_WS
* Japanese_Bushu_Kakusu_140_CI_AS_KS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AI
* Japanese_Bushu_Kakusu_140_CS_AI_WS
* Japanese_Bushu_Kakusu_140_CS_AI_KS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AS
* Japanese_Bushu_Kakusu_140_CS_AS_WS
* Japanese_Bushu_Kakusu_140_CS_AS_KS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
* Japanese_XJIS_140_BIN
* Japanese_XJIS_140_BIN2
* Japanese_XJIS_140_CI_AI_VSS
* Japanese_XJIS_140_CI_AI_WS_VSS
* Japanese_XJIS_140_CI_AI_KS_VSS
* Japanese_XJIS_140_CI_AI_KS_WS_VSS
* Japanese_XJIS_140_CI_AS_VSS
* Japanese_XJIS_140_CI_AS_WS_VSS
* Japanese_XJIS_140_CI_AS_KS_VSS
* Japanese_XJIS_140_CI_AS_KS_WS_VSS
* Japanese_XJIS_140_CS_AI_VSS
* Japanese_XJIS_140_CS_AI_WS_VSS
* Japanese_XJIS_140_CS_AI_KS_VSS
* Japanese_XJIS_140_CS_AI_KS_WS_VSS
* Japanese_XJIS_140_CS_AS_VSS
* Japanese_XJIS_140_CS_AS_WS_VSS
* Japanese_XJIS_140_CS_AS_KS_VSS
* Japanese_XJIS_140_CS_AS_KS_WS_VSS
* Japanese_XJIS_140_CI_AI
* Japanese_XJIS_140_CI_AI_WS
* Japanese_XJIS_140_CI_AI_KS
* Japanese_XJIS_140_CI_AI_KS_WS
* Japanese_XJIS_140_CI_AS
* Japanese_XJIS_140_CI_AS_WS
* Japanese_XJIS_140_CI_AS_KS
* Japanese_XJIS_140_CI_AS_KS_WS
* Japanese_XJIS_140_CS_AI
* Japanese_XJIS_140_CS_AI_WS
* Japanese_XJIS_140_CS_AI_KS
* Japanese_XJIS_140_CS_AI_KS_WS
* Japanese_XJIS_140_CS_AS
* Japanese_XJIS_140_CS_AS_WS
* Japanese_XJIS_140_CS_AS_KS
* Japanese_XJIS_140_CS_AS_KS_WS
* SQL_EBCDIC1141_CP1_CS_AS
* SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>檢查目前的排序規則

要檢查資料庫的目前排序規則,可以執行以下 T-SQL 代碼段:

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```

當傳遞「排序規則」作為屬性參數時,Database PropertyEx 函數將返回指定的資料庫的當前排序規則。 有關詳細資訊,請參閱[資料庫屬性Ex。](/sql/t-sql/functions/databasepropertyex-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
