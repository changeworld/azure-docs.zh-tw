---
title: 監視資料參考 |Microsoft Docs
titleSuffix: Azure Machine Learning
description: 瞭解為 Azure Machine Learning 收集的資料和資源，並可在 Azure 監視器中取得。 Azure 監視器會收集和呈現 Azure Machine Learning 工作區的相關資料，並可讓您查看計量、設定警示和分析已記錄的資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/27/2020
ms.openlocfilehash: e9a43f4a7da39869e002e2da9fb9638381e57cb8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856026"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure machine learning 監視資料參考

深入瞭解 Azure 監視器從 Azure Machine Learning 工作區收集的資料和資源。 如需收集和分析監視資料的詳細資訊，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md) 。

## <a name="resource-logs"></a>資源記錄

下表列出在 Azure 監視器記錄或 Azure 儲存體中收集 Azure Machine Learning 資源記錄檔時的屬性。

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents 資料表

| 屬性 | 描述 |
|:--- |:---|
| TimeGenerated | 產生記錄專案的時間 |
| OperationName | 與記錄事件相關聯的作業名稱 |
| 類別 | 記錄事件的名稱，AmlComputeClusterNodeEvent |
| JobId | 已提交作業的識別碼 |
| ExperimentId | 實驗的識別碼 |
| ExperimentName | 實驗的名稱 |
| CustomerSubscriptionId | 已提交實驗和作業的 SubscriptionId |
| WorkspaceName | 機器學習服務工作區的名稱 |
| ClusterName | 叢集的名稱 |
| ProvisioningState | 提交作業的狀態 |
| resourceGroupName | 資源群組的名稱 |
| JobName | 作業的名稱 |
| ClusterId | 叢集的識別碼 |
| EventType | 作業事件的類型，例如 JobSubmitted、JobRunning、JobFailed、JobSucceeded 等等。 |
| ExecutionState | 作業的狀態（執行），例如已排入佇列、執行中、成功、失敗 |
| ErrorDetails | 作業錯誤的詳細資料 |
| CreationApiVersion | 用來建立作業的 Api 版本 |
| ClusterResourceGroupName | 叢集的資源組名 |
| TFWorkerCount | TF worker 的計數 |
| TFParameterServerCount | TF 參數伺服器計數 |
| ToolType | 使用的工具類型 |
| RunInContainer | 描述是否應在容器內執行作業的旗標 |
| JobErrorMessage | 作業錯誤的詳細訊息 |
| NodeId | 工作執行所在之節點的識別碼 |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents 資料表

| 屬性 | 描述 |
|:--- |:--- |
| TimeGenerated | 產生記錄專案的時間 |
| OperationName | 與記錄事件相關聯的作業名稱 |
| 類別 | 記錄事件的名稱，AmlComputeClusterNodeEvent |
| ProvisioningState | 叢集的布建狀態 |
| ClusterName | 叢集的名稱 |
| ClusterType | 叢集類型 |
| CreatedBy | 建立叢集的使用者 |
| CoreCount | 叢集中的核心計數 |
| VmSize | 叢集的 Vm 大小 |
| VmPriority | 在叢集專用/LowPriority 內建立的節點優先順序 |
| ScalingType | 手動/自動調整的叢集類型 |
| InitialNodeCount | 叢集的初始節點計數 |
| MinimumNodeCount | 叢集的最小節點計數 |
| MaximumNodeCount | 叢集的節點計數上限 |
| NodeDeallocationOption | 節點應如何解除配置 |
| 發行者 | 叢集類型的發行者 |
| 產品 | 建立叢集所使用的供應專案 |
| SKU | 在叢集內建立的節點/VM Sku |
| 版本 | 建立節點/VM 時所使用的映射版本 |
| SubnetId | 叢集的 SubnetId |
| AllocationState | 叢集配置狀態 |
| CurrentNodeCount | 叢集的目前節點計數 |
| TargetNodeCount | 相應增加/減少時的叢集目標節點計數 |
| EventType | 叢集建立期間的事件種類。 |
| NodeIdleTimeSecondsBeforeScaleDown | 相應減少叢集之前的閒置時間（以秒為單位） |
| PreemptedNodeCount | 叢集的搶先節點計數 |
| IsResizeGrow | 表示叢集正在相應增加的旗標 |
| VmFamilyName | 可在叢集內建立之節點的 VM 系列名稱 |
| LeavingNodeCount | 離開叢集的節點計數 |
| UnusableNodeCount | 叢集的無法使用節點計數 |
| IdleNodeCount | 叢集的閒置節點計數 |
| RunningNodeCount | 正在執行叢集的節點計數 |
| PreparingNodeCount | 正在準備叢集的節點計數 |
| QuotaAllocated | 配置給叢集的配額 |
| QuotaUtilized | 叢集的使用量配額 |
| AllocationStateTransitionTime | 將時間從某個狀態轉換到另一個狀態 |
| ClusterErrorCodes | 叢集建立或調整期間收到的錯誤碼 |
| CreationApiVersion | 建立叢集時使用的 Api 版本 |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents 資料表

| 屬性 | 描述 |
|:--- |:--- |
| TimeGenerated | 產生記錄專案的時間 |
| OperationName | 與記錄事件相關聯的作業名稱 |
| 類別 | 記錄事件的名稱，AmlComputeClusterNodeEvent |
| ClusterName | 叢集的名稱 |
| NodeId | 建立的叢集節點識別碼 |
| VmSize | 節點的 Vm 大小 |
| VmFamilyName | 節點所屬的 Vm 系列 |
| VmPriority | 節點建立的專用/LowPriority 優先順序 |
| 發行者 | Vm 映射的發行者，例如 microsoft dsvm |
| 產品 | 與 VM 建立相關聯的供應專案 |
| SKU | 已建立的節點/VM Sku |
| 版本 | 建立節點/VM 時所使用的映射版本 |
| ClusterCreationTime | 建立叢集的時間 |
| ResizeStartTime | 啟動叢集相應增加/減少的時間 |
| ResizeEndTime | 叢集相應增加/減少結束的時間 |
| NodeAllocationTime | 配置節點的時間 |
| NodeBootTime | 節點啟動的時間 |
| StartTaskStartTime | 將工作指派給節點並啟動的時間 |
| StartTaskEndTime | 指派至節點的工作結束時間 |
| TotalE2ETimeInSeconds | 總時間節點為使用中狀態 |

### <a name="metrics"></a>計量

下表列出針對 Azure Machine Learning 所有度量所收集的平臺計量儲存在命名空間**Azure Machine Learning 工作區**中。

**模組**

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 模型部署失敗 | 計數 | 失敗的模型部署數目。 |
| 模型部署已開始 | 計數 | 已啟動的模型部署數目。 |
| 模型部署成功 | 計數 | 成功的模型部署數目。 |
| 模型註冊失敗 | 計數 | 失敗的模型註冊數目。 |
| 模型註冊成功 | 計數 | 成功的模型註冊數目。 |

**配額**

配額資訊僅適用于 Azure Machine Learning 計算。

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 現用核心 | 計數 | 使用中計算核心的數目。 |
| 現用節點 | 計數 | 使用中節點的數目。 |
| 閒置核心 | 計數 | 閒置計算核心的數目。 |
| 閒置節點 | 計數 | 閒置計算節點的數目。 |
| 離開核心 | 計數 | 離開核心的數目。 |
| 離開節點 | 計數 | 離開節點的數目。 |
| 搶先核心 | 計數 | 已佔用的核心數目。 |
| 搶先節點 | 計數 | 已搶先節點的數目。 |
| 配額使用量百分比 | 百分比 | 使用的配額百分比。 |
| 核心總數 | 計數 | 核心總數。 |
| 節點總計 | 計數 | 節點總數。 |
| 無法使用的核心 | 計數 | 無法使用的核心數目。 |
| 無法使用的節點 | 計數 | 無法使用的節點數目。 |

以下是可以用來篩選配額計量的維度：

| 維度 | 可用的度量 | 描述 |
| ---- | ---- | ---- |
| 叢集名稱 | 所有配額標準 | 計算實例的名稱。 |
| Vm 系列名稱 | 配額使用量百分比 | 叢集所使用的 VM 系列名稱。 |
| Vm 優先順序 | 配額使用量百分比 | VM 的優先順序。

**Resource**

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| CpuUtilization | 百分比 | 在執行/作業期間，針對指定節點使用多少 CPU 百分比。 只有在節點上執行工作時，才會發佈此計量。 一個作業可以使用一或多個節點。 此計量會針對每個節點發布。 |
| GpuUtilization | 百分比 | 在執行/作業期間，指定節點使用了多少 GPU 百分比。 一個節點可以有一或多個 Gpu。 此計量會每個節點每個 GPU 發佈一次。 |

以下是可以用來篩選資源計量的維度：

| 維度 | 描述 |
| ----- | ----- |
| CreatedTime | |
| DeviceId | 裝置的識別碼（GPU）。 僅適用于 GpuUtilization。 |
| NodeId | 工作執行所在之節點的識別碼。 |
| RunId | 執行/作業的識別碼。 |

**進行**

訓練執行的資訊。

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 已完成的執行 | 計數 | 已完成的執行數目。 |
| 失敗的執行 | 計數 | 失敗的執行數目。 |
| 開始執行 | 計數 | 已啟動的執行數目。 |

以下是可以用來篩選執行計量的維度：

| 維度 | 描述 |
| ---- | ---- |
| ComputeType | 執行所使用的計算類型。 |
| PipelineStepType | 執行時所使用的[PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py)類型。 |
| PublishedPipelineId | 執行時所使用之已發行管線的識別碼。 |
| RunType | 執行的類型。 |

RunType 維度的有效值如下：

| 值 | 描述 |
| ----- | ----- |
| 實驗 | 非管線執行。 |
| 來擷取 | 管線執行，這是 StepRun 的父系。 |
| StepRun | 管線步驟的執行。 |
| ReusedStepRun | 管線步驟的執行，會重複使用先前執行的。 |

## <a name="see-also"></a>另請參閱

- 如需監視 Azure Machine Learning 的說明，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md) 。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器監視 azure 資源](/azure/azure-monitor/insights/monitor-azure-resource)。
