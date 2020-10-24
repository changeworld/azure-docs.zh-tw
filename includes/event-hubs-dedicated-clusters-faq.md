---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 345454557c1bd0df3b4e7210229c81f0149af0f3
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495083"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>叢集可以達成哪些目標？

針對事件中樞叢集，您可以內嵌和串流的數量取決於各種因素，例如您的生產者、取用者、擷取和處理的速率，以及其他更多專案。 

下表顯示我們在測試期間達成的基準測試結果：

| 承載圖形 | 接收者 | 輸入頻寬| 輸入訊息 | 輸出頻寬 | 輸出訊息 | TU 總計 | 每個 CU 的 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 的批次 | 2 | 400 MB/秒 | 400000 則訊息/秒 | 800 MB/秒 | 800000 則訊息/秒 | 400 個 TU | 100 個 TU | 
| 10x10KB 的批次 | 2 | 666 MB/秒 | 66600 則訊息/秒 | 1.33 GB/秒 | 133000 則訊息/秒 | 666 個 TU | 166 個 TU |
| 6x32KB 的批次 | 1 | 1.05 GB/秒 | 34000 則訊息/秒 | 1.05 GB/秒 | 34000 則訊息/秒 | 1000 個 TU | 250 個 TU |

測試期間使用了下列準則：

- 使用了四個容量單位的專用層事件中樞叢集 (Cu) 。 
- 用於擷取的事件中樞具有 200 個分割區。 
- 已擷取的資料會透過接收自所有分割區的兩個接收者應用程式來接收。

### <a name="can-i-scale-updown-my-cluster"></a>我可以擴大/縮小我的叢集嗎？

建立之後，叢集會以最少4小時的使用量計費。 在自助體驗的預覽版本中，您可以在 [技術 > 配額] 下提交 [支援要求](https://ms.portal.azure.com/#create/Microsoft.Support) 至「事件中樞」小組 *> 要求擴大或縮小專用* 叢集，以相應增加或減少叢集規模。 最多可能需要7天的時間，才能完成擴大叢集的要求。 

### <a name="how-will-geo-dr-work-with-my-cluster"></a>異地 DR 會如何與我的叢集搭配運作？

您可以將專用層叢集下的命名空間與專用層叢集下的另一個命名空間進行異地配對。 我們不鼓勵將專用層命名空間與標準供應專案中的命名空間配對，因為輸送量限制將會造成錯誤。 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>我可以將我的標準命名空間遷移到屬於專用層叢集嗎？
我們目前不支援自動化的遷移程式，以將您的事件中樞資料從標準命名空間遷移到專用命名空間。 
