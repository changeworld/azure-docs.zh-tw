---
title: 將 Palo Alto 網路資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Palo Alto 網路資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588122"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>將 Palo Alto 網路連線到 Azure Sentinel



本文說明如何將您的 Palo Alto 網路設備連線到 Azure Sentinel。 Palo Alto Networks data connector 可讓您輕鬆地將 Palo Alto 網路記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改善調查。 在 Azure Sentinel 上使用 Palo Alto 網路，可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>將 Palo Alto 網路記錄轉送到 Syslog 代理程式

設定 Palo Alto 網路，以透過 Syslog 代理程式將 Syslog 訊息以 CEF 格式轉送至您的 Azure 工作區：
1.  前往[一般事件格式（CEF）設定指南](https://docs.paloaltonetworks.com/resources/cef)，並下載適用于您裝置類型的 pdf。 遵循指南中的所有指示，設定您的 Palo Alto 網路設備來收集 CEF 事件。 

1.  移至 [[設定 Syslog 監視](https://aka.ms/asi-syslog-paloalto-forwarding)]，然後遵循步驟2和3來設定從 Palo Alto 網路應用裝置到 AZURE SENTINEL 的 CEF 事件轉送。

    1. 請務必將**Syslog 伺服器格式**設定為**BSD**。

       > [!NOTE]
       > 從 PDF 複製/貼上作業可能會變更文字並插入隨機字元。 若要避免這種情況，請將文字複製到編輯器，並移除可能會在貼上之前中斷記錄格式的任何字元，如下列範例所示。
 
        ![CEF 文字複製問題](./media/connect-cef/paloalto-text-prob1.png)

1. 若要在 Log Analytics 中針對 Palo Alto Networks 事件使用相關的架構，請搜尋**CommonSecurityLog**。

1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Palo Alto 網路設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。


