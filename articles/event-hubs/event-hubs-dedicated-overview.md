---
title: 專用事件中樞概觀 - Azure 事件中樞 | Microsoft Docs
description: 本文概述專用 Azure 事件中樞，提供事件中樞的單一租使用者部署。
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 1a15206fc35f0d536c7105aa73dfdcfc9967124d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358859"
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

*事件中樞* 叢集為具有最嚴苛串流需求的客戶提供單一租使用者部署。 此單一租使用者供應專案具有保證99.99% 的 SLA，且僅適用于專用定價層。 事件中樞叢集可在保證容量與提供亞秒延遲的情況下，每秒輸入數百萬個事件。 在專用叢集內建立的命名空間和事件中樞包含標準供應專案的所有功能，但沒有任何輸入限制。 它也包含熱門的 [事件中樞捕捉](event-hubs-capture-overview.md) 功能，不需額外費用。 這項功能可讓您自動批次處理和記錄資料流程，以 Azure 儲存體或 Azure Data Lake。 

叢集是依容量單位來布建和計費 **(cu)** 是預先配置的 CPU 和記憶體資源數量。 您可以為每個叢集購買 1、2、4、8、12、16 或 20 個 CU。 每個 CU 可以內嵌和串流的程度，取決於各種不同的因素，例如下列各項： 

- 生產者和取用者數目
- 承載圖形
- 輸出速率

> [!NOTE]
> 所有事件中樞叢集預設都是 Kafka 啟用的，而且支援您現有的 Kafka 型應用程式可使用的 Kafka 端點。 在您的叢集上啟用 Kafka 並不會影響您的非 Kafka 使用案例;沒有任何選項或需要停用叢集上的 Kafka。

## <a name="why-dedicated"></a>為何要進行專用？

專用事件中樞為需要企業級容量的客戶提供三項具吸引力的優點：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>單一租用可保證容量，以獲得更佳的效能

專用叢集可保證以全方位規模提供容量。 它最多可以輸入 gb 的串流資料，並具有完全持久的儲存體和次秒延遲，以容納任何高載的流量。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>功能的內含和獨佔存取權 
專用的供應專案包括免費捕捉的功能，以及即將推出的功能（例如攜帶您自己的金鑰 (BYOK) ）的獨佔存取權。 此服務也會管理負載平衡、作業系統更新、安全性修補程式及資料分割。 因此，您可以花較少的時間來維護基礎結構，以及建立用戶端功能的更多時間。  

#### <a name="cost-savings"></a>節省成本
在高輸入磁片區上 ( # B0 100 輸送量單位) ，叢集的成本會比在標準供應專案中購買可比較的輸送量單位數量更少。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>事件中樞專用配額和限制

事件中樞專用供應項目以每月固定價格計費，最低為 4 小時的使用量。 專用層提供標準方案的所有功能，但企業規模的容量和限制適用于具有要求的工作負載的客戶。 

| 功能 | 標準 | 專用 |
| --- |:---|:---|
| 頻寬 | 20 Tu (高達 40 Tu)  | 20 個 CU |
| 命名空間 |  1 | 每個 CU 50 個 |
| 事件中樞 |  每個命名空間10個 | 每一命名空間 1000 個 |
| 輸入事件 | 按百萬個事件付費 | 已包括 |
| 訊息大小 | 1000000位元組 | 1000000位元組 |
| 資料分割 | 每個事件中樞32 | 每個事件中樞1024<br/>每個 CU 2000 個 |
| 用戶群組 | 每個事件中樞20個 | 沒有每個 CU 的限制，每個事件中樞 1000 個 |
| 代理連線 | 1000包含、5000最大值 | 包含的 100 K 和最大值 |
| 訊息保留期 | 7天，每 TU 包含 84 GB | 90 天，每個 CU 包含 10 TB |
| 擷取 | 按小時付費 | 已包括 |

如需更多配額和限制，請參閱 [事件中樞配額和限制](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>如何加入

透過[Azure 入口網站](https://aka.ms/eventhubsclusterquickstart)[建立事件中樞](event-hubs-dedicated-cluster-create-portal.md)叢集的自助體驗現已進入預覽階段。 如果您有任何疑問或需要協助，讓您上線至事件中樞專用，請洽詢 [事件中樞團隊](mailto:askeventhubs@microsoft.com)。

## <a name="faqs"></a>常見問題集

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>後續步驟

請洽詢您的 Microsoft 業務代表或 Microsoft 支援服務，以取得有關事件中樞專用的其他詳細資料。 您也可以造訪下列連結來建立叢集或深入瞭解事件中樞定價層：

- [透過 Azure 入口網站建立事件中樞叢集](https://aka.ms/eventhubsclusterquickstart) 
- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。
