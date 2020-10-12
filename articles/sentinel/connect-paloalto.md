---
title: 將 Palo Alto Networks 資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Palo Alto Networks 資料連線器，輕鬆地將您的 Palo Alto Networks 記錄與 Azure Sentinel 連接。
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
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564561"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>將 Palo Alto Networks 連接到 Azure Sentinel



本文說明如何將 Palo Alto Networks 設備連接到 Azure Sentinel。 Palo Alto Networks data connector 可讓您輕鬆地將 Palo Alto Networks 記錄與 Azure Sentinel 連接、查看儀表板、建立自訂警示，以及改進調查。 在 Azure Sentinel 上使用 Palo Alto Networks，可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>將 Palo Alto Networks 記錄轉送到 Syslog 代理程式

將 Palo Alto Networks 設定為透過 Syslog 代理程式，將 Syslog 訊息以 CEF 格式轉送至您的 Azure 工作區：
1.  移至 [常見事件格式 (CEF) 設定指南](https://docs.paloaltonetworks.com/resources/cef) ，並下載適用于您裝置類型的 pdf。 遵循本指南中的所有指示，設定您的 Palo Alto Networks 設備以收集 CEF 事件。 

1.  移至 [ [設定 Syslog 監視](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) ]，並遵循步驟2和3，設定從 Palo Alto Networks 設備到 AZURE SENTINEL 的 CEF 事件轉送。

    1. 請務必將 **Syslog 伺服器格式** 設定為 **BSD**。

       > [!NOTE]
       > 從 PDF 進行複製/貼上作業可能會變更文字並插入隨機字元。 若要避免這種情況，請將文字複製到編輯器，並移除可能會在貼上時中斷記錄格式的任何字元，如您在此範例中所見。
 
        ![CEF 文字複製問題](./media/connect-cef/paloalto-text-prob1.png)

1. 若要在 Log Analytics 中針對 Palo Alto Networks 事件使用相關的架構，請搜尋 **CommonSecurityLog**。

1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Palo Alto Networks 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


