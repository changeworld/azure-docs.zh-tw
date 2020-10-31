---
title: 將 CyberArk EPV 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 CyberArk 企業密碼保存庫 (EPV) 資料連線器，將其記錄提取至 Azure Sentinel。 查看活頁簿中的 CyberArk EPV 資料、建立警示，以及改進調查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: c375595951eb760d5341db424c5572719b97046a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102876"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>將 CyberArk 企業密碼保存庫 (EPV) 連線到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 CyberArk Enterprise 密碼保存庫 (EPV) 資料連線器目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

CyberArk Syslog 連接器可讓您輕鬆地將所有 CyberArk 安全性解決方案記錄檔與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改進調查。 CyberArk 與 Azure Sentinel 之間的整合會使用 CEF 資料連線器來適當地剖析及顯示 CyberArk Syslog 訊息。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-cyberark-epv"></a>設定並連接 CyberArk EPV

CyberArk EPV 記錄會從保存庫傳送到以 Linux 為基礎的記錄轉送伺服器 (執行 rsyslog 或 syslog-ng) 並安裝 Log Analytics 代理程式，以將記錄匯出至 Azure Sentinel。 如果您沒有這類記錄轉送伺服器，請參閱 [這些指示](connect-cef-agent.md) 以啟動並執行。

1. 在 Azure Sentinel 入口網站中，按一下 [ **資料連線器** ]，選取 [ **CyberArk 企業密碼保存庫] (EPV) 事件 (預覽)** 然後 **開啟 [連接器] 頁面** 。

1. 依照 CyberArk EPV 指示，設定傳送 syslog 資料到記錄轉送伺服器。

1. 驗證您的連線，並使用 [這些指示](connect-cef-verify.md)來確認資料內嵌。 最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料就會出現在 [ **記錄** ] 的 [ **Azure Sentinel** ] 區段的 [ *CommonSecurityLog* ] 資料表中。

若要查詢 Log Analytics 中的 CyberArk EPV 記錄，請在 `CommonSecurityLog` 查詢視窗的頂端輸入。

## <a name="next-steps"></a>下一步

在本檔中，您已瞭解如何將 CyberArk Enterprise 密碼保存庫記錄連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
