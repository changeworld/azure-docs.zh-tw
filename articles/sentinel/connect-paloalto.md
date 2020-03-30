---
title: 將帕洛阿爾托網路資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將帕洛阿爾托網路資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588122"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>將帕洛阿爾托網路連接到 Azure 哨兵



本文介紹如何將帕洛阿爾托網路設備連接到 Azure Sentinel。 帕洛阿爾托網路資料連線器允許您輕鬆地將帕洛阿爾托網路日誌與 Azure Sentinel 連接，以查看儀表板、創建自訂警報並改進調查。 在 Azure Sentinel 上使用 Palo Alto 網路將為您提供更多有關組織 Internet 使用方式的見解，並將增強其安全操作功能。 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>將帕洛阿爾托網路日誌轉發到 Syslog 代理

配置 Palo Alto 網路，通過 Syslog 代理將 CEF 格式的 Syslog 消息轉發到 Azure 工作區：
1.  轉到[常見事件格式 （CEF） 配置指南](https://docs.paloaltonetworks.com/resources/cef)，並下載適用于裝置類型的 pdf。 按照指南中的所有說明設置您的帕洛阿爾托網路設備來收集 CEF 事件。 

1.  轉到[配置 Syslog 監視](https://aka.ms/asi-syslog-paloalto-forwarding)，然後按照步驟 2 和 3 配置 CEF 事件從帕洛阿爾托網路設備轉發到 Azure Sentinel。

    1. 請確保將**Syslog 伺服器格式**設置為**BSD**。

       > [!NOTE]
       > PDF 中的副本/粘貼操作可能會更改文本並插入隨機字元。 為了避免這種情況，請將文本複製到編輯器，並在粘貼日誌格式之前刪除任何可能破壞日誌格式的字元，如本示例所示。
 
        ![CEF 文本複製問題](./media/connect-cef/paloalto-text-prob1.png)

1. 要在 Log Alto 網路事件的日誌分析中使用相關架構，請搜索**通用安全性記錄檔**。

1. 繼續[執行步驟 3：驗證連接](connect-cef-verify.md)。




## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將帕洛阿爾托網路設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


