---
title: 將檢查點資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將檢查點資料連線到 Azure 哨兵。
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588411"
---
# <a name="connect-check-point-to-azure-sentinel"></a>將檢查點連接到 Azure 哨兵



本文介紹如何將檢查點設備連接到 Azure Sentinel。 通過"檢查點"資料連線器，可以輕鬆地將檢查點日誌與 Azure Sentinel 連接，以查看儀表板、創建自訂警報並改進調查。 在 Azure Sentinel 上使用檢查點將為您提供更多有關組織 Internet 使用方式的見解，並將增強其安全操作功能。 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>將檢查點日誌轉發到 Syslog 代理

將檢查點設備配置為通過 Syslog 代理將 CEF 格式的 Syslog 消息轉發到 Azure 工作區。

1. 轉到[檢查點日誌匯出](https://aka.ms/asi-syslog-checkpoint-forwarding)。
1. 向下滾動到**基本部署**，然後按照說明設置連接，使用以下準則：
   - 將**Syslog 埠**設置為**514**或您在代理上設置的埠。
     - 將 CLI 中**的名稱****和目標伺服器 IP 位址**替換為 Syslog 代理名稱和 IP 位址。
     - 將格式設置為**CEF**。
1. 如果您使用的是 R77.30 或 R80.10 版本，請向上滾動到**安裝**，然後按照說明為您的版本安裝日誌匯出器。
1. 繼續[執行步驟 3：驗證連接](connect-cef-verify.md)。
 

## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將檢查點設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- [驗證連接](connect-cef-verify.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


