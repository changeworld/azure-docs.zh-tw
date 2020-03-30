---
title: 將福丁特資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Fortinet 資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588190"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>將福丁特連接到 Azure 哨兵



本文介紹如何將 Fortinet 設備連接到 Azure Sentinel。 Fortinet 資料連線器允許您輕鬆地將 Fortinet 日誌與 Azure Sentinel 連接，以查看儀表板、創建自訂警報並改進調查。 在 Azure Sentinel 上使用 Fortinet 將為您提供更多有關組織 Internet 使用方式的見解，並將增強其安全操作功能。 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>將福汀特日誌轉發到 Syslog 代理

將 Fortinet 配置為通過 Syslog 代理將 CEF 格式的 Syslog 消息轉發到 Azure 工作區。

1. 打開 Fortinet 設備上的 CLI 並運行以下命令：

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - 將伺服器**IP 位址**替換為代理的 IP 位址。
    - 將**syslog 埠**設置為**514**或代理上的埠集。
    - 要在早期 FortiOS 版本中啟用 CEF 格式，您可能需要運行命令集**csv 禁用**。
 
   > [!NOTE] 
   > 有關詳細資訊，請訪問[福廷特文件庫](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)。 選擇您的版本，並使用**手冊**和**日誌消息參考**。

1. 要在 Azure 監視器日誌分析中為 Fortinet 事件使用相關架構`CommonSecurityLog`，請搜索 。

1. 繼續[執行步驟 3：驗證連接](connect-cef-verify.md)。


## <a name="next-steps"></a>後續步驟
在本文中，您學習了如何將 Fortinet 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


