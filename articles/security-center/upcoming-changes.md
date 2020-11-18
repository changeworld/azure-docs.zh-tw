---
title: 未來 Azure 資訊安全中心的重要變更
description: 您需要留意並提早規劃的各項 Azure 資訊安全中心變更
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380157"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>未來 Azure 資訊安全中心的重要變更

> [!IMPORTANT]
> 本頁中載明的資訊與預先發行產品或功能相關，且內容可能在公開上市之前修改。 Microsoft 對於此處提供的資訊不做任何明示或暗示的承諾或保證。

本頁內容可帶您了解為資訊安全中心規劃的各項變更， 並說明規劃的產品修改，且可能影響您的安全分數或工作流程等項目。

如果要尋找 6 個月之前的版本資訊，請前往[Azure 資訊安全中心的新功能](release-notes.md)。


## <a name="planned-changes"></a>規劃的變更

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>「您應在機器上安裝系統更新」建議有子建議

#### <a name="summary"></a>摘要

| 層面 | 詳細資料 |
|---------|---------|
|公告日期 | 2020 年 11 月 9 日  |
|此變更的日期  |  2020 年 11 月中下旬 |
|影響     | 從這項建議的目前版本轉換為取代建議期間，您的安全分數可能會改變。 |
|  |  |

我們即將發行 **您應在機器上安裝系統更新** 建議的增強版。 新版本將 *取代* 套用系統更新安全性控制中的目前版本，並提供下列改進：

- 每個遺漏的更新已有子建議
- Azure 入口網站的 Azure 資訊安全中心頁面會提供經過重新設計的體驗
- Azure Resource Graph 的建議有更豐富的資料

#### <a name="transition-period"></a>轉換期間

轉換期間大約為 36 小時。 為了盡可能避免任何潛在的中斷情況，我們排定在週末進行更新。 在轉換期間，您的安全分數可能會受到影響。

#### <a name="redesigned-portal-experience"></a>重新設計的入口網站體驗

**您應在機器上安裝系統更新** 的建議詳細資料包含結果清單，如下所示。 當您選取單一結果時，詳細資料窗格隨即開啟，其中含有補救資訊的連結和受影響的資源清單。

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="在入口網站體驗中開啟其中一個子建議，以取得更新的建議":::


#### <a name="richer-data-from-azure-resource-graph"></a>Azure Resource Graph 有更豐富的資料

Azure Resource Graph 是 Azure 中的一項服務，旨在提供有效率的資源探索。 您可以使用 ARG 在一組指定的訂用帳戶中大規模查詢，以便有效率地控管您的環境。 

對於 Azure 資訊安全中心，您可以使用 ARG 和 [Kusto 查詢語言 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) 來查詢各種安全性狀態資料。

如果您查詢 **您應在機器上安裝系統更新** 的目前版本，ARG 唯一會提供的資訊將是必須在機器上補強建議。 在發行更新的版本時，下列查詢將會傳回每個遺漏的系統更新 (依機器分組)。

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>後續步驟

如需產品的所有最近變更，請參閱 [Azure 資訊安全中心有哪些新功能？](release-notes.md)。