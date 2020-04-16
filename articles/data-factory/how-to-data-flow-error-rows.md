---
title: 使用 Azure 資料工廠中映射資料流程處理錯誤列
description: 瞭解如何使用對應資料流處理 Azure 資料工廠中的 SQL 截斷錯誤。
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 4f65421a6457d4bf4d438ce9d035d46476829da2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414363"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>處理資料工廠映射資料串流中的 SQL 截斷錯誤列

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在資料工廠中使用映射數據流時的常見方案是將數據轉換後的數據寫入 Azure SQL 資料庫。 在這種情況下,必須防止的常見錯誤條件是可能的列截斷。 按照以下步驟提供不適合目標字串列的列的日誌記錄,從而允許數據流在這些方案中繼續。

## <a name="scenario"></a>狀況

1. 我們有一個目標 Azure SQL```nvarchar(5)```資料庫表, 該表具有一個名為"name"的列。

2. 在數據流中,我們希望將影片標題從接收器映射到目標"名稱"列。

    ![影片資料流程 1](media/data-flow/error4.png)
    
3. 問題是,影片標題不能全部適合只能容納 5 個字元的接收器列。 執行此資料串流時,您將收到以下的錯誤:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>如何圍繞此條件進行設計

1. 在這種情況下,"名稱"列的最大長度為五個字元。 因此,讓我們添加一個條件拆分轉換,允許我們記錄具有"標題"超過五個字元的行,同時允許可以容納該空間的其他行寫入資料庫。

    ![條件式分割](media/data-flow/error1.png)

2. 此條件拆分轉換將"標題"的最大長度定義為 5。 任何小於或等於五行都將進入```GoodRows```流。 任何大於 5 行都```BadRows```將進入 流中。

3. 現在,我們需要記錄失敗的行。 向```BadRows```流添加接收器轉換以進行日誌記錄。 在這裏,我們將"自動映射"所有欄位,以便記錄完整的事務記錄。 這是文本分隔 CSV 檔輸出到 Blob 儲存中的單個檔。 我們將將日誌檔稱為"壞行.csv"。

    ![壞行](media/data-flow/error3.png)
    
4. 已完成的數據流如下所示。 現在,我們能夠拆分錯誤行以避免 SQL 截斷錯誤,並將這些條目放入日誌檔中。 同時,成功的行可以繼續寫入我們的目標資料庫。

    ![完整的資料流程](media/data-flow/error2.png)

## <a name="next-steps"></a>後續步驟

* 通過使用映射數據流[轉換](concepts-data-flow-overview.md)構建數據流邏輯的其餘部分。
