---
title: 專用事件中樞概觀 - Azure 事件中樞 | Microsoft Docs
description: 本文概述了專用 Azure 事件中心，它提供事件中心的單租戶部署。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516755"
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

*事件中心群集*為具有最苛刻的流式處理需求客戶提供單租戶部署。 此單租戶產品具有 99.99% 的保證 SLA，僅在我們的專用定價層提供。 事件中心群集每秒可以侵入數百萬個事件，並且有保證的容量和次秒的延遲。 在專用群集中創建的命名空間和事件中心包括標準產品的所有功能等，但沒有任何入口限制。 它還包括流行的[事件中心捕獲](event-hubs-capture-overview.md)功能，無需額外費用，允許您自動批次處理和記錄資料流程到 Azure 存儲或 Azure 資料湖。 

群集由**容量單位 （CUS）** 進行預配和計費，容量單位是預分配的 CPU 和記憶體資源量。 您可以為每個叢集購買 1、2、4、8、12、16 或 20 個 CU。 每個 CU 可以引入和資料流多少取決於多種因素，例如生產者和消費者的數量、有效負載形狀、輸出率（有關詳細資訊，請參閱下面的基準結果）。 

> [!NOTE]
> 預設情況下，所有事件中心群集都啟用卡夫卡，並支援卡夫卡終結點，這些終結點可用於現有的基於卡夫卡的應用程式。 在群集上啟用 Kafka 不會影響非卡夫卡用例;因此，在群集上啟用 Kafka 不會影響您的非 Kafka 用例。沒有選項或需要禁用群集上的 Kafka。

## <a name="why-dedicated"></a>為什麼專用？

專用活動中心為需要企業級容量的客戶提供了三個引人注目的優勢：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>單租戶保證容量，提高性能

專用群集可確保全量級容量，並且可以以完全持久的存儲和次秒延遲進入高達千百萬位元組的流資料，以適應任何突發流量。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>對功能的包容性和獨佔性訪問 
專用產品包括免費捕獲等功能，以及對即將推出的功能（如自帶金鑰 （BYOK） 的獨佔存取權限。 該服務還為客戶管理負載平衡、作業系統更新、安全修補程式和分區，以便您可以在基礎結構維護上花費更少的時間，在構建用戶端功能上花費更多時間。  

#### <a name="cost-savings"></a>成本節約
在高入口容量（>100 個圖量）下，群集每小時的成本明顯低於標準產品/地區購買可比輸送量單位數量的成本。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>事件中心專用配額和限制

活動中心專用產品按固定月價計費，至少使用 4 小時。 專用層提供標準計畫的所有功能，但具有企業規模容量和工作負載要求苛刻的客戶的限制。 

| 功能 | 標準 | 專用 |
| --- |:---:|:---:|
| 頻寬 | 20 個圖（最多 40 個圖） | 20 個庫 |
| 命名空間 |  1 | 每個 CU 50 個 |
| 事件中樞 |  每個命名空間 10 個 | 每個命名空間 1000 個 |
| 輸入事件 | 按百萬個事件付費 | 已包括 |
| 訊息大小 | 100 萬位元組 | 100 萬位元組 |
| 資料分割 | 每個事件中心 32 個 | 每個事件中心 1024 個 |
| 用戶群組 | 每個事件中心 20 個 | 每個 CU 沒有限制，每個事件中心 1000 個 |
| 代理連線 | 包括 1，000 個，最多 5，000 個 | 包括 100 K 和最大值 |
| 訊息保留期 | 7 天，每個 TU 包含 84 GB | 90 天，每個 CU 包含 10 TB |
| 擷取 | 按小時付費 | 已包括 |

## <a name="how-to-onboard"></a>如何加入

通過[Azure 門戶](https://aka.ms/eventhubsclusterquickstart)[創建事件中心群集](event-hubs-dedicated-cluster-create-portal.md)的自助服務體驗現在處於預覽狀態。 如果您有任何問題或需要幫助載入活動中心專用，請聯繫[活動中心團隊](mailto:askeventhubs@microsoft.com)。

## <a name="faqs"></a>常見問題集

#### <a name="what-can-i-achieve-with-a-cluster"></a>使用群集可以實現什麼？

對於事件中心群集，可以引入和資料流多少取決於各種因素，例如生產者、消費者、攝入和處理的速度等等。 

下表顯示我們在測試期間達成的基準測試結果：

| 承載圖形 | 接收者 | 輸入頻寬| 輸入訊息 | 輸出頻寬 | 輸出訊息 | TU 總計 | 每個 CU 的 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 的批次 | 2 | 400 MB/秒 | 400k 消息/秒 | 800 MB/秒 | 800k 消息/秒 | 400 個 TU | 100 個 TU | 
| 10x10KB 的批次 | 2 | 666 MB/秒 | 66.6k 消息/秒 | 1.33 GB/秒 | 133k 消息/秒 | 666 個 TU | 166 個 TU |
| 6x32KB 的批次 | 1 | 1.05 GB/秒 | 34k 消息 / 秒 | 1.05 GB/秒 | 34k 消息/秒 | 1000 個 TU | 250 個 TU |

測試期間使用了下列準則：

- 使用了具有四個容量單元 （CUS） 的專用層事件中心群集。 
- 用於擷取的事件中樞具有 200 個分割區。 
- 已擷取的資料會透過接收自所有分割區的兩個接收者應用程式來接收。

#### <a name="can-i-scale-updown-my-cluster"></a>我可以向上/縮小群集嗎？

創建後，群集將至少收取 4 小時的使用量。 在自助服務體驗的預覽版本中，您可以在*技術>配額>請求向上擴展或縮小專用群集以*向上或向下擴展群集的情況下向事件中心團隊提交[支援請求](https://ms.portal.azure.com/#create/Microsoft.Support)。 可能需要長達 7 天的時間才能完成縮減群集的請求。 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Geo-DR 如何使用我的群集？

可以將專用層群集下的命名空間與專用層群集下的另一個命名空間進行地理配對。 我們不鼓勵在標準產品中將專用層命名空間與命名空間配對，因為輸送量限制將不相容，從而導致錯誤。 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>是否可以將標準命名空間遷移到專用層群集？
我們目前不支援自動遷移過程，用於將事件中心資料從標準命名空間遷移到專用命名空間。 

## <a name="next-steps"></a>後續步驟

請與您的 Microsoft 銷售代表或 Microsoft 支援部門聯繫，瞭解有關活動中心專用的其他詳細資訊。 您還可以通過訪問以下連結創建群集或瞭解有關事件中心定價層的更多情況：

- [通過 Azure 門戶創建事件中心群集](https://aka.ms/eventhubsclusterquickstart) 
- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。
