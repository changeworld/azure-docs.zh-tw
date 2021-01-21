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
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632896"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>將您的 Squadra 技術 secRMM 資料連線到 Azure Sentinel 

Squadra 技術 secRMM 連接器可讓您輕鬆地將 Squadra 技術 secRMM 安全性解決方案記錄與 Azure Sentinel 連線。 它可讓您查看儀表板、建立自訂警示，以及改進調查。 此連接器可讓您深入瞭解 USB 卸除式存放裝置事件。 Squadra 技術之間的整合 secRMM 和 Azure Sentinel 會使用 REST API。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>設定和連接 Squadra 技術 secRMM 

Squadra 技術 secRMM 可以將記錄直接整合和匯出至 Azure Sentinel。
1. 在 Azure Sentinel 入口網站中，按一下 [資料連線器] 並選取 [Squadra 技術 secRMM]，然後開啟 [連接器] 頁面。

2. 依照 Azure Sentinel 的《 [Squadra 技術入門指南》](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) 中所述的步驟取得 Azure Sentinel 中的 secRMM 資料。   

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料就會出現在 [ **記錄**] 的 [ **CustomLogs** ] 區段的 `secRMM_CL` 資料表中。

若要查詢 Squadra 技術 secRMM 記錄，請在查詢視窗的頂端輸入資料表名稱。

## <a name="validate-connectivity"></a>驗證連線能力

最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。 

## <a name="next-steps"></a>下一步

在本檔中，您已瞭解如何將 Squadra 技術 secRMM 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
