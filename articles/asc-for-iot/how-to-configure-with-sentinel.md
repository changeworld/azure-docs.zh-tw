---
title: 使用 Azure Sentinel 進行設定的 IoT 指南 Azure 資訊安全中心（預覽） |Microsoft Docs
description: 說明如何設定 Azure Sentinel，以從您的 Azure 資訊安全中心 IoT 解決方案接收資料。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 082b33332051fee9da2aebe63b0c41edb300afaf
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303540"
---
> [!IMPORTANT]
> Azure Sentinel 中的 IoT 資料連線器 Azure 資訊安全中心目前為公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>將您的資料從 IoT Azure 資訊安全中心連線到 Azure Sentinel （預覽） 

在本指南中，您將瞭解如何將 IoT 資料的 Azure 資訊安全中心連線至 Azure Sentinel。  

> [!div class="checklist"]
> * Prerequisites 
> * 連線設定
> * Log Analytics 警示視圖 

從 IoT 的 Azure 資訊安全中心連接警示，並直接將它們串流至 Azure Sentinel。

## <a name="prerequisites"></a>Prerequisites

- 您必須具有工作區**讀取**和**寫入**許可權。
- 您必須在相關 IoT 中樞上**啟用** **IoT 的 Azure 資訊安全中心**。
- 您必須同時擁有您想要連線之**Azure IoT 中樞**的 [**讀取**] 和 [**寫入**] 許可權。
- 您也必須擁有**Azure IoT 中樞資源群組**的 [**讀取**] 和 [**寫入**] 許可權。

> [!NOTE]
> 您必須擁有在訂用帳戶上執行的 Azure 資訊安全中心標準層授權，才能傳送一般 Azure 資源警示。 使用 IoT Azure 資訊安全中心所需的免費層授權時，只會將 IoT 相關警示的 Azure 資訊安全中心轉送至 Azure Sentinel。 

## <a name="connect-to-azure-security-center-for-iot"></a>連線到 IoT 的 Azure 資訊安全中心

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [**適用于 IoT 的 Azure 資訊安全中心**] 磚。
1. 在右窗格的底部，按一下 [**開啟連接器] 頁面**。 
1. 按一下每個 IoT 中樞訂用帳戶旁邊的 **[連線]** ，您要將警示和裝置警示串流至 Azure Sentinel。 
    - 如果未在該中樞上啟用 IoT 的 Azure 資訊安全中心，您會看到啟用警告訊息。 按一下 [**啟用**] 連結，以啟動並啟用服務。 
1. 您可以決定是否要讓來自 IoT Azure 資訊安全中心的警示在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**啟用**] 以啟用規則，自動從產生的警示建立事件。  您可以在 [**分析** **] > [** 作用中規則] 底下變更或編輯此規則。

> [!NOTE]
>進行連線變更之後，可能需要10秒以上的時間來重新整理中樞清單。 

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

在本檔中，您已瞭解如何將 IoT Azure 資訊安全中心連線至 Azure Sentinel。 若要深入瞭解威脅偵測和安全性資料存取，請參閱下列文章：

- 瞭解如何使用 Azure Sentinel 來[查看您的資料和潛在威脅](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)。

- 瞭解如何[存取您的 IoT 安全性資料](how-to-security-data-access.md)