---
title: 了解 Azure Spring Cloud 的計量
description: 瞭解如何查看 Azure 春雲中的指標
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256753"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>瞭解 Azure 春雲的指標

Azure 指標資源管理器是 Microsoft Azure 門戶的一個元件，它允許繪製圖表、直觀地關聯趨勢以及調查指標中的峰值和下降。 使用計量瀏覽器來調查資源的健康情況和使用量。 

在 Azure 春雲中，指標有兩個視圖點。
* 每個應用程式概述頁中的圖表
* 通用指標頁

 ![指標圖表](media/metrics/metrics-1.png)

應用程式**概述**中的圖表為每個應用程式提供快速的狀態檢查。 通用**指標**頁包含可供參考的所有指標。 您可以在通用指標頁中構建自己的圖表，並將其固定在儀表板中。

## <a name="application-overview-page"></a>應用程式概述頁
在**應用管理**中選擇應用以在概覽頁中查找圖表。  

 ![應用程式指標管理](media/metrics/metrics-2.png)

每個應用程式的 [應用程式概觀]**** 頁面都會呈現計量圖表，可讓您執行應用程式狀態快速檢查。  

 ![應用程式指標概述](media/metrics/metrics-3.png)

Azure 春雲為這五個圖表提供了每分鐘更新的指標：

* **Http 伺服器錯誤**： 對應用的 HTTP 要求的錯誤計數
* **資料在**：應用收到的位元組
* **資料出：** 應用發送的位元組
* **請求**：應用收到的請求
* **平均回應時間**：來自應用的平均回應時間

對於圖表，您可以選擇一小時到七天的時間範圍。

## <a name="common-metrics-page"></a>通用指標頁

左側功能窗格中的 **"指標"** 連結到公共指標頁。

首先，選擇要查看的指標：

![選擇指標視圖](media/metrics/metrics-4.png)

所有指標選項的詳細資訊請參閱以下[部分](#user-metrics-options)。

接下來，為每個指標選擇聚合類型：

![指標聚合](media/metrics/metrics-5.png)

聚合類型指示如何按時間聚合圖表中的指標點。 每分鐘有一個原始指標點，並且幾分鐘內的預聚合類型由指標類型預定義。
* 總和：將所有值作為目標輸出相加。
* 平均值：將期間中的平均值用作目標輸出。
* 最大值/最小值：使用期間中的最大值/最小值作為目標輸出。

也可以修改要顯示的時間範圍。 時間範圍可以選擇從過去 30 分鐘到持續 30 天，也可以選擇自訂時間範圍。

![公制修改](media/metrics/metrics-6.png)

預設視圖將 Azure Spring Cloud 服務應用程式的所有指標一起包含。 可以在顯示幕中篩選一個應用或實例的指標。  按一下 **"添加篩選器**"，將屬性設置為**App**，並在 **"值**"文字方塊中選擇要監視的目標應用程式。 

可以使用兩種類型的篩選器（屬性）：
* 應用：按應用名稱篩選
* 實例：按應用實例篩選

![指標篩選器](media/metrics/metrics-7.png)

您還可以使用 **"應用拆分**"選項，該選項將為一個應用繪製多條線：

![公制拆分](media/metrics/metrics-8.png)

>[!TIP]
> 您可以在計量頁面中建置自己的圖表，並釘選到您的 [儀表板]****。 從命名圖表開始。  接下來，選取右上角的 [釘選至儀表板]****。 您現在可以在入口網站 [儀表板]**** 檢查應用程式。

## <a name="user-metrics-options"></a>使用者指標選項

下表顯示了可用的指標和詳細資訊。

### <a name="error"></a>錯誤
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧執行器指標名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>| 湯姆卡特全域錯誤 | tomcat.global.錯誤 | Count | 已處理的請求發生的錯誤數 |

### <a name="performance"></a>效能
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧執行器指標名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>|系統 CPU 使用率 | 系統.cpu.使用率 | 百分比 | 整個系統的最近 CPU 使用率。 此值在 [0.0，1.0] 間隔內加倍。 值 0.0 表示在最近的觀察期間，所有 CPU 都處於空閒狀態，而值 1.0 表示所有 CPU 在最近觀察期間都在 100% 的時間內主動運行。|
>| 應用 CPU 使用率 | 應用 CPU 使用率 | 百分比 | JAVA 虛擬機器進程最近的 CPU 使用率。 此值在 [0.0，1.0] 間隔內加倍。 值 0.0 表示在最近的觀察期間，沒有一個 CPU 運行來自 JVM 進程的執行緒，而值 1.0 表示在觀察的最近一段時間內，所有 CPU 都在主動運行 JVM 100% 的執行緒。 JVM 的執行緒包括應用程式執行緒以及 JVM 內部執行緒。|
>| 應用記憶體分配 | jvm.記憶體.已提交 | 位元組 | 表示保證可供 JVM 使用的記憶體量。 JVM 可能會向系統釋放記憶體，並且提交記憶體可能小於 init。 提交將始終大於或等於使用。 |
>| 使用的應用程式記憶體 | jvm.記憶體.已用 | 位元組 | 表示當前在位元組中使用的記憶體量。 |
>| 應用記憶體最大值 | jvm.記憶體.max | 位元組 | 表示可用於記憶體管理的最大記憶體量。 如果定義最大值，則使用和提交的記憶體量將始終小於或等於最大值。 如果記憶體分配嘗試增加已使用記憶體，即使使用<= 最大值仍然為 true（例如，當系統虛擬記憶體不足時），記憶體分配也可能失敗，以便使用>提交的記憶體。 |
>| 最大可用舊代資料大小 | jvm.gc.max.資料.大小 | 位元組 | 自 JAVA 虛擬機器啟動以來，舊代記憶體池的峰值記憶體使用。 |
>| 舊代資料大小 | jvm.gc.live.data.size.大小 | 位元組 | 完整 GC 後舊代記憶體池的大小。 |
>| 提升為舊代資料大小 | jvm.gc.記憶體.升級 | 位元組 | GC 之前舊代記憶體池的大小正增長計數到 GC 之後。 |
>| 提升為年輕一代資料大小 | jvm.gc.記憶體.已分配 | 位元組 | 增加一個 GC 後年輕代記憶體池的大小，以在下一個 GC 之前增加。 |
>| GC 暫停計數 | jvm.gc.暫停（總數） | Count | 此 JMV 啟動後的總 GC 計數，包括"年輕"和"舊 GC"。 |
>| GC 暫停總時間 | jvm.gc.暫停（總時間） | 毫秒 | 此 JMV 啟動後消耗的總 GC 時間，包括"年輕"和"舊 GC"。 |

### <a name="request"></a>要求
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧執行器指標名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>| 湯姆卡特總傳送的位元組 | tomcat.global.sent | 位元組 | 發送的 Tomcat Web 服務器的資料量 |
>| 湯姆卡特總接收位元組 | tomcat.global.收到 | 位元組 | 收到的 Tomcat Web 服務器的資料量 |
>| 湯姆卡特請求總時間 | tomcat.global.請求（總時間） | 毫秒 | Tomcat Web 服務器處理請求的總時間 |
>| 湯姆卡特請求總數 | tomcat.global.請求（總數） | Count | 處理過的 Tomcat Web 服務器請求的總數 |
>| 湯姆卡特請求最大時間 | tomcat.global.request.max | 毫秒 | Tomcat Web 服務器處理請求的最長時間 |

### <a name="session"></a>工作階段
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧執行器指標名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>| Tomcat 會話 最大活動計數 | tomcat.sessions.active.max | Count | 同時處於活動狀態的最大會話數 |
>| Tomcat 會話最大啟用時間 | 托姆卡特.會話.alive.max | 毫秒 | 過期會話處於活動狀態的最長時間（以秒為單位） |
>| Tomcat 會話創建計數 | tomcat.sessions.創建 | Count | 已創建的會話數 |
>| Tomcat 會話過期計數 | tomcat.會話.過期 | Count | 已過期的會話數 |
>| Tomcat 會話已拒絕計數 | tomcat.sessions.拒絕 | Count | 由於達到的活動會話的最大數而未創建的會話數。 |

## <a name="see-also"></a>另請參閱
* [開始使用 Azure 計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [使用診斷設置分析日誌和指標](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>後續步驟
* [教程：使用警報和操作組監視春雲資源](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Azure 春雲的配額和服務方案](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

