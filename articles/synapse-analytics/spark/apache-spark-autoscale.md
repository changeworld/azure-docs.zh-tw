---
title: 自動調整 Azure Synapse Apache Spark 實例
description: 使用 Azure Synapse 自動調整功能自動調整 Apache Spark 實例
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: a2f907384326aa887c12c293feb8c988f42bbaf1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210510"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>自動調整 Azure Synapse Analytics Apache Spark 集區

Azure Synapse Spark 集區的自動調整功能會自動相應增加和減少叢集實例中的節點數目。 在建立新的 Azure Synapse Spark 集區期間，選取 [自動調整] 時，可以設定最小和最大節點數目。 自動調整會監視負載的資源需求，並相應增加或減少節點數目。 這項功能不需額外付費。

## <a name="metrics-monitoring"></a>計量監視

自動調整會持續監視 Spark 實例，並收集下列計量：

|計量|說明|
|---|---|
|擱置中的 CPU 總計|開始執行所有暫止節點時所需的核心總數。|
|擱置中的記憶體總計|開始執行所有暫止節點所需的總記憶體（以 MB 為單位）。|
|可用 CPU 總計|作用中節點上所有未使用核心的總和。|
|可用記憶體總計|作用中節點上未使用的記憶體總和（以 MB 為單位）。|
|每個節點的已使用記憶體|節點上的負載。 使用 10 GB 記憶體的節點，會被視為低於具有 2 GB 已使用記憶體的背景工作負載。|

上述計量會每隔30秒檢查一次。 自動調整會根據這些計量來進行相應增加和相應減少決策。

## <a name="load-based-scale-conditions"></a>以負載為基礎的調整規模條件

當偵測到下列情況時，自動調整會發出級別要求：

|相應增加|向下調整|
|---|---|
|擱置中的 CPU 總計大於可用的 CPU 總計超過 1 分鐘。|擱置中的 CPU 總計小於2分鐘的可用 CPU 總數。|
|擱置中的記憶體總計大於可用的記憶體總計超過 1 分鐘。|暫止的記憶體總計小於可用的記憶體總計超過2分鐘。|

針對相應增加，Azure Synapse 自動調整服務會計算需要多少個新節點，以符合目前的 CPU 和記憶體需求，然後發出相應增加要求來新增所需的節點數目。

根據執行次數、每個節點的應用程式主機，以及目前的 CPU 和記憶體需求進行相應減少，自動調整會發出移除特定節點數目的要求。 服務也會根據目前的作業執行，偵測哪些節點是候選項目。 相應減少作業會先會 add-on 節點，然後將它們從叢集中移除。

## <a name="get-started"></a>開始使用

### <a name="create-a-spark-pool-with-autoscaling"></a>建立具有自動調整的 Spark 集區

若要啟用自動調整功能，請在一般集區建立程式中完成下列步驟：

1. 在 [**基本**] 索引標籤上，選取 [**啟用自動**調整] 核取方塊。
1. 針對下列屬性輸入所需的值：  

    * 節點數目的**最小值**。
    * 節點的**最大**數目。

節點的初始數目會是最小值。 這個值會定義實例建立時的初始大小。 節點數目下限不能小於三個。

## <a name="best-practices"></a>最佳作法

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考慮相應增加或相應減少作業的延遲

調整作業可能需要1到5分鐘的時間才能完成。

### <a name="preparation-for-scaling-down"></a>向下調整的準備

在實例相應減少進程期間，自動調整會使節點處於解除委任狀態，如此就不會在該節點上啟動任何新的執行程式。

執行中的作業將會繼續執行並完成。 暫止的工作將等候排程為正常，且有較少的可用節點。

## <a name="next-steps"></a>後續步驟

設定新 Spark 集區的快速入門[建立 spark 集](..\quickstart-create-apache-spark-pool.md)區
