---
title: 配置安全解決方案，將 CEF 資料連線到 Azure 哨兵預覽*微軟文檔
description: 瞭解如何配置安全解決方案以將 CEF 資料連線到 Azure Sentinel。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588445"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>第 2 步：配置安全解決方案以發送 CEF 消息

在此步驟中，您將對安全解決方案本身執行必要的配置更改，以便將日誌發送到 CEF 代理。

## <a name="configure-a-solution-with-a-connector"></a>使用連接器配置解決方案

如果安全解決方案已具有現有連接器，請使用特定于連接器的說明，如下所示：

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [趨勢科技 Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>配置任何其他解決方案
如果特定安全解決方案不存在連接器，請使用以下通用說明將日誌轉發到 CEF 代理。

1. 有關如何配置解決方案以發送 CEF 消息的步驟，請轉到特定配置文章。 如果未列出解決方案，則需要在設備上設置這些值，以便設備根據日誌分析代理以必要的格式向 Azure Sentinel Syslog 代理髮送必要的日誌。 您可以在設備中修改這些參數，只要在 Azure Sentinel 代理上的 Syslog 守護進程中也修改它們。
    - 協定 = TCP
    - 埠 = 514
    - 格式 = CEF
    - IP 位址 - 請確保將 CEF 消息發送到專用於此目的的虛擬機器的 IP 位址。

   > [!NOTE]
   > 此解決方案支援 Syslog RFC 3164 或 RFC 5424。


1. 要在日誌分析中為 CEF 事件使用相關架構，請`CommonSecurityLog`搜索 。

1. 繼續執行步驟 3：[驗證連接](connect-cef-verify.md)。

## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 CEF 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

