---
title: 避免 Azure 串流分析作業中發生服務中斷
description: 本文說明可讓串流分析作業升級具有彈性的指引。
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75425997"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>在服務更新期間確保串流分析工作可靠性

完全受控服務的其中一個特性，就是能夠快速導入新的服務功能和改良功能。 因此，「串流分析」可以每週 (或更頻繁地) 進行服務更新部署。 不論做了多少測試，現有的執行中工作仍然可能有因引入錯誤而導致中斷的風險。 如果您執行的是任務關鍵性作業，就必須避免這些風險。 您可以遵循 Azure**[配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** 模型來降低此風險。 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure 配對區域如何解決這個問題？

「串流分析」可確保以個別的批次更新在配對區域中的工作。 因此，更新之間會有足夠的時間間隔，以識別潛在問題並加以修復。

「印度中部是一個例外」__(其配對區域印度南部並沒有提供「串流分析」服務)，「串流分析」更新部署不會在一組配對區域同時發生。 **相同群組內**多個區域中的部署可能**同時**發生。

關於**[可用性與配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** 的文章提供了有關配對區域的最新資訊。

建議您將相同的作業部署至兩個配對的區域。 然後，您應該[監視這些作業](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)，以便在發生未預期的情況時收到通知。 如果這些作業的其中一個在串流分析服務更新後最後處於[失敗狀態](https://docs.microsoft.com/azure/stream-analytics/job-states)，您可以聯絡客戶支援以協助找出根本原因。 您也應該將任何下游取用者故障切換到狀況良好的工作輸出。

## <a name="next-steps"></a>後續步驟

* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
