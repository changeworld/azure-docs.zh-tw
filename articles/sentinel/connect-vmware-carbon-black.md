---
title: 將 VMware 碳黑色雲端端點標準資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 VMware 碳黑色雲端端點標準資料連線到 Azure Sentinel。
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096305"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 將您的 VMware 碳黑色雲端端點標準連線至 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 VMware 碳黑色雲端端點標準資料連線器目前為公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

VMware 碳黑色雲端端點標準連接器可讓您輕鬆地將所有[VMware 碳黑色端點標準](https://www.carbonblack.com/products/endpoint-standard/)安全性解決方案記錄與 Azure Sentinel 連線，以查看儀表板、建立自訂警示，並改善調查。 VMware 碳黑色和 Azure Sentinel 之間的整合會使用 Azure Functions，利用 REST API 來提取記錄資料。


> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>設定及連接 VMware 碳黑色雲端端點標準

Azure Functions 可以直接從 VMware 碳黑色雲端端點標準整合和提取事件和記錄，並將其轉送至 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 [ **VMware 碳黑色**連接器]。
2. 選取 [**開啟連接器] 頁面**。
3. 遵循**VMware 碳黑**頁面上的指示。


## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會顯示在 Log Analytics 的 [ **CarbonBlackAuditLogs_CL**]、[ **CarbonBlackNotifications_CL** ] CarbonBlackEvents_CL 和 [* * * * *] 資料表中。

## <a name="validate-connectivity"></a>驗證連線能力
可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何使用 Azure 函數應用程式，將 VMware 碳黑色雲端端點標準連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

