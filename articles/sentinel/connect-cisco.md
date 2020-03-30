---
title: 將思科資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將思科資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588394"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>將 Cisco ASA 連接到 Azure 哨兵



本文介紹如何將 Cisco ASA 設備連接到 Azure Sentinel。 Cisco ASA 資料連線器允許您輕鬆地將 Cisco ASA 日誌與 Azure Sentinel 連接，查看儀表板、創建自訂警報並改進調查。 在 Azure Sentinel 上使用 Cisco ASA 將為您提供更多瞭解組織 Internet 使用方式的見解，並將增強其安全操作功能。 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>將 Cisco ASA 日誌轉發到系統代理

Cisco ASA 不支援 CEF，因此日誌以 Syslog 方式發送，Azure Sentinel 代理知道如何解析它們，就像它們是 CEF 日誌一樣。 配置 Cisco ASA 可通過 Syslog 代理將 Syslog 消息轉發到 Azure 工作區：

1. 轉到[將 Syslog 消息發送到外部 Syslog 伺服器](https://aka.ms/asi-syslog-cisco-forwarding)，然後按照說明設置連接。 當出現以下提示時，請使用以下參數：
    - 將**埠**設置為 514 或您在代理中設置的埠。
    - **syslog_ip**集到代理的 IP 位址。

1. 要在日誌分析中為思科事件使用相關架構，請搜索`CommonSecurityLog`。

1. 繼續[執行步驟 3：驗證連接](connect-cef-verify.md)。




## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Cisco ASA 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


