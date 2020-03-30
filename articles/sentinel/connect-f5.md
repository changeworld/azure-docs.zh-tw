---
title: 將 F5 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 F5 資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588258"
---
# <a name="connect-f5-to-azure-sentinel"></a>將 F5 連接到 Azure 哨兵

本文介紹如何將 F5 設備連接到 Azure Sentinel。 F5 資料連線器允許您輕鬆地將 F5 日誌與 Azure Sentinel 連接、查看儀表板、創建自訂警報以及改進調查。 在 Azure Sentinel 上使用 F5 將為您提供更多瞭解組織的 Internet 使用方式的資訊，並將增強其安全操作功能。 

## <a name="configure-your-f5-to-send-cef-messages"></a>配置 F5 以發送 CEF 消息

1. 轉到[F5 配置應用程式安全事件日誌記錄](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)，然後按照說明使用以下準則設置遠端日誌記錄：
   - 將**遠端存放類型**設置為**CEF**。
   - 將**協定**設置為**TCP**。
   - 將**IP 位址**設置為 Syslog 伺服器 IP 位址。
   - 將**埠號**設置為**514**，或將代理設置為要使用的埠。
   - 您可以將**最大查詢字串大小**設置為在代理中設置的大小。

1. 要在日誌分析中為 CEF 事件使用相關架構，請`CommonSecurityLog`搜索 。

1. 繼續[執行步驟 3：驗證連接](connect-cef-verify.md)。


## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 F5 連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

