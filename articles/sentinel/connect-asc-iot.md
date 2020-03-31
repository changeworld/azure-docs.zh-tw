---
title: 將 IoT 的 Azure 安全中心連接到 Azure 哨兵 |微軟文檔
description: 瞭解如何將 IoT 資料的 Azure 安全中心連接到 Azure 哨兵。
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
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588632"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>將資料從 IoT 的 Azure 安全中心連接到 Azure 哨兵 


> [!IMPORTANT]
> IoT 資料連線器的 Azure 安全中心當前處於公共預覽版中。 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 IoT 連接器的 Azure 安全中心將所有用於 IoT 事件的 Azure 安全中心資料流到 Azure Sentinel 中。 

## <a name="prerequisites"></a>Prerequisites

- **對**Azure Sentinel 部署到的工作區的讀取和**寫入**許可權
- **必須在相關 IoT**中心**上啟用**IoT 的 Azure 安全中心
- 要連接的 Azure **IoT 中心**上的**讀取**和**寫入**許可權
- **Azure** **IoT 中心資源組的**讀取和**寫入**許可權

> [!NOTE]
> 雖然必須在訂閱上啟用 Azure 安全中心**標準**層許可證，以便將 IoT 資源警報資料流到 Azure Sentinel，但只需在訂閱中啟用 Azure 安全中心**免費**層許可證，即查看 Azure Sentinel 中針對 IoT 警報的 Azure 安全中心。 

## <a name="connect-to-azure-security-center-for-iot"></a>連接到適用于 IoT 的 Azure 安全中心

1. 在 Azure 哨兵中，選擇**資料連線器**，然後按一下 IoT 的**Azure 安全中心**磁貼。
1. 從右下角窗格中，按一下 **"打開連接器"頁**。 
1. 按一下 **"連接**"，按一下每個 IoT 中心訂閱旁邊的，其警報和設備警報要資料流到 Azure Sentinel 中。 
    - 如果未啟用該集線器上的 IoT Azure 安全中心，您將看到**啟用**警告訊息。 按一下 **"啟用**"連結以啟動服務。 
1. 您可以決定是否希望來自 IoT 的 Azure 安全中心的警報在 Azure Sentinel 中自動建置事件。 在 **"創建事件****"下，選擇"啟用**"以啟用預設分析規則，以便從連接的安全服務中生成的警報自動創建事件。此規則可以在 **"分析** > **活動"** 規則下更改或編輯。

> [!NOTE]
> 在進行連接更改後，中心清單可能需要一些時間才能刷新。 

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

在本文檔中，您學習了如何將 IoT 資料的 Azure 安全中心連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
