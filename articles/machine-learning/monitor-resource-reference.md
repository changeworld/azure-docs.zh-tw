---
title: 監視 Azure Machine Learning 資料參考 |Microsoft Docs
description: 監視 Azure Machine Learning 的參考檔。 瞭解 Azure 監視器中收集和提供的資料 & 資源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/02/2020
ms.openlocfilehash: bab74a1db04bb557aa6604fe76c05ba3d087f8c0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540785"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>監視 Azure machine learning 資料參考

瞭解 Azure 監視器從 Azure Machine Learning 工作區收集的資料和資源。 如需收集和分析監視資料的詳細資訊，請參閱 [監視 Azure Machine Learning](monitor-azure-machine-learning.md) 。

## <a name="metrics"></a>計量

此區段會列出針對 Azure Machine Learning 收集的所有自動收集平臺計量。 這些計量的資源提供者是 [MachineLearningServices/工作區](../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces)。

**型號**

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 模型部署失敗 | 計數 | 失敗的模型部署數目。 |
| 已開始模型部署 | 計數 | 已啟動的模型部署數目。 |
| 模型部署成功 | 計數 | 成功的模型部署數目。 |
| 模型註冊失敗 | 計數 | 失敗的模型註冊數目。 |
| 模型註冊成功 | 計數 | 成功的模型註冊數目。 |

**配額**

配額資訊僅適用于 Azure Machine Learning 計算。

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 使用中核心 | 計數 | 使用中計算核心的數目。 |
| 現用節點 | 計數 | 使用中節點的數目。 |
| 閒置核心 | 計數 | 閒置計算核心的數目。 |
| 閒置節點 | 計數 | 閒置計算節點的數目。 |
| 離開核心 | 計數 | 離開核心的數目。 |
| 離開節點 | 計數 | 離開節點的數目。 |
| 搶先的核心 | 計數 | 搶先核心的數目。 |
| 先佔節點 | 計數 | 搶先節點數目。 |
| 配額使用率百分比 | 百分比 | 使用的配額百分比。 |
| 核心總數 | 計數 | 核心總數。 |
| 節點總計 | 計數 | 節點總數。 |
| 無法使用的核心 | 計數 | 無法使用的核心數目。 |
| 無法使用的節點 | 計數 | 無法使用的節點數目。 |

**Resource**

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| CpuUtilization | 百分比 | 在執行/作業期間，指定節點的 CPU 使用率百分比。 只有當工作正在節點上執行時，才會發佈此計量。 一個作業可能會使用一或多個節點。 此計量會依節點發布。 |
| GpuUtilization | 百分比 | 在執行/作業期間，指定節點的 GPU 使用量百分比。 一個節點可以有一或多個 Gpu。 每個節點的每個 GPU 都會發佈此計量。 |

**執行**

定型執行的資訊。

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 已完成執行 | 計數 | 已完成的執行數目。 |
| 失敗的執行 | 計數 | 失敗的執行數目。 |
| 開始執行 | 計數 | 已開始執行的數目。 |

## <a name="metric-dimensions"></a>度量維度

如需有關哪些計量維度的詳細資訊，請參閱 [多維度計量](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics)。

Azure Machine Learning 具有與其度量相關聯的下列維度。

| 尺寸 | 描述 |
| ---- | ---- |
| 叢集名稱 | 計算實例的名稱。 適用于所有配額度量。 |
| Vm 系列名稱 | 叢集所使用的 VM 系列名稱。 適用于配額使用率百分比。 |
| Vm 優先順序 | VM 的優先順序。 適用于配額使用率百分比。
| CreatedTime | 僅適用于 CpuUtilization 和 GpuUtilization。 |
| DeviceId |  (GPU) 的裝置識別碼。 僅適用于 GpuUtilization。 |
| NodeId | 工作執行時所建立的節點識別碼。 僅適用于 CpuUtilization 和 GpuUtilization。 |
| RunId | 執行/作業的識別碼。 僅適用于 CpuUtilization 和 GpuUtilization。 |
| ComputeType | 執行所使用的計算類型。 僅適用于已完成的執行、失敗的執行，並開始執行。 |
| PipelineStepType | 執行中所使用的 [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?preserve-view=true&view=azure-ml-py) 類型。 僅適用于已完成的執行、失敗的執行，並開始執行。 |
| PublishedPipelineId | 執行中所使用之已發佈管線的識別碼。 僅適用于已完成的執行、失敗的執行，並開始執行。 |
| RunType | 執行的類型。 僅適用于已完成的執行、失敗的執行，並開始執行。 |

RunType 維度的有效值為：

| 值 | 描述 |
| ----- | ----- |
| 實驗 | 非管線執行。 |
| Pipelinerun 來 | 管線執行，也就是 StepRun 的父系。 |
| StepRun | 管線步驟的執行。 |
| ReusedStepRun | 針對重複使用先前執行的管線步驟執行。 |

## <a name="activity-log"></a>活動記錄檔

下表列出可能會在活動記錄中建立之 Azure Machine Learning 的相關作業。

| 作業 | 描述 |
|:---|:---|
| 建立或更新 Machine Learning 工作區 | 已建立或更新工作區 |
| CheckComputeNameAvailability | 檢查計算名稱是否已在使用中 |
| 建立或更新計算資源 | 已建立或更新計算資源 |
| 刪除計算資源 | 已刪除計算資源 |
| 列出密碼 | 針對 Machine Learning 工作區在操作中列出秘密 |

## <a name="resource-logs"></a>資源記錄

此區段會列出您可以為 Azure Machine Learning 工作區收集的資源記錄類型。

資源提供者和類型： [Microsoft. MachineLearningServices/workspace](../azure-monitor/platform/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces)。

| 類別 | 顯示名稱 |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>結構描述

下列架構正在使用中 Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents 資料表

| 屬性 | 描述 |
|:--- |:---|
| TimeGenerated | 產生記錄專案的時間 |
| OperationName | 與記錄檔事件相關聯的作業名稱 |
| 類別 | 記錄檔事件的名稱，AmlComputeClusterNodeEvent |
| JobId | 提交的作業識別碼 |
| ExperimentId | 實驗的識別碼 |
| ExperimentName | 實驗的名稱 |
| CustomerSubscriptionId | 將實驗和作業提交至其中的 SubscriptionId |
| WorkspaceName | Machine learning 工作區的名稱 |
| ClusterName | 叢集的名稱 |
| ProvisioningState | 作業提交的狀態 |
| resourceGroupName | 資源群組的名稱 |
| JobName | 作業的名稱 |
| ClusterId | 叢集的識別碼 |
| EventType | 作業事件的類型。 例如，>jobsubmitted、JobRunning、JobFailed、JobSucceeded。 |
| ExecutionState | 工作 (執行) 的狀態。 例如，已排入佇列、執行中、成功、失敗 |
| ErrorDetails | 作業錯誤的詳細資料 |
| CreationApiVersion | 用來建立作業的 Api 版本 |
| .Clusterresourcegroupname | 叢集的資源組名 |
| TFWorkerCount | TF worker 的計數 |
| TFParameterServerCount | TF 參數伺服器的計數 |
| ToolType | 使用的工具類型 |
| RunInContainer | 描述是否應在容器內執行作業的旗標 |
| JobErrorMessage | 作業錯誤的詳細訊息 |
| NodeId | 工作執行時所建立的節點識別碼 |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents 資料表

| 屬性 | 描述 |
|:--- |:--- |
| TimeGenerated | 產生記錄專案的時間 |
| OperationName | 與記錄檔事件相關聯的作業名稱 |
| 類別 | 記錄檔事件的名稱，AmlComputeClusterNodeEvent |
| ProvisioningState | 叢集的布建狀態 |
| ClusterName | 叢集的名稱 |
| ClusterType | 叢集類型 |
| CreatedBy | 建立叢集的使用者 |
| CoreCount | 叢集中的核心計數 |
| VmSize | 叢集的 Vm 大小 |
| VmPriority | 在叢集專用/LowPriority 內建立之節點的優先順序 |
| ScalingType | 調整手動/自動的叢集類型 |
| InitialNodeCount | 叢集的初始節點計數 |
| MinimumNodeCount | 叢集的最小節點計數 |
| MaximumNodeCount | 叢集的節點計數上限 |
| NodeDeallocationOption | 節點應如何解除配置 |
| Publisher | 叢集類型的發行者 |
| 供應項目 | 用來建立叢集的供應專案 |
| SKU | 在叢集中建立之節點/VM 的 Sku |
| 版本 | 建立節點/VM 時使用的映射版本 |
| SubnetId | 叢集的 SubnetId |
| AllocationState | 叢集配置狀態 |
| CurrentNodeCount | 叢集目前的節點計數 |
| TargetNodeCount | 擴充/減少時的叢集目標節點計數 |
| EventType | 叢集建立期間的事件種類。 |
| NodeIdleTimeSecondsBeforeScaleDown | 叢集縮減之前的閒置時間（秒） |
| PreemptedNodeCount | 叢集的搶先節點計數 |
| IsResizeGrow | 指出叢集正在相應增加的旗標 |
| VmFamilyName | 可在叢集中建立之節點的 VM 系列名稱 |
| LeavingNodeCount | 離開叢集的節點計數 |
| UnusableNodeCount | 無法使用的叢集節點計數 |
| IdleNodeCount | 叢集的閒置節點計數 |
| RunningNodeCount | 正在執行叢集的節點計數 |
| PreparingNodeCount | 準備叢集的節點計數 |
| QuotaAllocated | 配置給叢集的配額 |
| QuotaUtilized | 使用的叢集配額 |
| AllocationStateTransitionTime | 從某個狀態到另一個狀態的轉換時間 |
| ClusterErrorCodes | 叢集建立或調整期間收到的錯誤碼 |
| CreationApiVersion | 建立叢集時使用的 Api 版本 |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents 資料表

| 屬性 | 描述 |
|:--- |:--- |
| TimeGenerated | 產生記錄專案的時間 |
| OperationName | 與記錄檔事件相關聯的作業名稱 |
| 類別 | 記錄檔事件的名稱，AmlComputeClusterNodeEvent |
| ClusterName | 叢集的名稱 |
| NodeId | 建立的叢集節點識別碼 |
| VmSize | 節點的 Vm 大小 |
| VmFamilyName | 節點所屬的 Vm 系列 |
| VmPriority | 節點建立的專用/LowPriority 優先順序 |
| Publisher | Vm 映射的發行者。 例如，microsoft dsvm |
| 供應項目 | 與 VM 建立相關聯的供應專案 |
| SKU | 已建立之節點/VM 的 Sku |
| 版本 | 建立節點/VM 時使用的映射版本 |
| ClusterCreationTime | 建立叢集的時間 |
| ResizeStartTime | 叢集擴大/減少啟動的時間 |
| ResizeEndTime | 叢集擴大/減少結束時間 |
| NodeAllocationTime | 節點的配置時間 |
| NodeBootTime | 節點啟動的時間 |
| StartTaskStartTime | 工作指派給節點並啟動的時間 |
| StartTaskEndTime | 指派給節點的工作結束時間 |
| TotalE2ETimeInSeconds | 總時間節點為作用中狀態 |


## <a name="see-also"></a>另請參閱

- 如需監視 Azure Machine Learning 的說明，請參閱 [監視 Azure Machine Learning](monitor-azure-machine-learning.md) 。
- 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
