---
title: 設定適用于 IoT 的 Defender Azure Sentinel
description: 說明如何設定 Azure Sentinel，以接收來自您的 Defender for IoT 解決方案的資料。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/28/2020
ms.author: shhazam
ms.openlocfilehash: 2d82aaadf158e45cb8faaeee0b9b4e0fc80a3420
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247330"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel"></a>將您的資料從 Defender for IoT 連接到 Azure Sentinel 

使用 Defender for IoT connector 將您所有的 Defender for IoT 事件串流至 Azure Sentinel。 

這種整合可讓組織快速偵測到經常跨越 IT 和界限的多階段攻擊。 此外，適用于 IoT 的 Defender 與 Azure Sentinel 的安全性協調流程、自動化和回應 (SOAR) 功能，可讓您使用內建的「內建」手冊來進行自動回應和防止。 

## <a name="prerequisites"></a>必要條件

- 部署 Azure Sentinel 之工作區的 **讀取** 和 **寫入** 許可權
- 您必須在相關的 IoT 中樞上 **啟用** 適用于 **iot 的 Defender** (s) 
- 您必須具有想要連接之 **訂** 用帳戶的 **參與者** 許可權

## <a name="connect-to-defender-for-iot"></a>連接到適用于 IoT 的 Defender

1. 在 Azure Sentinel 中，選取 [ **資料連線器** ]，然後選取 [ **適用于 IoT 的 Defender** ] (可能仍會從資源庫中呼叫適用於 IoT 的 Azure 資訊安全中心) 。

1. 從右窗格的底部，按一下 [ **開啟連接器] 頁面**。

1. 按一下您想要串流至 Azure Sentinel 的每個 IoT 中樞訂用帳戶旁的 **[連線]**。
    - 如果未在訂用帳戶內至少一個 IoT 中樞上啟用 Defender for IoT，您將會收到錯誤訊息。 在 IoT 中樞內啟用適用于 IoT 的 Defender 以移除錯誤。

1. 您可以決定是否要將來自 Defender for IoT 的警示自動產生 Azure Sentinel 中的事件。 在 [ **建立事件**] 底下，選取 [ **啟用** ] 以啟用預設分析規則，以自動從產生的警示建立事件。 您可以在 **分析** 作用  >  中 **規則** 下變更或編輯此規則。

> [!NOTE]
> 在進行連線變更之後， **訂** 用帳戶清單需要10秒鐘以上的時間才能重新整理。 

## <a name="log-analytics-alert-view"></a>Log Analytics 警示視圖

若要使用 Log Analytics 中的相關架構來顯示適用于 IoT 的 Defender 警示：

1. 開啟 [**記錄**]  >  **SecurityInsights**  >  **SecurityAlert**，或搜尋 **SecurityAlert**。

1. 使用下列 kql 篩選，篩選以僅查看適用于 IoT 的 Defender 產生的警示：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>服務附注

連接 **訂** 用帳戶之後，中樞資料會在大約15分鐘後 Azure Sentinel 提供。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Defender for IoT 連線至 Azure Sentinel。 若要深入瞭解威脅偵測和安全性資料存取，請參閱下列文章：

- 瞭解如何使用 Azure Sentinel 來 [查看您的資料和潛在的威脅](../sentinel/quickstart-get-visibility.md)。
- 瞭解如何 [存取您的 IoT 安全性資料](how-to-security-data-access.md)