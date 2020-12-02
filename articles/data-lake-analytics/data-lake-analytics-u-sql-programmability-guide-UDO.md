---
title: 適用于 Azure Data Lake 的 U-SQL UDO 可程式性指南
description: 瞭解可讓您建立絕佳 .USQL 腳本的雙 SQL UDO 程式設計 Azure Data Lake Analytics。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512540"
---
# <a name="u-sql-user-defined-objects-overview"></a>U-SQL 使用者定義物件總覽


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL：使用者定義物件： UDO
U-SQL 可讓您定義自訂可程式性物件，我們將它稱為使用者定義物件，簡稱 UDO。

以下是 U-SQL 中的 UDO 清單：

* 使用者定義擷取器
    * 逐列擷取
    * 用來實作從自訂結構化檔案擷取資料的作業

* 使用者定義輸出器
    * 逐列輸出
    * 用來輸出自訂資料類型或用來自訂檔案格式

* 使用者定義處理器
    * 擷取一列並產生一列
    * 用來減少資料行數目，或以衍生自現存資料行集的值產生新的資料行

* 使用者定義套用器
    * 擷取一列並產生 0 到 n 列
    * 與 OUTER/CROSS APPLY 搭配使用

* 使用者定義結合器
    * 結合資料列集--使用者定義 JOIN

* 使用者定義歸納器
    * 擷取 n 列並產生一列
    * 用來減少資料列數目

U-SQL 指令碼中通常會明確地呼叫 UDO 以做為下列 U-SQL 陳述式的一部分︰

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDO 限制為 0.5 Gb 記憶體。  這個記憶體限制不適用於本機執行。

## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)