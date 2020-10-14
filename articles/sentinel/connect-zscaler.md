---
title: 將 Zscaler 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Zscaler 資料連線至 Azure Sentinel。
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
ms.openlocfilehash: ef6677b6f1103c26bd719a3585800765a029f7fb
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056833"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>將 Zscaler 網際網路存取連線到 Azure Sentinel

本文說明如何將您的 Zscaler Internet Access 設備連線至 Azure Sentinel。 Zscaler data connector 可讓您輕鬆地將 Zscaler 的網際網路存取 (ZIA) 記錄與 Azure Sentinel，以查看儀表板、建立自訂警示，以及改進調查。 在 Azure Sentinel 上使用 Zscaler，可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>設定您的 Zscaler 以傳送 CEF 訊息

1. 在 Zscaler 設備上，您必須設定這些值，以便讓設備以必要的格式將必要的記錄傳送至 Azure Sentinel Syslog 代理程式（以 Log Analytics 代理程式為基礎）。 您可以在設備中修改這些參數，只要您也在 Azure Sentinel 代理程式的 Syslog daemon 中修改它們。
    - 通訊協定 = TCP
    - 埠 = 514
    - 格式 = CEF
    - IP 位址-請務必將 CEF 訊息傳送至您專用的虛擬機器 IP 位址。
 如需詳細資訊，請參閱 [Zscaler 和 Azure Sentinel 部署指南](https://aka.ms/ZscalerCEFInstructions)。
 
   > [!NOTE]
   > 此解決方案支援 Syslog RFC 3164 或 RFC 5424。


1. 若要在 Log Analytics 中針對 CEF 事件使用相關的架構，請搜尋 `CommonSecurityLog` 。
1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Zscaler 網際網路存取連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


