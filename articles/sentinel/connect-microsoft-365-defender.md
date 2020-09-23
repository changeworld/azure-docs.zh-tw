---
title: 將 Microsoft 365 Defender 原始資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將原始事件資料從 Microsoft 365 Defender 內嵌到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: e9e892ae08c65ee5c5008bbdc1b419d8dec30183
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934101"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>將資料從 Microsoft 365 Defender 連接至 Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 先前稱為 **Microsoft 威脅防護** 或 **MTP**。
>
> **Microsoft Defender For Endpoint** 先前稱為 **Microsoft Defender 進階威脅防護** 或 **MDATP**。
>
> 您可能會看到舊名稱仍在使用中一段時間。

## <a name="background"></a>背景

新的 [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-threat-protection) 連接器可讓您將 **advanced 搜尋** 記錄（一種原始事件資料類型）串流至 Azure Sentinel 的 Microsoft 365 Defender。 

藉由將 [Microsoft defender For endpoint (MDATP) ](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 至 Microsoft 365 defender 安全層，您現在可以使用 Microsoft 365 defender 連接器收集您的 microsoft Defender for endpoint [advanced 搜尋](https://aka.ms/mdatpAH) 事件，並將它們直接串流至您 Azure Sentinel 工作區中的新用途內建資料表。 這些資料表是以 Microsoft 365 Defender 入口網站中使用的相同架構為基礎，可讓您完整存取一組完整的 advanced 搜尋記錄，並允許您執行下列作業：

- 輕鬆地將您現有的 Microsoft Defender ATP advanced 搜尋查詢複製到 Azure Sentinel。

- 您可以使用原始事件記錄檔來提供警示、搜尋和調查的其他見解，以及將事件與 Azure Sentinel 中其他資料來源的資料相互關聯。

- 儲存保留期增加的記錄，超過 Microsoft Defender for Endpoint 或 Microsoft 365 Defender 的預設保留期30天。 您可以藉由設定工作區的保留期，或在 Log Analytics 中設定每個資料表的保留來達到此目的。

> [!IMPORTANT]
>
> Microsoft 365 Defender 連接器目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

- 您必須擁有適用于 Microsoft Defender for Endpoint 的有效授權，如 [安裝 Microsoft defender 以進行端點部署](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 

- 您必須在 Azure Active Directory) 中，將租使用者 (上的「全域管理員」角色指派給您的使用者。

## <a name="connect-to-microsoft-365-defender"></a>連接到 Microsoft 365 Defender

如果已部署 Microsoft Defender for Endpoint 並擷取您的資料，則可以輕鬆地將事件記錄串流至 Azure Sentinel。

1. 在 Azure Sentinel 中，選取 [ **資料連線器**]，從資源庫選取 [ **Microsoft 365 Defender (預覽) ** ]，然後選取 [ **開啟連接器] 頁面**。

1. 您可以從對應的「advanced 搜尋」資料表收集下列類型的事件。 標示您想要收集之事件種類的核取方塊：

    | 事件種類 | 資料表名稱 |
    |-|-|
    | 電腦資訊 (包括 OS 資訊)  | DeviceInfo |
    | 電腦的網路屬性 | DeviceNetworkInfo |
    | 進程建立和相關事件 | DeviceProcessEvents |
    | 網路連接和相關事件 | DeviceNetworkEvents |
    | 檔案建立、修改和其他檔案系統事件 | DeviceFileEvents |
    | 建立和修改登錄專案 | DeviceRegistryEvents |
    | 登入和其他驗證事件 | DeviceLogonEvents |
    | DLL 載入事件 | DeviceImageLoadEvents |
    | 其他事件種類 | DeviceEvents |
    |

1. 按一下 [套用變更]****。 

1. 若要查詢 Log Analytics 中的「advanced 搜尋」資料表，請在查詢視窗的上方清單中輸入資料表名稱。

## <a name="verify-data-ingestion"></a>確認資料內嵌

[連接器] 頁面中的資料圖形指出您正在擷取資料。 您會注意到它會顯示單一行，在所有已啟用的資料表中匯總事件量。 啟用連接器之後，您可以使用下列 KQL 查詢，為單一資料表產生類似的事件磁片區圖形 (將 *DeviceEvents* 資料表變更為您選擇的) 所需的資料表：

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

在 [ **後續步驟** ] 索引標籤中，您會找到一些已包含的範例查詢。 您可以在位置執行，或修改並儲存它們。

## <a name="next-steps"></a>下一步
在本檔中，您已瞭解如何使用 Microsoft 365 Defender 連接器，從 Microsoft Defender for Endpoint 取得原始事件資料至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
