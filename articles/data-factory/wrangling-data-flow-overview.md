---
title: Azure Data Factory 中的整頓資料流程
description: Azure Data Factory 中的整頓資料流程總覽
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5d0393ab5f2582efa2a4b727543bc799cdb1143e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082846"
---
# <a name="what-are-wrangling-data-flows"></a>什麼是整頓的資料流程？

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


組織需要進行資料準備和整頓，以精確分析每日持續成長的複雜資料。 需要進行資料準備，讓組織可以使用各種商務程式中的資料，並縮短價值的時間。

Azure Data Factory 中的整頓資料流程可讓您以雲端規模反復執行無程式碼資料的準備工作。 整頓資料流程會與[Power Query Online](https://docs.microsoft.com/power-query/)整合，讓 data factory 使用者可以使用 Power Query M 功能。

整頓資料流程會將 Power Query 線上混合編輯器產生的 M 轉譯為 spark 程式碼，以進行雲端規模的執行。

整頓資料流程特別適用于資料工程師或「公民資料整合者」。

> [!NOTE]
> 整頓資料流程目前提供公開預覽

## <a name="use-cases"></a>使用案例

### <a name="fast-interactive-data-exploration-and-preparation"></a>快速的互動式資料探索和準備

多個資料工程師和公民資料整合者可以互動方式探索及準備雲端規模的資料集。 隨著資料 lake 中資料量、各種和速度的增加，使用者需要有效的方式來探索和準備資料集。 例如，您可能需要建立一個資料集，其中包含自2017起，新客戶的所有客戶人口統計資訊。 您未對應到已知的目標。 您正在探索、整頓和準備資料集，以符合在 lake 中發佈之前的需求。 整頓的資料流程通常用於較不正式的分析案例。 備妥資料集可用來進行轉換，並使用下游的機器學習作業。

### <a name="code-free-agile-data-preparation"></a>無程式碼的 agile 資料準備

公民資料整合者會花費超過60% 的時間來尋找和準備資料。 他們想要以程式碼的免費方式執行，以提升營運生產力。 讓公民資料整合者能夠以可擴充的方式，使用已知的 Power Query 工具來豐富、塑造及發佈資料，以大幅提升其生產力。 Azure Data Factory 中的整頓資料流程可讓您熟悉的 Power Query 線上混合式編輯器，讓公民資料整合者能夠快速修正錯誤、將資料標準化，以及產生高品質的資料，以支援商務決策。

### <a name="data-validation"></a>資料驗證

以無程式碼的方式流覽資料，以移除任何極端值、異常狀況，並將其符合用於快速分析的圖形。

## <a name="supported-sources"></a>支援的來源

| 連接子 | 資料格式 | 驗證類型 |
| -- | -- | --|
| [Azure Blob 儲存體](connector-azure-blob-storage.md) | CSV、Parquet | 帳戶金鑰 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | 服務主體 |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) \(部分機器翻譯\) | CSV、Parquet | 帳戶金鑰，服務主體 |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 驗證 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 驗證 |

## <a name="the-mashup-editor"></a>混搭編輯器

當您建立整頓資料流程時，所有源資料集都會變成資料集查詢，並放在**ADFResource**資料夾中。 根據預設，UserQuery 會指向第一個資料集查詢。 所有的轉換都應該在 UserQuery 上完成，因為資料集查詢的變更不受支援，也不會保存。 目前不支援重新命名、加入和刪除查詢。

![整頓](media/wrangling-data-flow/editor.png)

目前不支援所有 Power Query M 函數來進行資料整頓，但仍可在撰寫期間使用。 建立整頓的資料流程時，如果不支援函式，系統會提示您輸入下列錯誤訊息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

如需有關支援的轉換的詳細資訊，請參閱[整頓資料流程函數](wrangling-data-flow-functions.md)。

目前整頓的資料流程僅支援寫入至一個接收。

## <a name="next-steps"></a>後續步驟

瞭解如何[建立整頓資料流程](wrangling-data-flow-tutorial.md)。
