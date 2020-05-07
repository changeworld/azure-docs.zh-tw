---
title: 監視應用程式
description: 了解如何使用 Azure 入口網站來監視 Azure App Service 中的應用程式。 瞭解所報告的配額和計量。
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 7205f8a842f2086b1cf3a6bbf76c2df48ed679e9
ms.sourcegitcommit: 291b2972c7f28667dc58f66bbe9d9f7d11434ec1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82738094"
---
# <a name="monitor-apps-in-azure-app-service"></a>監視 Azure App Service 中的應用程式
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)為[Azure 入口網站](https://portal.azure.com)中的 web 應用程式、行動裝置和 API 應用程式提供內建的監視功能。

在 Azure 入口網站中，您可以查看應用程式的*配額*和*計量*，並 App Service 方案，並設定*警示*和自動*調整規則型*計量。

## <a name="understand-quotas"></a>了解配額

App Service 中裝載的應用程式都必須遵守其可用資源的某些「限制」。 限制是由與應用程式相關聯的 App Service 方案所定義。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

如果應用程式裝載於「免費」** 或「共用」** 方案中，則應用程式可用資源的限制是由配額所定義。

如果應用程式裝載於「基本」**、「標準」** 或「進階」** 方案中，則其可用資源的限制是由 App Service 方案的大小** (小、中、大) 和執行個體計數** (1、2、3，以此類推) 所設定。

免費或共用應用程式的配額如下︰

| Quota | 描述 |
| --- | --- |
| **CPU (短期)** | 此應用程式在 5 分鐘間隔內允許的 CPU 數量。 此配額會每 5 分鐘重設一次。 |
| **CPU (天)** | 此應用程式在 1 天內允許的 CPU 總量。 此配額會每隔 24 小時在午夜 (UTC) 重設一次。 |
| **記憶體** | 此應用程式允許的記憶體總量。 |
| **頻寬** | 此應用程式在 1 天內允許的連出頻寬總量。 此配額會每隔 24 小時在午夜 (UTC) 重設一次。 |
| **內** | 允許的儲存體總量。 |

唯一適用于*基本*、*標準*和高階託管應用*程式的配額是 Filesystem* 。

如需有關不同 App Service SKU 可用特定配額、限制和功能的詳細資訊，請參閱 [Azure 訂用帳戶服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="quota-enforcement"></a>強制配額

如果應用程式超過 CPU (短期)**、CPU (天)** 或頻寬** 配額，則應用程式會停止，直到重設配額為止。 在此期間，所有連入要求都會導致 HTTP 403 錯誤。

![403 錯誤訊息][http403]

如果超過應用程式記憶體配額，則會暫時停止應用程式。

如果超過檔案系統配額時，任何寫入作業都將會失敗。 寫入作業失敗包括記錄的任何寫入。

升級您的 App Service 方案，即可在應用程式中增加或移除配額。

## <a name="understand-metrics"></a>了解計量

> [!NOTE]
> **檔案系統使用**方式是全域推出的新計量，除非您已列入個人預覽版的白名單中，否則不會有任何資料。
> 

> [!IMPORTANT]
> **平均回應時間**將會被取代，以避免與計量匯總產生混淆。 使用**回應時間**做為取代。

計量提供有關應用程式或 App Service 方案行為的資訊。

若為應用程式，可用的計量如下︰

| 計量 | 描述 |
| --- | --- |
| **回應時間** | 應用程式處理要求所花費的時間（以秒為單位）。 |
| **平均回應時間（已淘汰）** | 應用程式處理要求所花費的平均時間（以秒為單位）。 |
| **平均記憶體工作集** | 應用程式使用的平均記憶體數量 (以 MiB 為單位)。 |
| **連線** | 存在於沙箱 (w3wp.exe 及其子處理序) 中的已繫結通訊端數目。  建立已繫結通訊端時，會透過呼叫 bind()/connect() API 來建立，然後保留直到使用 CloseHandle()/closesocket() 來關閉上述通訊端為止。 |
| **CPU 時間** | 應用程式所耗用的 CPU 數量 (以秒為單位)。 如需此計量的詳細資訊，請參閱[cpu 時間與 cpu 百分比](#cpu-time-vs-cpu-percentage)。 |
| **目前的組件** | 在此應用程式的所有 AppDomain 載入的目前組件數目。 |
| **中的資料** | 應用程式所耗用的連入頻寬量 (MiB)。 |
| **資料輸出** | 應用程式所耗用的連出頻寬量 (MiB)。 |
| **檔案系統使用方式** | 應用程式所耗用的檔案系統配額百分比。 |
| **Gen 0 記憶體回收** | 自應用程式處理序啟動後，對層代 0 物件進行記憶體回收的次數。 較高層代的 GC 包含所有較低層代的 GC。|
| **Gen 1 記憶體回收** | 自應用程式處理序啟動後，對層代 1 物件進行記憶體回收的次數。 較高層代的 GC 包含所有較低層代的 GC。|
| **Gen 2 記憶體回收** | 自應用程式處理序啟動後，對層代 2 物件進行記憶體回收的次數。|
| **控制代碼計數** | 應用程式處理序目前開啟的控制代碼總數。|
| **Http 2xx** | 導致 HTTP 狀態碼 >= 200 但 < 300 的要求計數。 |
| **Http 3xx** | 導致 HTTP 狀態碼 >= 300 但 < 400 的要求計數。 |
| **Http 401** | 導致 HTTP 401 狀態碼的要求計數。 |
| **Http 403** | 導致 HTTP 403 狀態碼的要求計數。 |
| **Http 404** | 導致 HTTP 404 狀態碼的要求計數。 |
| **Http 406** | 導致 HTTP 406 狀態碼的要求計數。 |
| **Http 4xx** | 導致 HTTP 狀態碼 >= 400 但 < 500 的要求計數。 |
| **Http 伺服器錯誤** | 導致 HTTP 狀態碼 >= 500 但 < 600 的要求計數。 |
| **每秒的 IO 其他位元組數** | 應用程式進程對不包含資料的 i/o 作業（例如控制作業）發出位元組的速率。|
| **每秒的 IO 其他作業數** | 應用程式進程發出非讀取或寫入作業之 i/o 作業的速率。|
| **每秒 IO 讀取位元組數** | 應用程式處理序從 I/O 作業讀取位元組的速率。|
| **每秒 IO 讀取作業數** | 應用程式處理序發出讀取 I/O 作業的速率。|
| **每秒 IO 寫入位元組數** | 應用程式處理序將位元組寫入至 I/O 作業的速率。|
| **每秒 IO 寫入作業數** | 應用程式處理序發出寫入 I/O 作業的速率。|
| **記憶體工作集** | 應用程式目前使用的記憶體數量 (MiB)。 |
| **私用位元組** | 私用位元組是指應用程式進程已配置但無法與其他進程共用之記憶體的目前大小（以位元組為單位）。|
| **Requests** | 要求總數 (不論其導致的 HTTP 狀態碼為何)。 |
| **應用程式佇列中的要求數** | 應用程式要求佇列中的要求數目。|
| **執行緒計數** | 應用程式處理序中目前作用中的執行緒數目。|
| **應用程式網域數總計** | 在此應用程式中載入的目前 AppDomain 數目。|
| **已卸載的應用程式網域總計** | 自應用程式啟動後，已卸載的 AppDomain 總數。|


若為 App Service 方案，可用的計量如下︰

> [!NOTE]
> App Service 方案計量只適用於基本**、標準** 和進階** 層中的方案。
> 

| 計量 | 描述 |
| --- | --- |
| **CPU 百分比** | 方案的所有執行個體使用的平均 CPU。 |
| **記憶體百分比** | 方案的所有執行個體使用的平均記憶體。 |
| **中的資料** | 方案的所有執行個體使用的平均連入頻寬。 |
| **資料輸出** | 方案的所有執行個體使用的平均連出頻寬。 |
| **磁碟佇列長度** | 儲存體上已排入佇列的平均讀取和寫入要求數目。 磁片佇列長度很大表示應用程式可能因為過度的磁片 i/o 而變慢。 |
| **Http 佇列長度** | 必須在履行前排入佇列的平均 HTTP 要求數目。 HTTP 佇列長度很大或不斷增加是方案負載過重的徵兆。 |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 時間與 CPU 百分比
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

有兩個計量可反映 CPU 使用量：

**CPU 時間**：適用于在免費或共用方案中裝載的應用程式，因為其中一個配額是以應用程式使用的 CPU 分鐘數來定義。

**CPU 百分比**：適用于基本、標準和高階方案中裝載的應用程式，因為它們可以相應放大。CPU 百分比是所有實例整體使用量的良好指示。

## <a name="metrics-granularity-and-retention-policy"></a>計量資料細微性和保留原則
服務會記錄和匯總應用程式和 app service 方案的計量，並[根據這些規則來保留](../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics)。

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>在 Azure 入口網站中監視配額和計量
若要檢閱影響應用程式的各種配額和計量狀態，請前往 [Azure 入口網站](https://portal.azure.com)。

![Azure 入口網站中的配額圖表][quotas]

若要尋找配額，請選取 [**設定** > ] [**配額**]。 在圖表上，您可以檢閱： 
1. 配額名稱。
1. 重設間隔。
1. 目前限制。
1. 目前的值。

![度量圖表在 Azure 入口網站][metrics]您可以直接從資源的 [**總覽**] 頁面存取計量。 在這裡，您會看到代表一些應用程式計量的圖表。

按一下任一圖表會帶您前往 [計量] 視圖，您可以在其中建立自訂圖表、查詢不同的計量，以及其他更多功能。 

若要進一步了解計量，請參閱[監視服務計量](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

## <a name="alerts-and-autoscale"></a>警示和自動調整
應用程式或 App Service 方案的計量可以連接到警示。 如需詳細資訊，請參閱[接收警示通知](../monitoring-and-diagnostics/insights-alerts-portal.md)。

在基本或更高版本中裝載的 App Service 應用程式，App Service 方案支援自動調整。 自動調整可讓您設定用以監視 App Service 方案計量的規則。 這些規則可以增加或減少執行個體計數，並視需要提供其他資源。 當應用程式過度佈建時，這些規則也可協助您節省成本。

如需詳細資訊，請參閱[如何調整](../monitoring-and-diagnostics/insights-how-to-scale.md)和 [Azure 監視器自動調整的最佳做法](../azure-monitor/platform/autoscale-best-practices.md)。

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
