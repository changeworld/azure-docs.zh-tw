---
title: 如何在環境中配置保留 - Azure 時間序列見解 |微軟文檔
description: 瞭解如何在 Azure 時間序列見解環境中配置保留。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 3fdc007caaa4cb79f6083599a5bc176bc022fb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278602"
---
# <a name="configuring-retention-in-time-series-insights"></a>在時間序列深入解析中設定保留期

本文說明如何在 Azure 時間序列深入解析中，設定 [資料保留時間]**** 和 [超過儲存空間限制的行為]****。

## <a name="summary"></a>總結

每個 Azure 時間序列見解環境都有一個用於配置**資料保留時間的**設置。 可設定的值為 1 到 400 天。 資料會依環境儲存容量或保留持續時間 (1-400) 進行刪除，以先達到者為準。

每個時間序列見解環境都有一個額外的設置**存儲限制超出行為**。 此設定可控制當達到環境的容量上限時，要執行的輸入與清除行為。 有兩個行為可供選擇：

- **清除舊資料** (預設)
- **暫停輸入**

如需詳細資訊以進一步了解這些設定，請檢閱[了解時間序列深入解析中的保留期](time-series-insights-concepts-retention.md)。  

## <a name="configure-data-retention"></a>設定資料保留期

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 找出您的現有時間序列深入解析環境。 在 Azure 入口網站左側的功能表中選取 [所有資源]****。 選取 Time Series Insights 環境。

1. 在 **"設置"** 標題下，選擇 **"存儲配置**"。

    [![在"設置"下，選擇"存儲配置"](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. 選擇 **"資料保留時間"（以天表示）** 以使用滑塊欄配置保留，或在文字方塊中鍵入數位。

1. 請注意 [容量]**** 設定，因為此設定會影響資料事件數量上限和用於儲存資料的總儲存體容量。

1. 切換 [超過儲存空間限制的行為]**** 設定。 選取 [清除舊資料]**** 或 [暫停輸入]**** 行為。

    [![暫停入口 - 接受並保存。](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. 查看文檔以瞭解資料丟失的潛在風險。 選取 [儲存]**** 以設定變更。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請檢閱[了解時間序列深入解析中的保留期](time-series-insights-concepts-retention.md)。

- [瞭解如何擴展時間序列見解環境](time-series-insights-how-to-scale-your-environment.md)。

- 瞭解如何[規劃您的環境](time-series-insights-environment-planning.md)。
