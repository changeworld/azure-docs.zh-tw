---
title: 將 Cisco 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Cisco 資料連線到 Azure Sentinel。
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588394"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>將 Cisco ASA 連線至 Azure Sentinel



本文說明如何將 Cisco ASA 應用裝置連接到 Azure Sentinel。 Cisco ASA 資料連線器可讓您輕鬆地將 Cisco ASA 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改善調查。 在 Azure Sentinel 上使用 Cisco ASA 可讓您更深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>將 Cisco ASA 記錄轉送到 Syslog 代理程式

Cisco ASA 不支援 CEF，因此記錄會以 Syslog 的形式傳送，而 Azure Sentinel 代理程式知道如何剖析它們，就好像它們是 CEF 記錄一樣。 設定 Cisco ASA，以透過 Syslog 代理程式將 Syslog 訊息轉送至您的 Azure 工作區：

1. 請移至將[Syslog 訊息傳送至外部 Syslog 伺服器](https://aka.ms/asi-syslog-cisco-forwarding)，並遵循指示來設定連線。 出現提示時，請使用這些參數：
    - 將**埠**設定為514或您在代理程式中設定的埠。
    - 將**syslog_ip**設定為代理程式的 ip 位址。

1. 若要在 Log Analytics 中針對 Cisco 事件使用相關的架構，請搜尋 `CommonSecurityLog`。

1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Cisco ASA 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。


