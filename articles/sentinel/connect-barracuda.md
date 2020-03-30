---
title: 將梭子魚資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將梭子魚資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588479"
---
# <a name="connect-your-barracuda-appliance"></a>連接梭子魚產品 



梭魚 Web 應用程式防火牆 （WAF） 連接器允許您輕鬆地將梭子魚日誌與 Azure Sentinel 連接，以查看儀表板、創建自訂警報並改進調查。 這使您可以更深入地瞭解組織的網路，並改進您的安全操作功能。 Azure Sentinel 利用**梭子魚和**日誌分析代理之間的本機集成來提供無縫集成。 


> [!NOTE]
> 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。

## <a name="configure-and-connect-barracuda-waf"></a>配置和連接梭子魚 WAF
梭子魚 Web 應用程式防火牆可以通過日誌分析代理將日誌直接集成並匯出到 Azure Sentinel。
1. 轉到[梭子魚 WAF 配置流](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，然後按照說明設置連接，使用這些參數：
    - **工作區 ID**：從 Azure 哨兵梭子魚連接器頁複製工作區 ID 的值。
    - **主鍵**：從 Azure Sentinel 梭子魚連接器頁複製主鍵的值。
1. 要在梭子魚事件的日誌分析中使用相關架構，請搜索**通用安全性記錄檔**和**barracuda_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。 



## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將梭子魚設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


