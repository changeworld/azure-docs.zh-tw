---
title: 將 Citrix WAF 資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Citrix WAF data connector 將其記錄提取至 Azure Sentinel。 查看活頁簿中的 Citrix WAF 資料、建立警示及改進調查。
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
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102873"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>將您的 Citrix WAF 連接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Citrix Web 應用程式防火牆 (WAF) 資料連線器目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何將您的 Citrix Web 應用程式防火牆 (WAF) 設備連線至 Azure Sentinel。 Citrix WAF data connector 可讓您輕鬆地將 Citrix WAF 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改進調查。 藉由將 Citrix WAF CEF 記錄連線到 Azure Sentinel，您可以針對每個記錄，利用搜尋和相互關聯、警示和威脅情報擴充。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>將 Citrix WAF 記錄轉送到 Syslog 代理程式  

Citrix WAF 會將 CEF 格式的 Syslog 訊息傳送至以 Linux 為基礎的記錄轉送伺服器 (執行 rsyslog 或 Syslog-ng) 並在其中安裝 Log Analytics 代理程式，以將記錄轉送至您的 Azure Sentinel 工作區。

1. 如果您沒有這類記錄轉送伺服器，請參閱 [這些指示](connect-cef-agent.md) 以啟動並執行。

1. 遵循 Citrix 提供的指示來 [設定 WAF](https://support.citrix.com/article/CTX234174)、 [設定 CEF 記錄](https://support.citrix.com/article/CTX136146)，以及 [設定將記錄傳送至您的記錄](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)轉寄站。 請務必將記錄傳送至記錄轉寄站電腦 IP 位址上的 TCP 埠514。

1. 驗證您的連線，並使用 [這些指示](connect-cef-verify.md)來確認資料內嵌。 最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料就會出現在 [ **記錄** ] 的 [ **Azure Sentinel** ] 區段的 [ *CommonSecurityLog* ] 資料表中。

若要查詢 Log Analytics 中的 Citrix WAF 記錄，請在 `CommonSecurityLog` 查詢視窗的頂端輸入。

## <a name="next-steps"></a>下一步

在本檔中，您已瞭解如何將 Citrix WAF 連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。