---
title: 管理資源和配額
titleSuffix: Azure Purview
description: 深入瞭解 Azure 範疇資源的配額和限制，以及如何要求增加配額。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938829"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>使用 Azure 範疇管理和提高資源配額
 
Azure 範疇是一項雲端服務，可供資料使用者使用。 您可以使用 Azure 範疇來集中管理跨雲端和內部內部部署環境之資料資產的資料管理。 服務可讓商務分析師使用有意義的商務詞彙來搜尋相關的資料。 若要提高訂用帳戶的上限，請連絡支援人員。
 
## <a name="azure-purview-limits"></a>Azure Purview 限制
 
|**Resource**|  **預設限制**  |**上限**|
|---|---|---|
|每個區域的範疇帳戶，每個租使用者 (所有訂用帳戶組合) |3|請連絡支援人員|
|虛擬核心可用於掃描，每個帳戶 *|160|160|
|並行掃描，每個帳戶在指定的時間點。 限制是以掃描的資料來源類型為基礎 *|5 | 10 |
|掃描可執行檔時間上限|7 天|7 天|
|API 呼叫（每個帳戶）|適用于4個容量單位平臺大小的每月1千萬個 Api。 <br>適用于16個容量單位平臺大小的 40M Api/月 |適用于4個容量單位平臺大小的每月1千萬個 Api。 <br>適用于16個容量單位平臺大小的 40M Api/月|
|儲存體，每個帳戶|4個容量單位的平臺大小為 10 GB。 <br>適用于16個容量單位的 40 GB 平臺大小 |4個容量單位的平臺大小為 10 GB。 <br> 適用于16個容量單位的 40 GB 平臺大小 |
|每個帳戶的資產大小|100M 實體資產 |請連絡支援人員|
|目錄中資產的大小上限|2 MB|2 MB|
|資產名稱和分類名稱的最大長度|4 KB|4 KB|
|資產屬性名稱和值的最大長度|32 KB|32 KB|
|分類屬性名稱和值的最大長度|32 KB|32 KB|
|每個帳戶的詞彙數目上限|100K|100K|
 
* 自我裝載的整合執行時間案例超出上表中所定義之限制的範圍。 
 
## <a name="next-steps"></a>後續步驟
 
> [!div class="nextstepaction"]
>[教學課程：使用 Azure 範疇掃描資料](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[教學課程：導覽首頁並搜尋資產](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[教學課程：流覽資產並查看其歷程](tutorial-browse-and-view-lineage.md)
