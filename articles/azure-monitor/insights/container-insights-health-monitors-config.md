---
title: 適用于容器健全狀況監視設定的 Azure 監視器 |Microsoft Docs
description: 本文提供的內容說明容器 Azure 監視器中健全狀況監視的詳細設定。
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80055707"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>適用于容器的 Azure 監視器健全狀況監視設定指南

監視是用於測量健全狀況及偵測容器 Azure 監視器中錯誤的主要元素。 這篇文章可協助您瞭解如何測量健全狀況的概念，以及組成健全狀況模型的元素，以使用[健全狀況（預覽）](container-insights-health.md)功能來監視和報告 Kubernetes 叢集的健康情況。

>[!NOTE]
>此健全狀況功能目前為公開預覽階段。
>

## <a name="monitors"></a>監視器

監視會測量受管理物件某方面的健全狀況。 監視各有兩到三種健全狀況狀態。 在任何特定時間，監視都將處於一種 (且只限一種) 可能的狀態。 當容器化代理程式載入監視時，它會初始化為狀況良好的狀態。 只有在偵測到另一個狀態的指定條件時，狀態才會變更。

特定物件的整體健全狀況是由其各個監視的健全狀況所決定。 此階層會在 [適用于容器的 Azure 監視器中的 [健康情況階層] 窗格中說明。 健全狀況的匯總原則是匯總監視設定的一部分。

## <a name="types-of-monitors"></a>監視類型

|監視 | 描述 | 
|--------|-------------|
| 單位監視 |單位監視會測量資源或應用程式的某些層面。 這可能會檢查效能計數器，以判斷資源的效能或其可用性。 |
|彙總監視器 | 匯總監視會將多個監視器分組，以提供單一健全狀況匯總健全狀況狀態。 單位監視通常設定在特定的匯總監視之下。 例如，節點匯總監視會匯總節點 CPU 使用率、記憶體使用率和節點狀態的狀態。
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>匯總監視健全狀況匯總套件原則

每個匯總監視都會定義健全狀況匯總套件原則，這是用來根據監視器底下的監視健全狀況來判斷匯總監視器健全狀況的邏輯。 匯總監視的可能健全狀況匯總套件原則如下所示：

#### <a name="worst-state-policy"></a>最差狀態原則

匯總監視的狀態會與健全狀況狀態最差的子監視狀態相符。 這是匯總監視器最常使用的原則。

![匯總監視匯總最差狀態的範例](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>百分比原則

來源物件符合在最佳狀態中，目標物件的指定百分比之單一成員的最差狀態。 當目標物件的特定百分比必須是狀況良好，才能將目標物件視為狀況良好時，會使用此原則。 百分比原則會以狀態嚴重性的遞減順序來排序監視器，而匯總監視的狀態會計算為 N% 的最差狀態（N 是由設定參數*StateThresholdPercentage*所決定）。

例如，假設有五個容器映射的容器實例，其個別狀態為 [**重大**]、[**警告**] **、[狀況良好**]、[**狀況**良好]、[**狀況良好**]。  容器 CPU 使用率監視的狀態將會很**重要**，因為如果容器的90% 最差狀態是依嚴重性的遞減順序進行排序，這會是**很重要的**。

## <a name="understand-the-monitoring-configuration"></a>瞭解監視設定

適用于容器的 Azure 監視器包括一些主要的監視案例，其設定如下。

### <a name="unit-monitors"></a>單位監視

|**監視名稱** | 監視類型 | **描述** | **參數** | **ReplTest1** |
|-----------------|--------------|-----------------|---------------|-----------|
|節點記憶體使用率 |單位監視 |此監視會使用 cadvisor 報告的資料，每分鐘評估節點的記憶體使用量。 |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|節點 CPU 使用率 |單位監視 |此監視會使用 cadvisor 報告的資料，每分鐘檢查節點的 CPU 使用率。 | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|節點狀態 |單位監視 |此監視會檢查 Kubernetes 所報告的節點條件。<br> 目前已檢查下列節點條件：磁片壓力、記憶體壓力、PID 壓力、磁碟空間不足、網路無法使用、節點的就緒狀態。<br> 在上述情況下，如果*磁片*或*網路無法使用* **，則監視會變更**為**重大**狀態。<br> 如果有任何其他條件等於**true**，而不是**就緒**狀態，則監視器會變更為**警告**狀態。 | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|容器記憶體使用率 |單位監視 |此監視器會報告容器實例的記憶體使用量（RSS）的健全狀況狀態。<br> 它會執行簡單的比較，將每個樣本與單一閾值進行比較，並由設定參數**ConsecutiveSamplesForStateTransition**所指定。<br> 其狀態會計算為90% 容器（StateThresholdPercentage）實例的最差狀態，並以容器健全狀況狀態的嚴重性遞減順序排序（也就是「重大」、「警告」、「狀況良好」）。<br> 如果沒有從容器實例接收記錄，則容器實例的健全狀況狀態會回報為 [**未知**]，而且在 [**重大**] 狀態的排序次序中會有較高的優先順序。<br> 每個個別容器實例的狀態都是使用設定中指定的閾值來計算。 如果使用量超過重大臨界值（90%），則實例會處於**重大**狀態（如果低於重大臨界值（90%））但大於警告臨界值（80%），則實例會處於**警告**狀態。 否則，它會處於**狀況良好**的狀態。 |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|容器 CPU 使用率 |單位監視 |此監視器會回報容器實例之 CPU 使用率的結合健全狀況狀態。<br> 它會執行簡單的比較，將每個樣本與單一閾值進行比較，並由設定參數**ConsecutiveSamplesForStateTransition**所指定。<br> 其狀態會計算為90% 容器（StateThresholdPercentage）實例的最差狀態，並以容器健全狀況狀態的嚴重性遞減順序排序（也就是「重大」、「警告」、「狀況良好」）。<br> 如果沒有從容器實例接收記錄，則容器實例的健全狀況狀態會回報為 [**未知**]，而且在 [**重大**] 狀態的排序次序中會有較高的優先順序。<br> 每個個別容器實例的狀態都是使用設定中指定的閾值來計算。 如果使用量超過重大臨界值（90%），則實例會處於**重大**狀態（如果低於重大臨界值（90%））但大於警告臨界值（80%），則實例會處於**警告**狀態。 否則，它會處於**狀況良好**的狀態。 |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|系統工作負載 pod 就緒 |單位監視 |此監視會根據指定工作負載中處於 [就緒] 狀態的 pod 百分比來報告狀態。 如果小於100% 的 pod 處於**狀況良好**狀態，其狀態會設為 [**重大**] |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Kube API 狀態 |單位監視 |此監視會報告 Kube API 服務的狀態。 如果 Kube API 端點無法使用，則監視器處於重大狀態。 針對此特定監視，狀態是藉由對 kube-api 伺服器的「節點」端點進行查詢來決定。 「確定」回應碼以外的任何專案都會將監視器變更為「**重大**」狀態。 | 沒有設定屬性 |||

### <a name="aggregate-monitors"></a>匯總監視

|**監視名稱** | **描述** | **演算法** |
|-----------------|-----------------|---------------|
|節點 |此監視器是所有節點監視的匯總。 其符合健全狀況狀態最差的子系監視狀態：<br> 節點 CPU 使用率<br> 節點記憶體使用率<br> 節點狀態 | 最差|
|節點集區 |此監視會報告節點集區*agentpool*中所有節點的結合健全狀況狀態。 這是三個狀態監視器，其狀態是根據節點集區中節點80% 的最差狀態，以節點狀態的嚴重性遞減順序排序（也就是「重大」、「警告」、「狀況良好」）。|百分比 |
|節點（節點集區的父系） |這是所有節點集區的匯總監視。 其狀態是以其子監視的最差狀態為基礎（也就是存在於叢集中的節點集區）。 |最差 |
|Cluster （節點的父系/<br> Kubernetes 基礎結構） |這是符合健全狀況狀態最差之子監視狀態的父監視，也就是 kubernetes 基礎結構和節點。 |最差 |
|Kubernetes 基礎結構 |此監視會報告叢集之受管理基礎結構元件的健全狀況狀態組合。 其狀態會計算為其子監視狀態的「最差」，亦即 kube-系統工作負載和 API 伺服器狀態。 |最差|
|系統工作負載 |此監視會報告 kube 系統工作負載的健全狀況狀態。 此監視符合健全狀況狀態最差的子系監視狀態，也就是**處於就緒狀態**的 pod （監視和工作負載中的容器）。 |最差 |
|容器 |此監視會報告給定工作負載中容器的整體健全狀況狀態。 此監視符合健全狀況狀態最差的子監視狀態，也就是**CPU 使用率**和**記憶體使用率**監視器。 |最差 |

## <a name="next-steps"></a>後續步驟

查看[監視叢集健全狀況](container-insights-health.md)，以瞭解如何查看 Kubernetes 叢集的健全狀況狀態。
