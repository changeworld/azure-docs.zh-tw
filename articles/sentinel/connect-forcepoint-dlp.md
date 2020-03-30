---
title: 將力點 DLP 連接到 Azure 哨兵*微軟文檔
description: 瞭解如何將力點 DLP 連接到 Azure 哨兵。
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
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588241"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>將強制端點 DLP 連接到 Azure 哨兵

> [!IMPORTANT]
> Azure Sentinel 中的強制端點資料丟失防護 （DLP） 資料連線器當前處於公共預覽版中。 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Forcepoint DLP 連接器允許您自動將 DLP 事件資料匯出到 Azure Sentinel 中。 您可以使用它來瞭解使用者活動和資料丟失事件。 它還支援與 Azure 工作負荷和其他源中的資料關聯，並改進 Azure Sentinel 內工作手冊的監視功能。

> [!NOTE]
> 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。

## <a name="configure-and-connect-forcepoint-dlp"></a>配置和連接強制端點 DLP

配置強制端點 DLP 以 JSON 格式將事件資料通過 REST API 轉發到 Azure 工作區，如[Forcepoint DLP 集成指南](https://frcpnt.com/dlp-sentinel)中所述。


## <a name="find-your-data"></a>查找您的資料

設置 Forcepoint DLP 連接器後，資料將顯示在自訂日誌**ForcepointDLPEvents_CL**下的日誌分析中。


要在"強制端點 DLP 的日誌分析"中使用相關架構，請搜索**ForcepointDLPEvents_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。

## <a name="next-steps"></a>後續步驟

在本文檔中，您學習了如何將力點 DLP 連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。