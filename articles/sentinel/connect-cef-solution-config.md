---
title: 設定您的安全性解決方案，以將 CEF 資料連線到 Azure Sentinel 預覽版 |Microsoft Docs
description: 瞭解如何設定您的安全性解決方案，以將 CEF 資料連線到 Azure Sentinel。
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
ms.openlocfilehash: e2ed3680a0867ab8f7e2ad41603883f07a4be427
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655743"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>步驟2：設定您的安全性解決方案以傳送 CEF 訊息

在此步驟中，您將會對安全性解決方案本身執行必要的設定變更，以將記錄傳送至 CEF 代理程式。

## <a name="configure-a-solution-with-a-connector"></a>使用連接器設定解決方案

如果您的安全性解決方案已經有現有的連接器，請使用連接器特定的指示，如下所示：

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [趨勢科技 Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>設定其他任何解決方案

如果特定安全性解決方案的連接器不存在，請使用下列將記錄轉送至 CEF 代理程式的一般指示。

1. 移至特定的設定文章，以取得如何設定解決方案以傳送 CEF 訊息的步驟。 如果未列出您的解決方案，您必須在設備上設定這些值，讓設備以必要的格式將必要的記錄傳送給 Azure Sentinel Syslog 代理程式（以 Log Analytics 代理程式為基礎）。 您可以在設備中修改這些參數，只要您也在 Azure Sentinel 代理程式的 Syslog daemon 中修改它們。
    - 通訊協定 = TCP
    - 埠 = 514
    - 格式 = CEF
    - IP 位址-請務必將 CEF 訊息傳送至您專用的虛擬機器 IP 位址。

   > [!NOTE]
   > 此解決方案支援 Syslog RFC 3164 或 RFC 5424。

1. 若要在 Log Analytics 中針對 CEF 事件使用相關的架構，請搜尋 `CommonSecurityLog` 。

1. 繼續進行步驟3： [驗證連線能力](connect-cef-verify.md)。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 CEF 設備連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](./tutorial-detect-threats-built-in.md)。