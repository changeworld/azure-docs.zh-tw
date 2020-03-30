---
title: 將強制端點產品連接到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Forcepoint 產品連接到 Azure 哨兵。
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
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588224"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>將強制端點產品連接到 Azure 哨兵

> [!IMPORTANT]
> Azure Sentinel 中的 Forcepoint 產品資料連線器當前處於公共預覽版中。 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


本文介紹如何將 Forcepoint 產品連接到 Azure Sentinel。 

Forcepoint 資料連線器允許您將 Forcepoint 雲訪問安全代理和強制端點下一代防火牆日誌與 Azure Sentinel 連接。 通過這種方式，您可以自動將使用者定義的日誌即時匯出到 Azure Sentinel 中。 該連接器使您能夠豐富對 Forcepoint 產品記錄的使用者活動的可見度。 它還支援與 Azure 工作負荷和其他源中的資料進一步關聯，並改進 Azure Sentinel 內工作手冊的監視功能。

> [!NOTE]
> 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>將強制端點產品日誌轉發到 Syslog 代理 

將強制端點產品配置為通過 Syslog 代理將 CEF 格式的 Syslog 消息轉發到 Azure 工作區。

1. 按照以下安裝指南中所述，將 Forcepoint 產品設置為 Azure Sentinel 集成：
 - [力點 CASB 集成指南](https://frcpnt.com/casb-sentinel)
 - [力點 NGFW 集成指南](https://frcpnt.com/ngfw-sentinel)

2. 搜索通用安全性記錄檔以使用與設備供應商名稱的日誌分析中的相關架構包含"強制端點"。 

3. 繼續[執行步驟 3：驗證連接](connect-cef-verify.md)。



## <a name="next-steps"></a>後續步驟

在本文檔中，您學習了如何將 Forcepoint 產品連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。

- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。