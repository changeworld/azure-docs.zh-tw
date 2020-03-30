---
title: Azure IoT 安全中心指南，用於使用 Azure 哨兵進行配置（預覽）*微軟文檔
description: 說明如何配置 Azure Sentinel 以從 Azure 安全中心接收 IoT 解決方案的資料。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303540"
---
> [!IMPORTANT]
> Azure Sentinel 中 IoT 資料連線器的 Azure 安全中心當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>將資料從 IoT 的 Azure 安全中心連接到 Azure 哨兵（預覽） 

在本指南中，瞭解如何將 IoT 資料的 Azure 安全中心連接到 Azure Sentinel。  

> [!div class="checklist"]
> * Prerequisites 
> * 連線設定
> * 日誌分析警示檢視 

連接來自 IoT 的 Azure 安全中心的警報，並將它們直接資料流到 Azure Sentinel 中。

## <a name="prerequisites"></a>Prerequisites

- 您必須具有工作區**讀取**和**寫入**許可權。
- **必須在相關 IoT**中心上**啟用**IoT 的 Azure 安全中心。
- 您必須同時具有要連接的**Azure IoT 中心的****讀取**和**寫入**許可權。
- 還必須對**Azure IoT 中心資源組**具有**讀取**和**寫入**許可權。

> [!NOTE]
> 您必須在訂閱上運行 Azure 安全中心標準層許可才能發送常規 Azure 資源警報。 由於 IoT 的 Azure 安全中心需要免費的層許可，因此只有針對 IoT 相關警報的 Azure 安全中心將轉發到 Azure Sentinel。 

## <a name="connect-to-azure-security-center-for-iot"></a>連接到適用于 IoT 的 Azure 安全中心

1. 在 Azure 哨兵中，選擇**資料連線器**，然後按一下 IoT 的**Azure 安全中心**磁貼。
1. 從右側窗格的底部，按一下 **"打開連接器"頁**。 
1. 按一下 **"連接**"，按一下每個 IoT 中心訂閱旁邊的，其警報和設備警報要資料流到 Azure Sentinel 中。 
    - 如果未在該集線器上啟用 IoT 的 Azure 安全中心，您將看到啟用警告訊息。 按一下 **"啟用**"連結以啟動和啟用服務。 
1. 您可以決定是否希望來自 IoT 的 Azure 安全中心的警報在 Azure Sentinel 中自動建置事件。 在 **"創建事件****"下，選擇"啟用**"以使規則能夠自動從生成的警報創建事件。  此規則可以在 **"分析** > **活動"** 規則下更改或編輯。

> [!NOTE]
>更改連接後，刷新中心清單可能需要 10 秒或更長時間。 

## <a name="log-analytics-alert-display"></a>日誌分析警報顯示

要使用日誌分析中的相關架構來顯示 IoT 警報的 Azure 安全中心：：

1. 打開**日誌** > **安全見解** > **安全警報**， 或搜索**安全警報**. 
2. 篩選器，以便僅查看使用以下 kql 篩選器生成 IoT 警報的 Azure 安全中心：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>服務說明

連接 IoT 中心後，中心資料大約在 15 分鐘後在 Azure Sentinel 中可用。


## <a name="next-steps"></a>後續步驟

在本文檔中，您學習了如何將 IoT 的 Azure 安全中心連接到 Azure Sentinel。 要瞭解有關威脅檢測和安全資料訪問的更多詳細資訊，請參閱以下文章：

- 瞭解如何使用 Azure Sentinel[來查看資料以及潛在威脅](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)。

- 瞭解如何訪問[IoT 安全資料](how-to-security-data-access.md)