---
title: 達成 Azure 串流分析作業的地理位置冗余
description: 本文說明如何達到 Azure 串流分析作業的地理位置，而不是異地容錯移轉。
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015516"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>達成 Azure 串流分析作業的地理位置冗余

Azure 串流分析不會提供自動異地容錯移轉，但您可以藉由在多個 Azure 區域中部署相同的串流分析作業來達成異地複寫。 每個作業都會連接到本機輸入和本機輸出來源。 您的應用程式必須負責將輸入資料傳送到兩個區域輸入，並在兩個區域輸出之間進行協調。 串流分析工作有兩個不同的實體。

下圖說明使用事件中樞輸入和 Azure 資料庫輸出的「異地冗余串流分析作業」部署範例。

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="異地多餘串流分析作業的圖表":::

## <a name="primarysecondary-strategy"></a>主要/次要策略

您的應用程式需要管理哪個區域的輸出資料庫被視為主要複本，並被視為次要複本。 在主要區域失敗時，應用程式會切換到次要資料庫，並開始從該資料庫讀取更新。 允許最小化重複讀取的實際機制取決於您的應用程式。您可以將其他資訊寫入輸出，以簡化此程式。 例如，您可以將時間戳記或序列識別碼加入至每個輸出，以將重複的資料列略過簡單的作業。 還原主要區域之後，它會使用類似的機制來捕捉次要資料庫。

雖然不同的輸入和輸出類型允許不同的異地複寫選項，但我們建議使用本文中所述的模式來達成地理位置冗余，因為它可提供事件產生者和事件取用者的彈性和控制。

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 監視和管理 Azure 串流分析作業](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [在 Azure 串流分析中進行資料導向的偵錯](stream-analytics-job-diagram-with-metrics.md)