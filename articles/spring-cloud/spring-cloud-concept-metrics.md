---
title: 了解 Azure Spring Cloud 的計量
description: 瞭解如何在 Azure 春季雲端中檢查計量
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: e488f2ddc44f1339d648cd6fe6b1aae18b748679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892649"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>瞭解 Azure 春季雲端的計量

Azure 計量瀏覽器是 Microsoft Azure 入口網站的元件，可讓您繪製圖表、以視覺化方式關聯趨勢，以及調查計量中的尖峰和下降。 使用計量瀏覽器來調查資源的健康情況和使用量。 

在 Azure 春季雲端中，計量有兩種視點。
* 每個應用程式總覽頁面中的圖表
* 一般計量頁面

 ![計量圖表](media/metrics/metrics-1.png)

應用程式 **總覽** 中的圖表會提供每個應用程式的快速狀態檢查。 [一般 **度量** ] 頁面包含可供參考的所有計量。 您可以在 [一般度量] 頁面中建立自己的圖表，並將其釘選在儀表板中。

## <a name="application-overview-page"></a>應用程式總覽頁面
選取 **應用** 程式中的應用程式，以在 [總覽] 頁面中尋找圖表。  

 ![應用程式計量管理](media/metrics/metrics-2.png)

每個應用程式的 [應用程式概觀]**** 頁面都會呈現計量圖表，可讓您執行應用程式狀態快速檢查。  

 ![應用程式計量總覽](media/metrics/metrics-3.png)

Azure 春季 Cloud 提供這五個具有每分鐘更新計量的圖表：

* **Http 伺服器錯誤**：對您的應用程式發出 HTTP 要求的錯誤計數
* **資料傳入**：您的應用程式所接收的位元組
* **資料輸出**：您的應用程式傳送的位元組
* **要求**：您的應用程式收到的要求
* **平均回應時間**：來自您應用程式的平均回應時間

若為圖表，您可以選取一個小時到七天的時間範圍。

## <a name="common-metrics-page"></a>一般計量頁面

左側導覽窗格中的 **計量** 會連結至 [一般度量] 頁面。

首先，選取要查看的計量：

![選取度量視圖](media/metrics/metrics-4.png)

您可以在下 [一節](#user-metrics-options) 中找到所有計量選項的詳細資料。

接下來，選取每個度量的匯總類型：

![度量匯總](media/metrics/metrics-5.png)

匯總類型表示如何依時間匯總圖表中的度量點。 每分鐘都有一個原始的計量點，而在一分鐘內的預先匯總型別是由計量類型預先定義。
* Sum：將所有值加總為目標輸出。
* 平均：使用期間的平均值做為目標輸出。
* 最大值/最小值：使用期間內的最大值/最小值作為目標輸出。

時間範圍也可以從過去30分鐘調整為過去30天或自訂的時間範圍。

![度量修改](media/metrics/metrics-6.png)

預設視圖會將所有 Azure 春季雲端服務的應用程式計量一起包含在一起。 其中一個應用程式或實例的計量可以在顯示中篩選。  按一下 [ **新增篩選**]，將屬性設為 [ **應用程式**]，然後在 [ **值** ] 文字方塊中選取您想要監視的目標應用程式。 

您可以使用兩種類型的篩選 (屬性) ：
* 應用程式：依應用程式名稱篩選
* 實例：依應用程式實例篩選

![度量篩選](media/metrics/metrics-7.png)

您也可以使用 [套用 **分割** ] 選項，它會針對一個應用程式繪製多行：

![度量分割](media/metrics/metrics-8.png)

>[!TIP]
> 您可以在 [計量] 頁面上建立您自己的圖表，並將其釘選到 **儀表板**。 從命名圖表開始。  接下來，選取右上角的 [釘選至儀表板]****。 您現在可以在入口網站 [儀表板]**** 檢查應用程式。

## <a name="user-metrics-options"></a>使用者度量選項

下表顯示可用的計量和詳細資料。

### <a name="error"></a>錯誤
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧傳動標準名稱 | Unit | 詳細資料 |
>|----|----|----|------------|
>| tomcat。錯誤 | tomcat。錯誤 | Count | 已處理的要求中發生的錯誤數目 |

### <a name="performance"></a>效能
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧傳動標準名稱 | Unit | 詳細資料 |
>|----|----|----|------------|
>| 系統。使用量 | 系統。使用量 | 百分比 | 整個系統的最近 CPU 使用率。 此值是 [0.0，1.0] 間隔中的雙精度浮點數。 值為0.0 時，表示所有 Cpu 在觀察到的最近一段時間都處於閒置狀態，而1.0 的值表示所有 Cpu 在觀察到最近一段期間內，會主動執行100% 的時間。|
>| 處理常式。 cpu。使用量 | 應用程式 CPU 使用率百分比 | 百分比 | JAVA 虛擬機器進程的最近 CPU 使用率。 此值是 [0.0，1.0] 間隔中的雙精度浮點數。 值為0.0 時，表示在觀察到最近一段期間內，沒有任何 Cpu 正在執行來自 JVM 進程的執行緒，而1.0 的值表示所有 Cpu 都在觀察到最近一段時間內，在 JVM 的 JVM 100% 內主動執行執行緒。 JVM 中的執行緒包括應用程式執行緒和 JVM 內部執行緒。|
>| jvm。已認可 | jvm。已認可 | 位元組 | 代表可供 JVM 使用的記憶體數量。 JVM 可能會釋出記憶體給系統，並認可哥能小於 init。 認可一律會大於或等於使用。 |
>| jvm. memory. 使用 | jvm. memory. 使用 | 位元組 | 代表目前使用的記憶體數量（以位元組為單位）。 |
>| jvm。記憶體。最大值 | jvm。記憶體。最大值 | 位元組 | 代表可用於記憶體管理的最大記憶體數量。 如果定義了 max，則已使用和已認可的記憶體數量一律會小於或等於 max。 如果記憶體配置嘗試增加已使用的記憶體（例如使用的記憶體） >，即使已使用 <= max 仍為 true (例如，當系統的虛擬記憶體不足) 時，記憶體配置可能會失敗。 |
>| jvm. 資料大小上限 | jvm. 資料大小上限 | 位元組 | 自 JAVA 虛擬機器啟動之後，舊世代記憶體集區的尖峰記憶體使用量。 |
>| jvm. 資料大小 | jvm. 資料大小 | 位元組 | 完整 GC 之後的舊世代記憶體集區大小。 |
>| jvm. gc。已升級 | jvm. gc。已升級 | 位元組 | 在 GC 到 gc 之後，舊世代記憶體集區大小的正增加計數。 |
>| jvm. 已配置的記憶體 | jvm. 已配置的記憶體 | 位元組 | 在一個 GC 到下一個 GC 之後，將年輕世代記憶體集區的大小增加增加。 |
>| jvm. pause. total. count | jvm. gc. pause (total-count)  | Count | 此 JMV 開始之後的 GC 總數總計，包括年輕和舊 GC。 |
>| jvm. gc. pause. total. time | jvm. gc. pause (total time)  | 毫秒 | 此 JMV 開始後所耗用的 GC 時間總計，包括年輕和舊的 GC。 |

::: zone pivot="programming-language-csharp"
### <a name="performance-net"></a>效能 ( .NET) 

>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧傳動標準名稱 | Unit | 詳細資料 |
>|------|-----------------------------|------|---------|
>| CPU 使用量       | cpu-使用量      | 毫秒 | 進程使用 CPU 的時間量。 |
>| 工作集     | 工作集    | MB    | 進程使用的工作集量。 |
>| GC 堆積大小    | gc-堆積-大小   | MB    | 垃圾收集行程所報告的總堆積大小。 |
>| Gen 0 GC 計數  | gen-0-gc-計數 | Count        | 每秒層代0垃圾收集數目。 |
>| Gen 1 GC 計數  | gen-1-gc-計數 | Count        | 每秒層代1垃圾收集數目。 |
>| Gen 2 GC 計數  | gen-2-gc-計數 | Count        | 每秒層代2垃圾收集數目。 |
>| Gen 0 堆積大小 | gen-0-大小     | 位元組        | 層代0堆積大小。 |
>| 層代 1 堆積大小 | gen-1-大小     | 位元組        | 第1代堆積大小。 |
>| 層代 2 堆積大小 | gen-2-大小     | 位元組        | 層代2堆積大小。 |
>| LOH 堆積大小   | loh-大小       | 位元組        | 大型物件堆積堆積大小。 |
>| 配置速率 | 分配速率     | 位元組        | 每秒配置的位元組數目。 |
>| 元件計數  | 元件計數 | Count        | 載入的元件數目。 |
>| 例外狀況計數 | 例外狀況-計數 | Count       | 每秒的例外狀況數目。 |
>| 執行緒集區執行緒計數      | threadpool-執行緒計數              | Count | 執行緒集區執行緒的數目。 |
>| 監視鎖定爭用計數 | 監視-鎖定-爭用計數        | Count | 嘗試取得監視器鎖定時，每秒發生爭用的次數。 |
>| 執行緒集區佇列長度      | threadpool-佇列-長度              | Count | 執行緒集區工作專案佇列長度。 |
>| 執行緒集區已完成的專案計數 | threadpool-已完成-專案-計數 | Count | 執行緒集區已完成工作專案計數。 |
>| 作用中計時器計數               | 主動-計時器-計數               | Count | 目前作用中的計時器數目。 作用中的計時器是在未來某個時間點已註冊要進行滴答的活動計時器，但尚未取消。 |

如需詳細資訊，請參閱 [dotnet 計數器](/dotnet/core/diagnostics/dotnet-counters)。
::: zone-end

### <a name="request"></a>要求
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧傳動標準名稱 | Unit | 詳細資料 |
>|----|----|----|------------|
>| tomcat. global. 已傳送 | tomcat. global. 已傳送 | 位元組 | 已傳送的資料 Tomcat web 伺服器數量 |
>| tomcat. global. received | tomcat. global. received | 位元組 | 已接收的資料 Tomcat web 伺服器數量 |
>| tomcat. total. count | tomcat. 要求 (總數)  | Count | Tomcat web 伺服器處理的要求總數 |
>| tomcat. 要求數上限 | tomcat. 要求數上限 | 毫秒 | Tomcat web 伺服器處理要求的最長時間 |

::: zone pivot="programming-language-csharp"
### <a name="request-net"></a>要求 ( .NET) 

>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧傳動標準名稱 | Unit | 詳細資料 |
>|------|-----------------------------|------|---------|
>| 每秒要求 | 每秒要求數 | Count | 要求速率。 |
>| 要求總數 | 要求總數 | Count | 要求數總計。 |
>| 目前的要求 | 目前要求 | Count | 目前要求的數目。 |
>| 失敗的要求 | 失敗-要求 | Count | 失敗的要求數目。 |

如需詳細資訊，請參閱 [dotnet 計數器](/dotnet/core/diagnostics/dotnet-counters)。
::: zone-end

### <a name="session"></a>工作階段
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 彈簧傳動標準名稱 | Unit | 詳細資料 |
>|----|----|----|------------|
>| tomcat。作用中。最大值 | tomcat。作用中。最大值 | Count | 同時處於作用中狀態的會話數上限 |
>| tomcat。最大值 | tomcat。最大值 | 毫秒 | 過期的會話處於作用中的最長時間 (秒)  |
>| tomcat. 會話。已建立 | tomcat. 會話。已建立 | Count | 已建立的會話數目 |
>| tomcat. 會話已過期 | tomcat. 會話已過期 | Count | 已過期的會話數目 |
>| tomcat. 會話。已拒絕 | tomcat. 會話。已拒絕 | Count | 因為已達到作用中會話的最大數目，而未建立的會話數目。 |
>| tomcat。現行 | tomcat。現行 | Count | Tomcat 會話使用中計數 |

## <a name="see-also"></a>另請參閱

* [快速入門：使用記錄、計量和追蹤來監視 Azure Spring Cloud 應用程式](spring-cloud-quickstart-logs-metrics-tracing.md)

* [開始使用 Azure 計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [使用診斷設定來分析記錄和計量](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>後續步驟

* [教學課程：使用警示和動作群組監視春季雲端資源](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Azure 春季雲端的配額和服務方案](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)
