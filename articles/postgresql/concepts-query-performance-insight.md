---
title: 查詢性能洞察 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 本文介紹了 Azure 資料庫中用於 PostgreSQL - 單伺服器的查詢性能洞察功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768379"
---
# <a name="query-performance-insight"></a>查詢效能深入解析 

**適用于：** 用於 PostgreSQL 的 Azure 資料庫 - 單伺服器版本 9.6、10、11

查詢效能深入解析可協助您快速找出執行時間最長的查詢、一段時間後的變化情形，以及受到哪些等候的影響。

## <a name="permissions"></a>權限
需要**擁有者**或**參與者**權限，才能檢視查詢效能深入解析中的查詢文字。 **讀者**可以檢視圖表與資料表，但無法檢視查詢文字。

## <a name="prerequisites"></a>Prerequisites
資料必須存在於[查詢存放區](concepts-query-store.md)中，查詢效能深入解析才能運作。

## <a name="viewing-performance-insights"></a>檢視效能深入解析
Azure 入口網站中的[查詢效能深入解析](concepts-query-performance-insight.md)檢視會以視覺效果呈現來自查詢存放區的重要資訊。 

在 PostgreSQL 伺服器 Azure 資料庫的門戶頁中，在功能表列的**智慧性能**部分下選擇 **"查詢性能見解**"。

![查詢效能深入解析長時間執行的查詢](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

**"長時間執行查詢"** 選項卡按每次執行的平均持續時間顯示前五個查詢，以 15 分鐘間隔聚合。 您可以從 [查詢數目]**** 下拉式清單中選取，以檢視更多查詢。 當您這樣做時，特定查詢識別碼的圖表色彩可能會有所變更。

您可以在圖表中按一下並拖曳來縮小到特定時間範圍。 或者，使用放大和縮小圖示，分別檢視一段較短或較長的時間。

圖表下方的資料表會提供該時間範圍內長時間執行之查詢的詳細資訊。

選取 [等候統計資料]**** 索引標籤，以檢視伺服器中等候的對應視覺效果。

![查詢性能洞察等待統計資訊](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>考量
* 查詢性能見解不適用於[讀取副本](concepts-read-replicas.md)。

## <a name="next-steps"></a>後續步驟
- 深入了解在適用於 PostgreSQL 的 Azure 資料庫中進行[監視和微調](concepts-monitoring.md)。


