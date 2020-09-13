---
title: 將適用于 IoT 的 Azure Defender 連接到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將適用于 IoT 資料的 Azure Defender (Azure 資訊安全中心) 連線至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659607"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>將您的資料從 Azure Defender (先前 Azure 資訊安全中心適用于 IoT 的) 連線至 Azure Sentinel 


> [!IMPORTANT]
> 適用于 IoT 的 Azure Defender data connector 目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用適用于 IoT 的 Azure Defender 連接器，將所有適用于 IoT 的 Azure Defender 事件串流至 Azure Sentinel。 

## <a name="prerequisites"></a>必要條件

- 部署 Azure Sentinel 之工作區的**讀取**和**寫入**許可權
- 您必須在相關的 IoT 中樞 (上**啟用****適用于 Iot 的 Azure Defender**) 
- 您要連接之**Azure IoT 中樞**的**讀取**和**寫入**許可權
- **Azure IoT 中樞資源群組**的**讀取**和**寫入**許可權

## <a name="connect-to-azure-defender-for-iot"></a>連接到適用于 IoT 的 Azure Defender

1. 在 Azure Sentinel 中，選取 [ **資料連線器** ]，然後從資源庫中選取 [ **適用于 IoT 的 Azure Defender** ] (適用於 IoT 的 Azure 資訊安全中心) 。
1. 在右下方的窗格中，按一下 [ **開啟連接器] 頁面**。 
1. 按一下您想要串流至 Azure Sentinel 的每個 IoT 中樞訂用帳戶旁的 **[連線]**。 
    - 如果該中樞上未啟用適用于 IoT 的 Azure Defender，您將會看到 **啟用** 的警告訊息。 按一下 [ **啟用** ] 連結以啟動服務。 
1. 您可以決定是否要讓適用于 IoT 的 Azure Defender 的警示在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**啟用**] 以啟用預設分析規則，以自動從已連線的安全性服務中產生的警示建立事件。您可以在**分析**作用中規則下變更或編輯此規則  >  **Active** 。

> [!NOTE]
> 在進行連線變更之後，中樞清單可能需要一些時間才會重新整理。 

## <a name="log-analytics-alert-display"></a>Log Analytics 警示顯示

若要使用 Log Analytics 中的相關架構來顯示適用于 IoT 的 Azure Defender 警示：

1. 開啟 [**記錄**]  >  **SecurityInsights**  >  **SecurityAlert**，或搜尋**SecurityAlert**。 
2. 使用下列 kql 篩選篩選，僅查看適用于 IoT 的 Azure Defender 產生的警示：

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>服務附注

連線 IoT 中樞之後，中樞資料會在大約15分鐘後 Azure Sentinel 提供。


## <a name="next-steps"></a>接下來的步驟

在本檔中，您已瞭解如何將適用于 IoT 的 Azure Defender 資料連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
