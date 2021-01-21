---
title: 將 F5 BIG IP 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 F5 BIG IP 資料連線至 Azure Sentinel。
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
ms.openlocfilehash: e895640daeb3fbbba2f2708969ee4cf856bf1033
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621176"
---
# <a name="connect-your-f5-big-ip-appliance"></a>連接您的 F5 BIG IP 設備 

F5 BIG IP 連接器可讓您輕鬆地將所有 F5 大型 IP 記錄檔連接到 Azure Sentinel、查看活頁簿、建立自訂警示，以及改進調查。 這可讓您深入瞭解組織的網路，並改善安全性作業功能。 F5 BIG IP 與 Azure Sentinel 之間的整合會使用 REST API。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-f5-big-ip"></a>設定並連接 F5 BIG IP 

F5 BIG IP 可將記錄直接整合和匯出至 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下 [ **資料連線器** ]，然後選取 [ **F5 BIG-IP** ]，然後 **開啟 [連接器] 頁面**。 
1. 若要連接您的 F5 大型 IP，您必須將 JSON 宣告張貼至系統的 API 端點。 如需如何進行此動作的指示，請參閱 [將 F5 BIG IP 與 Azure Sentinel 整合](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)。
8. 從 F5 BIG IP 連接器頁面，複製工作區識別碼和主要金鑰，並將其貼入 [Azure Log Analytics 的串流資料](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)底下的指示。
1. 完成 F5 BIG IP 指示之後，您可以在 [Azure Sentinel 連接器] 頁面中看到已連接的資料類型。
1. 若要在 Log Analytics 中使用適用于 F5 大型 IP 事件的相關架構，請搜尋 **F5Telemetry_LTM_CL**、 **F5Telemetry_system_CL** 和 **F5Telemetry_ASM_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，記錄才會開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 F5 BIG IP 連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


