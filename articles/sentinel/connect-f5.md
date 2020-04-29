---
title: 將 F5 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 F5 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588258"
---
# <a name="connect-f5-to-azure-sentinel"></a>將 F5 連接到 Azure Sentinel

本文說明如何將您的 F5 設備連接到 Azure Sentinel。 F5 資料連線器可讓您輕鬆地將 F5 記錄與 Azure Sentinel 連線，以查看儀表板、建立自訂警示，以及改善調查。 在 Azure Sentinel 上使用 F5 可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 

## <a name="configure-your-f5-to-send-cef-messages"></a>設定 F5 以傳送 CEF 訊息

1. 請移至[F5 設定應用程式安全性事件記錄](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)，並遵循指示以使用下列指導方針來設定遠端記錄：
   - 將**遠端存放裝置類型**設定為**CEF**。
   - 將**通訊協定**設定為**TCP**。
   - 將**ip 位址**設定為 SYSLOG 伺服器 ip 位址。
   - 將**埠號碼**設定為**514**，或將代理程式設定為使用的埠。
   - 您可以將**查詢字串大小上限**設定為您在代理程式中設定的大小。

1. 若要在 Log Analytics 中針對 CEF 事件使用相關的架構，請`CommonSecurityLog`搜尋。

1. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 F5 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。

