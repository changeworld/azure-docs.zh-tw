---
title: 將 AI Vectra 偵測資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 AI Vectra 偵測資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038217"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>將 AI Vectra 偵測連接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 AI Vectra 偵測資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何將您的[AI Vectra](https://www.vectra.ai/product/cognito-detect)偵測設備連線到 Azure Sentinel。 AI Vectra 偵測資料連線器可讓您輕鬆地將 AI Vectra 偵測到 Azure Sentinel，讓您可以在活頁簿中查看資料、用它來建立自訂警示，並將其納入以改善調查。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>設定您的 AI Vectra 偵測設備以傳送 CEF 訊息  

透過您在[步驟1：部署記錄](connect-cef-agent.md)轉寄站中設定的 syslog 轉寄站，設定 AI Vectra 偵測以將 CEF 格式的 Syslog 訊息轉送至您的 Log Analytics 工作區。

1. 從 Vectra 介面，流覽至 [設定] > [通知]，然後選擇 [編輯 Syslog 設定]。 請依照下列指示來設定連接：

    - 新增目的地（[記錄](connect-cef-agent.md)轉寄站的主機名稱）
    - 將埠設定為**514**
    - 將通訊協定設定為**UDP**
    - 將格式設定為**CEF**
    - 設定記錄檔類型（選取所有可用的記錄檔類型）
    - 按一下 [**儲存**]

2. 您可以按一下 [**測試**] 按鈕，強制將一些測試事件傳送至記錄轉寄站。

3. 若要在 Log Analytics 中使用適用于 AI Vectra 偵測事件的相關架構，請搜尋**CommonSecurityLog**。

4. 繼續進行[步驟3：驗證連線能力](connect-cef-verify.md)。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 AI Vectra 偵測設備連線到 Azure Sentinel。 若要充分利用此資料連線器內建的功能，請按一下 [資料連線器] 頁面上的 [**後續步驟**] 索引標籤。 您可以在這裡找到一些現成的範例查詢，讓您可以開始尋找有用的資訊。

若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
