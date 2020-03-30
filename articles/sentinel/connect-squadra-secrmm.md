---
title: 將 Squadra 技術秒RMM 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Squadra 技術秒RMM 資料連線到 Azure 哨兵。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588105"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>將小隊技術秒RMM 資料連線到 Azure 哨兵 

> [!IMPORTANT]
> Azure Sentinel 中的 Squadra 技術安全卸除式媒體管理器 （secRMM） 資料連線器當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


Squadra 技術 secRMM 連接器允許您輕鬆地將 Squadra 技術 secRMM 安全解決方案日誌與 Azure Sentinel 連接。 它允許您查看儀表板、創建自訂警報和改進調查。 此連接器讓您深入瞭解 USB 卸除式存放裝置事件。 Squadra 技術 secRMM 和 Azure 哨兵之間的集成利用了 REST API。


> [!NOTE]
> 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>配置和連接 Squadra 技術 secRMM 

Squadra 技術 secRMM 可以直接將日誌集成並匯出到 Azure Sentinel。
1. 在 Azure Sentinel 門戶中，按一下"資料連線器"並選擇"Squadra 技術秒RMM"，然後選擇"打開連接器"頁。

2. 按照[Azure 哨兵的"Squadra 技術入職指南](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)"中概述的步驟，在 Azure Sentinel 中獲取 Squadra secRMM 資料。   


## <a name="find-your-data"></a>查找您的資料

成功建立連接後，資料將顯示在自訂日誌secRMM_CL下的日誌分析中。
要在"小隊技術"秒RMM的日誌分析中使用相關架構，請搜索secRMM_CL。

## <a name="validate-connectivity"></a>驗證連線能力
可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。 


## <a name="next-steps"></a>後續步驟
在本文中，您學習了如何將 Squadra 技術 secRMM 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

