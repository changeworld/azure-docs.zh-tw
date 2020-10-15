---
title: '為適用于 Defender 的 Defender (preview 設定 Azure Sentinel) '
description: 說明如何設定 Azure Sentinel，以接收來自您的 Defender for IoT 解決方案的資料。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 12f65d0e7f9c380f77fe4189d26fdeafd426295b
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090789"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>將您的資料從 Defender for IoT 連接至 Azure Sentinel (preview) 

Azure Sentinel 中的 Azure Defender for IoT data connector 目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)。

在本指南中，您將瞭解如何將 Defender for IoT 資料連線到 Azure Sentinel。

> [!div class="checklist"]
> * 必要條件
> * 連線設定
> * Log Analytics 警示視圖

連接來自 Defender 的 IoT 警示，並將其直接串流至 Azure Sentinel。

藉由更緊密整合適用于 IoT 的 Azure Defender 與 Azure Sentinel、第一個雲端原生 SIEM，以及原生 IoT 和安全性的第一個 SIEM，Microsoft 提供了更簡單的方法，讓您在 IT 與產業網路之間整合安全性。 相較于 Azure Sentinel 的機器學習服務，這種整合可讓組織快速偵測到經常跨越 IT 和界限的多階段攻擊。 此外，適用于 IoT 的 Azure Defender 與 Azure Sentinel 的安全性協調流程、自動化和回應 (SOAR) 功能，可讓您使用內建的「內建」手冊來進行自動回應和防止。 

## <a name="prerequisites"></a>必要條件

- 您必須擁有工作區的 **讀取** 和 **寫入** 許可權。
- 您必須在相關的 IoT 中樞上**啟用**適用于**iot 的 Defender** (s) 。
- 您必須具有您想要連接之**Azure IoT 中樞**的**讀取**和**寫入**許可權。
- 您也必須具備**Azure IoT 中樞資源群組**的**讀取**和**寫入**許可權。


## <a name="connect-to-defender-for-iot"></a>連接到適用于 IoT 的 Defender

1. 在 Azure Sentinel 中，選取 [ **資料連線器** ]，然後按一下 [ **適用于 IoT 的 Defender** ] 磚。
1. 從右窗格的底部，按一下 [ **開啟連接器] 頁面**。
1. 按一下您想要串流至 Azure Sentinel 的每個 IoT 中樞訂用帳戶旁的 **[連線]**。
    - 如果該中樞上未啟用 Defender for IoT，您將會看到啟用警告訊息。 按一下 [ **啟用** ] 連結，以啟動並啟用服務。
1. 您可以決定是否要將來自 Defender for IoT 的警示自動產生 Azure Sentinel 中的事件。 在 [ **建立事件**] 底下，選取 [ **啟用** ] 以啟用此規則，以自動從產生的警示建立事件。  您可以在**分析**作用中規則下變更或編輯此規則  >  **Active** 。

> [!NOTE]
>進行連線變更之後，可能需要10秒鐘以上的時間才能重新整理中樞清單。

## <a name="using-log-analytics-for-alert-display"></a>使用 Log Analytics 進行警示顯示

若要使用 Log Analytics 中的相關架構來顯示適用于 IoT 的 Defender 警示：

1. 開啟 [**記錄**]  >  **SecurityInsights**  >  **SecurityAlert**，或搜尋**SecurityAlert**。
1. 使用下列 kql 篩選，篩選以僅查看適用于 IoT 的 Defender 產生的警示：

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>服務附注

連線 IoT 中樞之後，中樞資料會在大約15分鐘後 Azure Sentinel 提供。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Defender for IoT 連線至 Azure Sentinel。 若要深入瞭解威脅偵測和安全性資料存取，請參閱下列文章：

- 瞭解如何使用 Azure Sentinel 來 [查看您的資料和潛在的威脅](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)。

- 瞭解如何 [存取您的 IoT 安全性資料](how-to-security-data-access.md)
