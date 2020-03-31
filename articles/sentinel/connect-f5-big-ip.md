---
title: 將 F5 大 IP 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 F5 BIG-IP 資料連線到 Azure 哨兵。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588275"
---
# <a name="connect-your-f5-big-ip-appliance"></a>連接您的 F5 大 IP 設備 

> [!IMPORTANT]
> Azure Sentinel 中的 F5 BIG-IP 資料連線器當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

F5 BIG-IP 連接器允許您使用 Azure Sentinel 輕鬆連接所有 F5 BIG-IP 日誌，查看活頁簿、創建自訂警報並改進調查。 這使您可以更深入地瞭解組織的網路，並改進您的安全操作功能。 F5 BIG-IP 和 Azure 哨兵之間的集成利用了 REST API。


> [!NOTE]
> 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。

## <a name="configure-and-connect-f5-big-ip"></a>配置和連接 F5 BIG-IP 

F5 BIG-IP 可以直接集成日誌並將其匯出到 Azure Sentinel。

1. 在 Azure Sentinel 門戶中，按一下 **"資料連線器**"並選擇**F5 BIG-IP，** 然後**選擇"打開連接器"頁**。 
1. 要連接 F5 BIG-IP，您必須將 JSON 聲明發佈到系統的 API 終結點。 有關如何執行此操作的說明，請參閱[將 F5 BIG-IP 與 Azure Sentinel 集成](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)。
8. 從 F5 BIG-IP 連接器頁面，複製工作區 ID 和主金鑰，然後按照流資料指示將其粘貼[到 Azure 日誌分析](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)。
1. 完成 F5 BIG-IP 說明後，在 Azure Sentinel 連接器頁中，您將看到已連接的資料類型。
1. 要在日誌分析中為 F5 BIG-IP 事件使用相關架構，請搜索**F5Telemetry_LTM_CL、F5Telemetry_system_CL**和**F5Telemetry_ASM_CL**。 **F5Telemetry_LTM_CL**


## <a name="validate-connectivity"></a>驗證連線能力

可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。 



## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 F5 BIG-IP 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


