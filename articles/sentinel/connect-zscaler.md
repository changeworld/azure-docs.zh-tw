---
title: 將 Zscaler 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Zscaler 資料連線到 Azure 哨兵。
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587986"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>將 Zscaler 互聯網接入連接到 Azure 哨兵

> [!IMPORTANT]
> Azure Sentinel 中的 Zscaler 資料連線器當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介紹如何將 Zscaler Internet Access 設備連接到 Azure Sentinel。 Zscaler 資料連線器允許您使用 Azure Sentinel 輕鬆連接 Zscaler Internet Access （ZIA） 日誌，以查看儀表板、創建自訂警報並改進調查。 在 Azure Sentinel 上使用 Zscaler 將為您提供更多有關組織 Internet 使用方式的見解，並將增強其安全操作功能。 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>配置 Zscaler 以發送 CEF 消息

1. 在 Zscaler 設備上，您需要設置這些值，以便設備根據日誌分析代理以必要的格式向 Azure Sentinel Syslog 代理髮送必要的日誌。 您可以在設備中修改這些參數，只要在 Azure Sentinel 代理上的 Syslog 守護進程中也修改它們。
    - 協定 = TCP
    - 埠 = 514
    - 格式 = CEF
    - IP 位址 - 請確保將 CEF 消息發送到專用於此目的的虛擬機器的 IP 位址。
 有關詳細資訊，請參閱[Zscaler 和 Azure 哨兵部署指南](https://aka.ms/ZscalerCEFInstructions)。
 
   > [!NOTE]
   > 此解決方案支援 Syslog RFC 3164 或 RFC 5424。


1. 要在日誌分析中為 CEF 事件使用相關架構，請`CommonSecurityLog`搜索 。
1. 繼續[執行步驟 3：驗證連接](connect-cef-verify.md)。


## <a name="next-steps"></a>後續步驟
在本文中，您學習了如何將 Zscaler Internet 訪問連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


