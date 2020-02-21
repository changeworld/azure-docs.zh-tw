---
title: 將 IoT Azure 資訊安全中心連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 IoT 資料的 Azure 資訊安全中心連接至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: rkarlin
ms.openlocfilehash: b68745f04a49565090b0f9b14331036f76e5f132
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501286"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>將您的資料從 IoT Azure 資訊安全中心連線到 Azure Sentinel 


> [!IMPORTANT]
> IoT 資料連線器的 Azure 資訊安全中心目前為公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用適用于 IoT 連接器的 Azure 資訊安全中心，將 IoT 事件的所有 Azure 資訊安全中心串流至 Azure Sentinel。 

## <a name="prerequisites"></a>Prerequisites

- 已部署 Azure Sentinel 之工作區的 [**讀取**] 和 [**寫入**] 許可權
- 必須在您的相關 IoT 中樞上**啟用** **IoT 的 Azure 資訊安全中心**
- 您想要連接之**Azure IoT 中樞**的**讀取**和**寫入**許可權
- **Azure IoT 中樞資源群組**的**讀取**和**寫入**許可權

> [!NOTE]
> 雖然您必須啟用訂用帳戶的 Azure 資訊安全中心**標準**層授權，才能將 IoT 資源警示串流至 Azure Sentinel，但您只需要在訂用帳戶上啟用 Azure 資訊安全中心**免費**層授權，即可在 Azure Sentinel 中查看 IoT 警示的 Azure 資訊安全中心。 

## <a name="connect-to-azure-security-center-for-iot"></a>連線到 IoT 的 Azure 資訊安全中心

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [**適用于 IoT 的 Azure 資訊安全中心**] 磚。
1. 從右下方的窗格中，按一下 [**開啟連接器] 頁面**。 
1. 按一下每個 IoT 中樞訂用帳戶旁邊的 **[連線]** ，您要將警示和裝置警示串流至 Azure Sentinel。 
    - 如果未在該中樞上啟用 IoT 的 Azure 資訊安全中心，您會看到**啟用**警告訊息。 按一下 [**啟用**] 連結以啟動服務。 
1. 您可以決定是否要讓來自 IoT Azure 資訊安全中心的警示在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**啟用**] 以啟用預設分析規則，以自動從已連線安全性服務中產生的警示建立事件。您可以在 [**分析** **] > [** 作用中規則] 底下變更或編輯此規則。

> [!NOTE]
> 在進行連線變更之後，中樞清單可能需要一些時間才能重新整理。 

## <a name="log-analytics-alert-display"></a>Log Analytics 警示顯示

若要在 Log Analytics 中使用相關的架構來顯示 IoT 警示的 Azure 資訊安全中心：

1. 開啟 **記錄** > **SecurityInsights** > **SecurityAlert**，或搜尋**SecurityAlert**。 
2. 使用下列 kql 篩選器篩選，僅查看 IoT 產生警示的 Azure 資訊安全中心：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>服務注意事項

連接 IoT 中樞之後，中樞資料會在大約15分鐘後於 Azure Sentinel 中提供。


## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 IoT 資料的 Azure 資訊安全中心連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。
