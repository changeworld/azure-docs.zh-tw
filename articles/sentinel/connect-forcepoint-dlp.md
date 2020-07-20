---
title: 將 Forcepoint DLP 連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Forcepoint DLP 連線到 Azure Sentinel。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588241"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>將您的 Forcepoint DLP 連線至 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Forcepoint 資料遺失防護（DLP）資料連線器目前為公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Forcepoint DLP 連接器可讓您自動將 DLP 事件-資料匯出至 Azure Sentinel。 您可以使用它來取得使用者活動和資料遺失事件的可見度。 它也可以與 Azure 工作負載和其他摘要中的資料相互關聯，並改善 Azure Sentinel 內活頁簿的監視功能。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-forcepoint-dlp"></a>設定及連接 Forcepoint DLP

設定 Forcepoint DLP，透過如[FORCEPOINT DLP 整合指南](https://frcpnt.com/dlp-sentinel)中所述的 REST API，將事件資料以 JSON 格式轉送至您的 Azure 工作區。


## <a name="find-your-data"></a>尋找您的資料

設定 Forcepoint DLP 連接器之後，資料會顯示在 Log Analytics 的 [CustomLogs **ForcepointDLPEvents_CL**] 底下。


若要在 Log Analytics 中使用 Forcepoint DLP 的相關架構，請搜尋**ForcepointDLPEvents_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Forcepoint DLP 連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。