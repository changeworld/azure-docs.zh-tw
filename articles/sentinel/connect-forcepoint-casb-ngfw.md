---
title: 將 Forcepoint 產品連接到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Forcepoint 產品連線到 Azure Sentinel。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588224"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>將您的 Forcepoint 產品連線至 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Forcepoint products 資料連線器目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


本文說明如何將您的 Forcepoint 產品連線到 Azure Sentinel。 

Forcepoint 資料連線器可讓您將 Forcepoint Cloud Access Security Broker 和 Forcepoint 新一代防火牆記錄與 Azure Sentinel 連線。 如此一來，您就可以將使用者定義的記錄自動匯出至 Azure Sentinel。 此連接器可讓您更豐富地看到 Forcepoint 產品所記錄的使用者活動。 它也可以與 Azure 工作負載和其他摘要中的資料進一步相互關聯，並改善 Azure Sentinel 內活頁簿的監視功能。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>將 Forcepoint 產品記錄轉送到 Syslog 代理程式 

設定 Forcepoint 產品，以透過 Syslog 代理程式將 Syslog 訊息的 CEF 格式轉送至您的 Azure 工作區。

1. 設定 Forcepoint 產品以 Azure Sentinel 整合，如下列安裝指南所述：
 - [Forcepoint CASB 整合指南](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW 整合指南](https://frcpnt.com/ngfw-sentinel)

2. 搜尋 CommonSecurityLog 以使用 Log Analytics 中的相關架構，其 DeviceVendor 名稱包含 ' Forcepoint '。 

3. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。



## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Forcepoint 產品連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。

- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。