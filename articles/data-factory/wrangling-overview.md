---
title: Azure Data Factory 中的資料整頓
description: Azure Data Factory 中的資料整頓總覽
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: aa829f8f3de145cf831719a39cfb66163d9cc4f9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633924"
---
# <a name="what-is-data-wrangling"></a>什麼是資料整頓？

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

組織必須能夠探索其重要的商務資料，以進行資料準備和整頓，以便為每天持續成長的複雜資料提供精確的分析。 需要資料準備，讓組織可以使用各種商務程式中的資料，並縮短價值的時間。

Data Factory 可讓您使用 Power Query 反復進行雲端規模的無程式碼資料準備。 Data Factory 與 [Power Query Online](/power-query/) 整合，並讓 Power Query M 函式可作為管線活動。

Data Factory 藉由將 M 轉譯成 Azure Data Factory 的資料流程，將 Power Query 線上混搭編輯器所產生的 M 轉譯成適用于雲端規模執行的 spark 程式碼。 使用 Power Query 和資料流程整頓資料對於資料工程師或「公民資料整合者」特別有用。

> [!NOTE]
> Azure Data Factory 中的 Power Query 活動目前提供公開預覽

## <a name="use-cases"></a>使用案例

### <a name="fast-interactive-data-exploration-and-preparation"></a>快速的互動式資料探索和準備

多個資料工程師和公民資料整合者可以透過雲端規模以互動方式探索及準備資料集。 隨著資料量、資料 lake 中資料量、多樣性和速度的增加，使用者需要有效的方式來探索及準備資料集。 例如，您可能需要建立資料集，該資料集為新客戶自2017起的所有客戶人口統計資訊。 您未對應到已知的目標。 您正在探索、整頓和準備資料集以符合需求，然後才在 lake 中發佈它。 整頓通常用於較不正式的分析案例。 備妥資料集可以用來進行下游的轉換和機器學習作業。

### <a name="code-free-agile-data-preparation"></a>無程式碼的敏捷式資料準備

公民資料整合者花費超過60% 的時間來尋找和準備資料。 他們想要以無程式碼的方式來達成目的，以提升營運生產力。 Power Query 利用可調整的方式，讓公民資料整合者能夠以可調整的方式擴充、塑造及發佈資料，大幅改善其生產力。 Azure Data Factory 中的整頓可讓您熟悉的 Power Query 線上混搭編輯器，讓公民資料整合者能夠快速修正錯誤、將資料標準化，以及產生高品質的資料以支援商務決策。

### <a name="data-validation-and-exploration"></a>資料驗證和探索

以無程式碼的方式，以無程式碼的方式來掃描您的資料，以移除任何極端值、異常，並將其符合可快速分析的圖形。

## <a name="supported-sources"></a>支援的來源

| 連接子 | 資料格式 | 驗證類型 |
| -- | -- | --|
| [Azure Blob 儲存體](connector-azure-blob-storage.md) | CSV、Parquet | 帳戶金鑰 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | 服務主體 |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) \(部分機器翻譯\) | CSV、Parquet | 帳戶金鑰、服務主體 |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 驗證 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 驗證 |

## <a name="the-mashup-editor"></a>混搭編輯器

當您建立 Power Query 活動時，所有源資料集都會變成資料集查詢，並放在 **ADFResource** 資料夾中。 根據預設，UserQuery 會指向第一個資料集查詢。 所有轉換都應該在 UserQuery 上完成，因為資料集查詢的變更不受支援，也不會保存它們。 目前不支援重新命名、新增和刪除查詢。

![爭吵](media/wrangling-data-flow/editor.png)

目前並不支援所有 Power Query M 函式進行資料整頓（儘管在撰寫期間可供使用）。 當您建立 Power Query 活動時，如果函式不受支援，系統會提示您輸入下列錯誤訊息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

如需有關支援之轉換的詳細資訊，請參閱 [資料整頓函數](wrangling-functions.md)。

## <a name="next-steps"></a>下一步

瞭解如何 [建立 Power Query 混合的資料整頓](wrangling-tutorial.md)。
