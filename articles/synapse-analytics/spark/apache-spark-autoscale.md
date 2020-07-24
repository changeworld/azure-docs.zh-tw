---
title: 自動調整 Apache Spark 實例
description: 使用 Azure Synapse 自動調整功能來自動調整 Apache Spark 執行個體
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: 7c6278d24e52159424c3e346b2382c93f4ab8160
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075797"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>自動調整 Azure Synapse Analytics Apache Spark 集區

適用於 Azure Synapse Analytics 的 Apache Spark 集區自動調整功能會自動擴大和縮小叢集執行個體中的節點數目。 在新建適用於 Azure Synapse Analytics 的 Apache Spark 集區期間，只要選取 [自動調整]，您就可以設定最小和最大節點數目。 接著自動調整就會監視負載的資源需求，然後擴大或縮小節點數目。 這項功能不需額外付費。

## <a name="metrics-monitoring"></a>計量監視

自動調整會持續監視 Spark 執行個體，並收集下列計量：

|計量|描述|
|---|---|
|擱置中的 CPU 總計|開始執行所有擱置中節點所需的核心總數。|
|擱置中的記憶體總計|開始執行所有擱置中節點所需的記憶體總計 (MB)。|
|可用的 CPU 總計|作用中節點上所有未使用核心的總和。|
|可用的記憶體總計|作用中節點上所有未使用記憶體的總和 (MB)。|
|每個節點的已使用記憶體|節點上的負載。 已使用 10 GB 記憶體的節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。|

系統每隔 30 秒就會檢查上述計量。 自動調整將依據這些計量決定要進行擴大或縮小。

## <a name="load-based-scale-conditions"></a>以負載為基礎的調整條件

當偵測到下列情況時，自動調整將會發出調整要求：

|相應增加|縮小|
|---|---|
|擱置中的 CPU 總計大於可用的 CPU 總計超過 1 分鐘。|擱置中的 CPU 總計小於可用的 CPU 總計超過 2 分鐘。|
|擱置中的記憶體總計大於可用的記憶體總計超過 1 分鐘。|擱置中的記憶體總計小於可用的記憶體總計超過 2 分鐘。|

若要進行擴大，Azure Synapse 自動調整服務會計算需要多少個新節點，才能符合目前的 CPU 和記憶體需求，然後發出擴大要求來新增所需的節點數目。

若要進行縮小，自動調整會依據執行程式的數目、每個節點的應用程式主機，以及目前的 CPU 和記憶體需求，來發出移除特定節點數目的要求。 服務也會根據目前的作業執行狀況，偵測哪些節點是移除的候選項目。 縮小作業會先會將節點解除委任，再將其從叢集中移除。

## <a name="get-started"></a>開始使用

### <a name="create-a-spark-pool-with-autoscaling"></a>建立具有自動調整功能的 Spark 集區

若要啟用自動調整功能，請在一般集區的建立過程中完成下列步驟：

1. 在 [基本] 索引標籤上，選取 [啟用自動調整] 核取方塊。
1. 針對下列屬性輸入所需的值：  

    * **最小**節點數目。
    * **最大**節點數目。

節點的初始數目會是最小值。 此值會定義叢集建立時的初始大小。 最小節點數目不能小於三個。

## <a name="best-practices"></a>最佳作法

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考量擴大或縮小作業的延遲

完成調整作業可能需要 1 到 5 分鐘的時間。

### <a name="preparation-for-scaling-down"></a>縮小的準備

在縮小執行個體的期間，自動調整會使節點處於解除委任狀態，如此該節點上就不會啟動任何新的執行程式。

執行中的作業將會繼續執行並完成。 暫止的作業將像平常一樣等候排程，但可用節點會變少。

## <a name="next-steps"></a>後續步驟

設定新 Spark 集區的快速入門：[建立 Spark 集區](../quickstart-create-apache-spark-pool-portal.md)
