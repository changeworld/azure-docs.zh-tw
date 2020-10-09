---
title: 將 Squadra 技術 secRMM 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Squadra 技術 secRMM 資料連線到 Azure Sentinel。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588105"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>將您的 Squadra 技術 secRMM 資料連線到 Azure Sentinel 

> [!IMPORTANT]
> Azure Sentinel 中的 Squadra 技術安全性卸載式媒體管理員 (secRMM) 資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


Squadra 技術 secRMM 連接器可讓您輕鬆地將 Squadra 技術 secRMM 安全性解決方案記錄與 Azure Sentinel 連線。 它可讓您查看儀表板、建立自訂警示，以及改進調查。 此連接器可讓您深入瞭解 USB 卸除式存放裝置事件。 Squadra 技術之間的整合 secRMM 和 Azure Sentinel 會使用 REST API。


> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>設定和連接 Squadra 技術 secRMM 

Squadra 技術 secRMM 可以將記錄直接整合和匯出至 Azure Sentinel。
1. 在 Azure Sentinel 入口網站中，按一下 [資料連線器] 並選取 [Squadra 技術 secRMM]，然後開啟 [連接器] 頁面。

2. 依照 Azure Sentinel 的《 [Squadra 技術入門指南》](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) 中所述的步驟取得 Azure Sentinel 中的 secRMM 資料。   


## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會出現在 Log Analytics 的 CustomLogs secRMM_CL 下。
若要在 Log Analytics 中使用適用于 Squadra 技術 secRMM 的相關架構，請搜尋 secRMM_CL。

## <a name="validate-connectivity"></a>驗證連線能力
可能需要20分鐘的時間，記錄才會開始出現在 Log Analytics 中。 


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Squadra 技術 secRMM 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

