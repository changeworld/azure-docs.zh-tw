---
title: 專用事件中樞概觀 - Azure 事件中樞 | Microsoft Docs
description: 本文提供專用 Azure 事件中樞的總覽，其中提供事件中樞的單一租使用者部署。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "72516755"
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

*事件中樞*叢集針對具有最嚴苛串流需求的客戶提供單一租使用者部署。 此單一租使用者供應專案具有保證99.99% 的 SLA，且僅適用于我們專用的定價層。 事件中樞叢集可以每秒輸入數百萬個事件，並保證容量和二秒延遲。 在專用叢集內建立的命名空間和事件中樞包含標準供應專案的所有功能，但不含任何輸入限制。 其中還包含熱門的[事件中樞 Capture](event-hubs-capture-overview.md)功能，不需額外費用，可讓您自動批次處理並記錄資料流程，以 Azure 儲存體或 Azure Data Lake。 

叢集會依**容量單位（cu）** 布建和計費，這是預先配置的 CPU 和記憶體資源量。 您可以為每個叢集購買 1、2、4、8、12、16 或 20 個 CU。 每個 CU 可以內嵌和串流的數量取決於各種因素，例如生產者和取用者數目、承載圖形、輸出速率（如需詳細資訊，請參閱以下的基準測試結果）。 

> [!NOTE]
> 預設會 Kafka 啟用所有事件中樞叢集，並支援可供現有 Kafka 型應用程式使用的 Kafka 端點。 在您的叢集中啟用 Kafka 不會影響您的非 Kafka 使用案例;沒有任何選項或需要停用叢集上的 Kafka。

## <a name="why-dedicated"></a>為何要專用？

專用事件中樞為需要企業級容量的客戶提供三大優點：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>單一租使用者保證容量以獲得更佳的效能

專用的叢集可保證完整規模的容量，且最多可以輸入 gb 的串流資料，並具有完全持久的儲存空間和二秒延遲，以容納流量中的任何高載。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>功能的內含和獨佔存取權 
專用供應專案包含不需額外成本的 Capture 功能，以及即將推出的功能（如攜帶您自己的金鑰（BYOK））的獨佔存取權。 此服務也會管理客戶的負載平衡、作業系統更新、安全性修補程式和資料分割，讓您可以花更少的時間進行基礎結構維護，以及更多的時間來建立用戶端功能。  

#### <a name="cost-savings"></a>節省成本
在高輸入磁片區（>100 Tu），叢集的成本會比在標準供應專案中購買可比較的輸送量單位數量多出每小時。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>事件中樞專用配額和限制

事件中樞專用供應專案以固定的每月價格計費，最低使用量為4小時。 專用層提供標準方案的所有功能，但具備企業級的容量和限制，可滿足具有工作負載需求的客戶。 

| 功能 | Standard | 專用 |
| --- |:---:|:---:|
| 頻寬 | 20 Tu （最多 40 Tu） | 20 Cu |
| 命名空間 |  1 | 50/CU |
| 事件中樞 |  每個命名空間10個 | 1000每個命名空間 |
| 輸入事件 | 按百萬個事件付費 | 內含 |
| 訊息大小 | 1000000個位元組 | 1000000個位元組 |
| 資料分割 | 每個事件中樞32 | 每個事件中樞1024 |
| 用戶群組 | 每個事件中樞20個 | 每個事件中樞不會有每個 CU 的限制（1000） |
| 代理連線 | 1000包含，5000最大值 | 100 K 包含和最大值 |
| 訊息保留期 | 7天，每個 TU 包含 84 GB | 90天，每個 CU 包含 10 TB |
| 擷取 | 按小時付費 | 內含 |

## <a name="how-to-onboard"></a>如何加入

透過[Azure 入口網站](https://aka.ms/eventhubsclusterquickstart)[建立事件中樞](event-hubs-dedicated-cluster-create-portal.md)叢集的自助體驗現供預覽。 如果您有任何疑問或需要協助上線以事件中樞專用，請洽詢[事件中樞團隊](mailto:askeventhubs@microsoft.com)。

## <a name="faqs"></a>常見問題集

#### <a name="what-can-i-achieve-with-a-cluster"></a>叢集可以達成什麼目標？

針對事件中樞叢集，您可以內嵌和串流處理的數量取決於各種因素，例如您的產生者、取用者、內嵌和處理的速率，以及更多。 

下表顯示我們在測試期間達成的基準測試結果：

| 承載圖形 | 接收者 | 輸入頻寬| 輸入訊息 | 輸出頻寬 | 輸出訊息 | TU 總計 | 每個 CU 的 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 的批次 | 2 | 400 MB/秒 | 400k 訊息數/秒 | 800 MB/秒 | 800k 訊息數/秒 | 400 個 TU | 100 個 TU | 
| 10x10KB 的批次 | 2 | 666 MB/秒 | 66.6 k 訊息數/秒 | 1.33 GB/秒 | 133k 訊息數/秒 | 666 個 TU | 166 個 TU |
| 6x32KB 的批次 | 1 | 1.05 GB/秒 | 34k 則訊息數/秒 | 1.05 GB/秒 | 34k 則訊息數/秒 | 1000 個 TU | 250 個 TU |

測試期間使用了下列準則：

- 使用具有四個容量單位（Cu）的專用層事件中樞叢集。 
- 用於擷取的事件中樞具有 200 個分割區。 
- 已擷取的資料會透過接收自所有分割區的兩個接收者應用程式來接收。

#### <a name="can-i-scale-updown-my-cluster"></a>我可以相應增加/相應減少叢集嗎？

建立之後，叢集會以最少4小時的使用量計費。 在自助體驗的預覽版本中，您可以向事件中樞小組提交[支援要求](https://ms.portal.azure.com/#create/Microsoft.Support)（*技術 > 配額） > 要求相應增加或相應減少專用*叢集，以相應增加或減少您的叢集。 最多可能需要7天的時間，才能完成向下延展叢集的要求。 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>異地 DR 如何與我的叢集搭配使用？

您可以將命名空間與專用層叢集下另一個命名空間的專用層叢集進行異地配對。 我們不鼓勵將專用層命名空間與標準供應專案中的命名空間配對，因為輸送量限制將會不相容，因而導致錯誤。 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>我可以將我的標準命名空間遷移成屬於專用層叢集嗎？
我們目前不支援將您的事件中樞資料從標準命名空間遷移至專用的自動化遷移程式。 

## <a name="next-steps"></a>後續步驟

請洽詢您的 Microsoft 業務代表或 Microsoft 支援服務，以取得有關事件中樞專用的其他詳細資料。 您也可以造訪下列連結來建立叢集或深入瞭解事件中樞定價層：

- [透過 Azure 入口網站建立事件中樞叢集](https://aka.ms/eventhubsclusterquickstart) 
- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。
