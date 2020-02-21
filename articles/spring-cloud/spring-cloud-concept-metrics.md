---
title: 了解 Azure Spring Cloud 的計量
description: 瞭解如何在 Azure 春季雲端中審查計量
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500336"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>瞭解 Azure 春季雲端的計量

Azure 計量瀏覽器是 Microsoft Azure 入口網站的元件，可讓您繪製圖表、以視覺化方式相互關聯趨勢，以及調查計量中的尖峰和下降。 使用計量瀏覽器來調查資源的健康情況和使用量。 

在 Azure 春季雲端中，計量有兩個視圖點。
* 每個應用程式中的圖表總覽頁面
* [一般計量] 頁面

 ![計量圖表](media/metrics/metrics-1.png)

應用程式**總覽**中的圖表提供每個應用程式的快速狀態檢查。 [一般**計量**] 頁面包含所有可供參考的計量。 您可以在 [一般計量] 頁面中建立自己的圖表，並將其釘選在儀表板中。

## <a name="application-overview-page"></a>應用程式總覽頁面
在 [**應用程式管理**] 中選取應用程式，以在 [總覽] 頁面中尋找圖表。  

 ![應用程式計量管理](media/metrics/metrics-2.png)

每個應用程式的 [應用程式概觀] 頁面都會呈現計量圖表，可讓您執行應用程式狀態快速檢查。  

 ![應用程式計量總覽](media/metrics/metrics-3.png)

Azure 春季雲端提供這五個圖表，其中包含每分鐘更新的計量：

* **Http 伺服器錯誤**：對您應用程式的 HTTP 要求的錯誤計數
* **中的資料**：您的應用程式所接收的位元組
* **資料輸出**：您的應用程式所傳送的位元組
* **要求**：您的應用程式收到的要求
* **平均回應時間**：來自您應用程式的平均回應時間

在圖表中，您可以選取一小時到7天的時間範圍。

## <a name="common-metrics-page"></a>[一般計量] 頁面

左側流覽窗格中的**計量**會連結至 [一般計量] 頁面。

首先，選取要查看的計量：

![選取度量視圖](media/metrics/metrics-4.png)

您可以在下[一節](#user-metrics-options)中找到所有計量選項的詳細資料。

接下來，選取每個度量的匯總類型：

![計量匯總](media/metrics/metrics-5.png)

匯總類型會指出如何依時間匯總圖表中的度量點。 每分鐘有一個原始計量點，而分鐘內的預先匯總類型是由計量類型預先定義。
* 總和：將所有值加總為目標輸出。
* 平均：使用句點中的平均值作為目標輸出。
* 最大/最小值：使用句點作為目標輸出的最大/最小值。

要顯示的時間範圍也可以修改。 可以選擇從過去30分鐘到過去30天的時間範圍，或是自訂的時間範圍。

![度量修改](media/metrics/metrics-6.png)

預設的視圖會同時包含所有 Azure 春季雲端服務的 application's 計量。 可以在顯示中篩選一個應用程式或實例的計量。  按一下 [**新增篩選**]，將屬性設定為 [**應用程式**]，然後在 [**值**] 文字方塊中選取您要監視的目標應用程式。 

您可以使用兩種篩選準則（屬性）：
* 應用程式：依應用程式名稱篩選
* 實例：依應用程式實例篩選

![度量篩選](media/metrics/metrics-7.png)

您也可以使用 [套用**分割**] 選項，它會針對一個應用程式繪製多行：

![度量分割](media/metrics/metrics-8.png)

>[!TIP]
> 您可以在計量頁面中建置自己的圖表，並釘選到您的 [儀表板]。 從命名圖表開始。  接下來，選取右上角的 [釘選至儀表板]。 您現在可以在入口網站 [儀表板] 檢查應用程式。

## <a name="user-metrics-options"></a>使用者度量選項

下表顯示可用的計量和詳細資料。

### <a name="error"></a>錯誤
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 春季傳動標準名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>| Tomcat 全域錯誤 | tomcat. global 錯誤 | Count | 處理的要求發生的錯誤數目 |

### <a name="performance"></a>效能
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 春季傳動標準名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>|系統 CPU 使用量百分比 | system.object。使用方式 | 百分比 | 整個系統的最近 CPU 使用量。 此值是 [0.0，1.0] 間隔中的雙精度浮點數。 值為0.0 時，表示所有 Cpu 在觀察到的最近一段時間都處於閒置狀態，而1.0 的值表示所有 Cpu 都在觀察到最近一段時間內，主動執行100%。|
>| 應用程式 CPU 使用量百分比 | 應用程式 CPU 使用量百分比 | 百分比 | JAVA 虛擬機器進程的最近 CPU 使用量。 此值是 [0.0，1.0] 間隔中的雙精度浮點數。 值為0.0 表示在觀察到最近的一段時間內，沒有任何 Cpu 正在執行來自 JVM 進程的執行緒，而1.0 的值表示所有 Cpu 都是在最近一段期間，從 JVM 100% 的時間執行執行緒。 來自 JVM 的執行緒包括應用程式執行緒和 JVM 內部執行緒。|
>| 已指派應用程式記憶體 | jvm。認可的記憶體 | 位元組 | 代表保證可供 JVM 使用的記憶體數量。 JVM 可能會釋出記憶體給系統，且認可的可能小於 init。 認可的一律會大於或等於使用。 |
>| 使用的應用程式記憶體 | jvm. 記憶體。使用 | 位元組 | 代表目前使用的記憶體數量（以位元組為單位）。 |
>| 應用程式記憶體最大值 | jvm. 記憶體。最大值 | 位元組 | 表示可用於記憶體管理的記憶體數量上限。 如果已定義 max，則已使用和認可的記憶體數量一律小於或等於最大值。 如果記憶體配置嘗試增加已使用的記憶體（例如使用 > 認可，即使使用 < = max 仍為 true （例如，當虛擬記憶體不足時），可能會失敗。 |
>| 可用的舊世代資料大小上限 | jvm. gc. 資料大小上限 | 位元組 | 啟動 JAVA 虛擬機器之後，舊的世代記憶體集區的尖峰記憶體使用量。 |
>| 舊的產生資料大小 | jvm. 資料大小 | 位元組 | 完整 GC 之後的舊層代記憶體集區大小。 |
>| 升級為舊的產生資料大小 | jvm. gc. 記憶體升級。 | 位元組 | 在 GC 到 GC 之後，舊的世代記憶體集區大小的正向計數增加。 |
>| 升階為年輕產生資料大小 | 已配置的 jvm。 | 位元組 | 在一個 GC 到下一個 GC 之後的空間增加時，會遞增。 |
>| GC 暫停計數 | jvm. gc. pause （總計數） | Count | 此 JMV 開始後的 GC 計數總計，包括年輕和舊的 GC。 |
>| GC 暫停總時間 | jvm. gc. pause （總時間） | 毫秒 | 此 JMV 開始後所耗用的 GC 時間總計（包括年輕和舊的 GC）。 |

### <a name="request"></a>要求
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 春季傳動標準名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>| Tomcat 總傳送位元組數 | tomcat. global. 已傳送 | 位元組 | 已傳送的資料 Tomcat web 伺服器數量 |
>| Tomcat 接收的位元組總數 | tomcat. global. received | 位元組 | 已接收的資料 Tomcat web 伺服器數量 |
>| Tomcat 要求總時間 | tomcat. global. 要求（總時間） | 毫秒 | Tomcat web 伺服器處理要求的總時間 |
>| Tomcat 要求總計數 | tomcat. global. 要求（總計-計數） | Count | Tomcat web 伺服器已處理要求的總計數 |
>| Tomcat 要求最大時間 | tomcat. global. 要求數上限 | 毫秒 | Tomcat web 伺服器處理要求的時間上限 |

### <a name="session"></a>工作階段
>[!div class="mx-tdCol2BreakAll"]
>| 名稱 | 春季傳動標準名稱 | 單位 | 詳細資料 |
>|----|----|----|------------|
>| Tomcat 會話最大使用中計數 | tomcat。作用中。最大值 | Count | 同時處於作用中狀態的會話數目上限 |
>| Tomcat 會話最大的存留時間 | tomcat. keep-alive. max | 毫秒 | 過期會話已運作的最長時間（以秒為單位） |
>| Tomcat 會話已建立計數 | tomcat. 會話. 已建立 | Count | 已建立的會話數目 |
>| Tomcat 會話過期計數 | tomcat. 會話已過期 | Count | 已過期的會話數目 |
>| Tomcat 會話已拒絕計數 | tomcat。已拒絕 | Count | 因已達到作用中會話數目上限而未建立的會話數目。 |

## <a name="see-also"></a>另請參閱
* [開始使用 Azure 計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [使用診斷設定來分析記錄和計量](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>後續步驟
* [教學課程：使用警示和動作群組監視春季雲端資源](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Azure 春季雲端的配額和服務方案](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

