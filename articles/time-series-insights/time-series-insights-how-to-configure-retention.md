---
title: 如何在您的環境中設定保留期-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何在 Azure Azure 時間序列深入解析環境中設定保留期。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: 9ee06501134515d9369e98e724e55a66f040fffa
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495120"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>在 Azure 時間序列深入解析 Gen1 中設定保留期

本文說明如何在 Azure 時間序列深入解析中，設定 [資料保留時間]**** 和 [超過儲存空間限制的行為]****。

## <a name="summary"></a>摘要

每個 Azure 時間序列深入解析環境都有設定**資料保留時間**的設定。 可設定的值為 1 到 400 天。 資料會依環境儲存容量或保留持續時間 (1-400) 進行刪除，以先達到者為準。

每個 Azure 時間序列深入解析環境都有額外的「**儲存限制超過**」的設定行為。 此設定可控制當達到環境的容量上限時，要執行的輸入與清除行為。 有兩個行為可供選擇：

- **清除舊資料** (預設)
- **暫停輸入**

如需進一步瞭解這些設定的詳細資訊，請參閱[瞭解 Azure 時間序列深入解析中的保留](time-series-insights-concepts-retention.md)。  

## <a name="configure-data-retention"></a>設定資料保留

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 找出您現有的 Azure 時間序列深入解析環境。 在 Azure 入口網站左側的功能表中選取 [所有資源]****。 選取您的 Azure 時間序列深入解析環境。

1. 在 [**設定**] 標題下，選取 [**儲存體**設定]。

    [![在 [設定] 底下，選取 [儲存體設定]](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. 選取 [**資料保留時間（以天**為單位）]，以使用滑杆列設定保留期，或在文字方塊中輸入數位。

1. 請注意 [容量]**** 設定，因為此設定會影響資料事件數量上限和用於儲存資料的總儲存體容量。

1. 切換 [超過儲存空間限制的行為]**** 設定。 選取 [清除舊資料]**** 或 [暫停輸入]**** 行為。

    [![暫停輸入-接受並儲存。](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. 請參閱檔，以瞭解資料遺失的潛在風險。 選取 [儲存]**** 以設定變更。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[瞭解 Azure 時間序列深入解析中的保留](time-series-insights-concepts-retention.md)。

- 瞭解[如何調整您的 Azure 時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)。

- 瞭解如何[規劃您的環境](time-series-insights-environment-planning.md)。
