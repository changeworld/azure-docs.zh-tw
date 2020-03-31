---
title: 在 Azure 資料工廠中旋轉資料流程
description: Azure 資料工廠中爭用資料流程的概述
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8f91ed926c733b211443805722b6817b1ce005b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048352"
---
# <a name="what-are-wrangling-data-flows"></a>什麼是在爭論資料流程？

組織需要進行資料準備和爭論，以便準確分析資料，這些資料每天都在增長。 資料準備是必需的，以便組織可以在各種業務流程中使用資料並縮短價值時間。

通過 Azure 資料工廠中旋轉資料流程，您可以反覆運算雲規模進行無代碼資料準備。 旋轉資料流程與[連線電源查詢](https://docs.microsoft.com/power-query/)集成，並使電源查詢 M 函數可供資料工廠使用者使用。

旋轉資料流程將電源查詢連線混搭編輯器生成的 M 轉換為用於雲縮放執行的火花代碼。

對資料工程師或"公民資料整合商"來說，爭鳴資料流程尤其有用。

## <a name="use-cases"></a>使用案例

### <a name="fast-interactive-data-exploration-and-preparation"></a>快速互動式資料探索和準備

多個資料工程師和公民資料整合商可以在雲規模上以對話模式探索和準備資料集。 隨著資料湖中資料量、多樣性和速度的增加，使用者需要一種有效的方法來探索和準備資料集。 例如，您可能需要創建一個資料集，該資料集"自 2017 年以來已為新客戶提供了所有客戶人口統計資訊"。 您沒有映射到已知目標。 在將資料集發佈到湖中之前，您正在流覽、爭用和準備資料集以滿足要求。 爭鳴資料流程通常用於不太正式的分析方案。 預置資料集可用於在下游執行轉換和機器學習操作。

### <a name="code-free-agile-data-preparation"></a>無代碼敏捷資料準備

公民資料整合商花費超過 60% 的時間來查找和準備資料。 他們希望以無代碼的方式做到這一點，以提高運營效率。 允許公民資料整合商使用已知工具（如 Power Query Online）以可擴展的方式豐富、塑造和發佈資料，從而大大提高了工作效率。 在 Azure 資料工廠中，Wrangrang 資料流程使熟悉的 Power 查詢連線混搭編輯器能夠允許公民資料整合商快速修復錯誤、標準化資料並生成高品質的資料以支援業務決策。

### <a name="data-validation"></a>資料驗證

以無代碼方式直觀地掃描資料，以刪除任何異常值、異常值，並將其與形狀保持一致，以便快速分析。

## <a name="supported-sources"></a>支援的來源

| 連接器 | 資料格式 | 驗證類型 |
| -- | -- | --|
| [Azure Blob 存儲](connector-azure-blob-storage.md) | CSV， 鑲木地板 | 帳戶金鑰 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | 服務主體 |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV， 鑲木地板 | 帳戶金鑰，服務主體 |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 驗證 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 驗證 |

## <a name="the-mashup-editor"></a>混搭編輯器

創建爭鳴的資料流程時，所有源資料集都將變為資料集查詢，並放置在**ADFResource**資料夾中。 預設情況下，UserQuery 將指向第一個資料集查詢。 所有轉換都應在 UserQuery 上完成，因為不支援對資料集查詢的更改，也不會持久化更改。 當前不支援重命名、添加和刪除查詢。

![爭吵](media/wrangling-data-flow/editor.png)

儘管在創作期間可用，但目前並非所有 Power Query M 函數都支援用於資料爭用。 構建爭用資料流程時，如果不支援函數，系統將提示您出現以下錯誤訊息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

有關支援的轉換的詳細資訊，請參閱[爭用資料流程函數](wrangling-data-flow-functions.md)。

當前，爭鳴的資料流程僅支援寫入一個接收器。

## <a name="next-steps"></a>後續步驟

瞭解如何[創建爭鳴的資料流程](wrangling-data-flow-tutorial.md)。