---
title: 設定您的安全性解決方案，以將 CEF 資料連線至 Azure Sentinel 預覽 |Microsoft Docs
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588445"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>步驟2：設定您的安全性解決方案以傳送 CEF 訊息

在此步驟中，您將在安全性解決方案本身上執行必要的設定變更，以將記錄檔傳送至 CEF 代理程式。

## <a name="configure-a-solution-with-a-connector"></a>使用連接器設定解決方案

如果您的安全性解決方案已經有現有的連接器，請使用連接器特定的指示，如下所示：

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [趨勢科技 Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>設定其他任何解決方案
如果您的特定安全性解決方案不存在連接器，請使用下列將記錄轉送至 CEF 代理程式的一般指示。

1. 如需如何設定解決方案來傳送 CEF 訊息的步驟，請移至特定設定一文。 如果未列出您的解決方案，您必須在設備上設定這些值，讓應用裝置根據 Log Analytics 代理程式，將必要的記錄檔傳送至 Azure Sentinel Syslog 代理程式。 您可以在應用裝置中修改這些參數，只要您也在 Azure Sentinel 代理程式上的 Syslog daemon 中加以修改即可。
    - 通訊協定 = TCP
    - 埠 = 514
    - 格式 = CEF
    - IP 位址-請務必將 CEF 訊息傳送到您專用於此用途之虛擬機器的 IP 位址。

   > [!NOTE]
   > 此解決方案支援 Syslog RFC 3164 或 RFC 5424。


1. 若要在 Log Analytics 中針對 CEF 事件使用相關的架構，請搜尋 `CommonSecurityLog`。

1. 繼續進行步驟3：[驗證連線能力](connect-cef-verify.md)。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 CEF 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

