---
title: 將 Zscaler 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Zscaler 資料連線到 Azure Sentinel。
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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77587986"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>將 Zscaler 網際網路存取連線至 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Zscaler 資料連線器目前為公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何將您的 Zscaler 網際網路存取設備連接到 Azure Sentinel。 Zscaler 資料連線器可讓您輕鬆地將 Zscaler Internet Access （ZIA）記錄與 Azure Sentinel 連線，以查看儀表板、建立自訂警示，以及改善調查。 在 Azure Sentinel 上使用 Zscaler，可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>設定 Zscaler 以傳送 CEF 訊息

1. 在 Zscaler 設備上，您必須設定這些值，讓應用裝置根據 Log Analytics 代理程式，將必要的記錄檔傳送至 Azure Sentinel Syslog 代理程式。 您可以在應用裝置中修改這些參數，只要您也在 Azure Sentinel 代理程式上的 Syslog daemon 中加以修改即可。
    - 通訊協定 = TCP
    - 埠 = 514
    - 格式 = CEF
    - IP 位址-請務必將 CEF 訊息傳送到您專用於此用途之虛擬機器的 IP 位址。
 如需詳細資訊，請參閱[Zscaler 和 Azure Sentinel 部署指南](https://aka.ms/ZscalerCEFInstructions)。
 
   > [!NOTE]
   > 此解決方案支援 Syslog RFC 3164 或 RFC 5424。


1. 若要在 Log Analytics 中針對 CEF 事件使用相關的架構，請`CommonSecurityLog`搜尋。
1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Zscaler 網際網路存取連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。


