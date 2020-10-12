---
title: 將 F5 ASM 資料連線到 Azure Sentinel | Microsoft Docs
description: 瞭解如何使用 F5 ASM 資料連線器，將 F5 ASM 記錄提取至 Azure Sentinel。 查看活頁簿中的 F5 ASM 資料、建立警示，以及改進調查。
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
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 63e20467750e9a4b0c85de33180d4fa51831e59b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85555415"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>將 F5 ASM 連線到 Azure Sentinel

本文說明如何使用 F5 ASM 資料連接器，輕鬆地將 F5 ASM 記錄提取到 Azure Sentinel 中。 這可讓您在活頁簿中檢視 F5 ASM 資料、用資料來建立自訂警示，並將其納入以改善調查。 在 Azure Sentinel 中擁有 F5 ASM 資料，可讓您深入了解組織的 Web 應用程式安全性，並可增強您的安全性作業功能。 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>設定 F5 ASM 以傳送 CEF 訊息

1. 遵循 [F5 設定應用程式安全性事件記錄](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)中的指示，使用下列指導方針來設定遠端記錄：
   - 將 [遠端儲存體類型] 設定為 [CEF]。
   - 將 [通訊協定] 設定為 [TCP]。
   - 將 [IP 位址] 設定為 Syslog 伺服器的 IP 位址。
   - 將 [連接埠號碼] 設定為 [514]，或您設定要代理程式使用的連接埠。
   - 您可以將 [查詢字串大小上限] 設定為您在代理程式中設定的大小。

1. 若要在 Log Analytics 中為 CEF 事件使用相關結構描述，請搜尋 `CommonSecurityLog`。

1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。


## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何將 F5 ASM 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

