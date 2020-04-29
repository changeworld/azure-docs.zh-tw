---
title: 將 F5 BIG LIP 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 F5 BIG LIP 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588275"
---
# <a name="connect-your-f5-big-ip-appliance"></a>連接您的 F5 BIG IP 應用裝置 

> [!IMPORTANT]
> Azure Sentinel 中的 F5 BIG IP 資料連線器目前為公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

F5 BIG IP 連接器可讓您使用 Azure Sentinel 輕鬆地連接所有 F5 大 IP 記錄，以查看活頁簿、建立自訂警示，以及改善調查。 這可讓您深入瞭解組織的網路，並改善您的安全性作業功能。 F5 BIG-IP 和 Azure Sentinel 之間的整合會使用 REST API。


> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-f5-big-ip"></a>設定並連接 F5 BIG IP 

F5 BIG-IP 可以將記錄直接整合到 Azure Sentinel，並將其匯出。

1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後依序選取 [ **F5 BIG IP** ] 和 [**開啟連接器] 頁面**。 
1. 若要連接您的 F5 BIG IP，您必須將 JSON 宣告張貼到系統的 API 端點。 如需如何執行此操作的指示，請參閱[整合 F5 BIG IP 與 Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)。
8. 在 [F5 BIG-IP 連接器] 頁面上，複製 [工作區識別碼] 和 [主要金鑰]，並將它們貼入 [[串流處理資料至 Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)] 底下的指示。
1. 當您完成 F5 BIG IP 指示之後，您會在 [Azure Sentinel 連接器] 頁面中看到連接的資料類型。
1. 若要在 Log Analytics 中使用適用于 F5 大 IP 事件的相關架構，請搜尋**F5Telemetry_LTM_CL**、 **F5Telemetry_system_CL**和**F5Telemetry_ASM_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 F5 BIG IP 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。


