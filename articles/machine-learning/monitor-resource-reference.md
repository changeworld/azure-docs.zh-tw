---
title: 監控資料引用 |微軟文檔
titleSuffix: Azure Machine Learning
description: 瞭解為 Azure 機器學習收集的資料和資源，並在 Azure 監視器中提供。 Azure 監視器收集和顯示有關 Azure 機器學習工作區的資料，並允許您查看指標、設置警報和分析記錄的資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927555"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure 機器學習監視資料引用

瞭解 Azure 監視器從 Azure 機器學習工作區收集的資料和資源。 有關收集和分析監視資料的詳細資訊，請參閱[監視 Azure 機器學習](monitor-azure-machine-learning.md)。

## <a name="resource-logs"></a>資源記錄

下表列出了在 Azure 監視器日誌或 Azure 存儲中收集 Azure 機器學習資源日誌的屬性。

### <a name="amlcomputejobevents-table"></a>Aml 計算作業事件表

| 屬性 | 描述 |
|:--- |:---|
| TimeGenerated | 生成日誌條目的時間 |
| OperationName | 與日誌事件關聯的操作的名稱 |
| 類別 | 日誌事件的名稱，Aml 計算叢集節點事件 |
| JobId | 提交的作業 ID |
| 實驗Id | 實驗的 ID |
| 實驗名稱 | 實驗名稱 |
| 客戶訂閱 ID | 提交實驗和作業的訂閱 Id |
| 工作區名稱 | 機器學習工作區的名稱 |
| ClusterName | 群集的名稱 |
| ProvisioningState | 作業提交狀態 |
| resourceGroupName | 資源組的名稱 |
| JobName | 作業名稱 |
| ClusterId | 群集的 ID |
| EventType | 作業事件的類型，例如作業提交、作業運行、作業失敗、作業成功等。 |
| ExecutionState | 作業的狀態（運行），例如，排隊、運行、成功、失敗 |
| ErrorDetails | 作業錯誤的詳細資訊 |
| 創建 Apiversion | 用於創建作業的 Api 版本 |
| 群集資源組名稱 | 群集的資源組名稱 |
| TFWorkerCount | TF 工作人員計數 |
| TF參數伺服器計數 | TF 參數伺服器計數 |
| 工具類型 | 使用的工具類型 |
| 運行容器 | 描述是否應在容器內運行作業的標誌 |
| 作業錯誤訊息 | 作業錯誤的詳細消息 |
| NodeId | 作業運行位置創建的節點的 ID |

### <a name="amlcomputeclusterevents-table"></a>Aml 計算群集事件表

| 屬性 | 描述 |
|:--- |:--- |
| TimeGenerated | 生成日誌條目的時間 |
| OperationName | 與日誌事件關聯的操作的名稱 |
| 類別 | 日誌事件的名稱，Aml 計算叢集節點事件 |
| ProvisioningState | 群集的預配狀態 |
| ClusterName | 群集的名稱 |
| 群集類型 | 群集的類型 |
| CreatedBy | 創建群集的使用者 |
| CoreCount | 群集中的內核計數 |
| VmSize | 群集的 Vm 大小 |
| Vm 優先 | 在群集專用/低優先順序內創建的節點的優先順序 |
| 縮放類型 | 群集縮放手動/自動類型 |
| 初始節點計數 | 群集的初始節點計數 |
| 最小節點計數 | 群集的最小節點計數 |
| 最大節點計數 | 群集的最大節點計數 |
| 節點交易定位選項 | 節點應如何處理 |
| 發行者 | 群集類型的發行者 |
| 供應項目 | 使用群集創建群集的要約 |
| SKU | 在群集中創建的節點/VM 的 Sku |
| 版本 | 創建節點/VM 時使用的圖像版本 |
| 子網Id | 群集的子網Id |
| 分配狀態 | 群集分配狀態 |
| 當前節點計數 | 群集的當前節點計數 |
| 目標節點計數 | 向上/向下擴展時目標節點計數 |
| EventType | 群集創建期間的事件種類。 |
| 節點閒置時間秒在縮小之前 | 縮減群集前的閒置時間（以秒為單位） |
| PreemptedNodeCount | 群集的搶佔節點計數 |
| 正在調整增長 | 指示群集正在向上擴展的標誌 |
| Vm 系列名稱 | 可在群集內創建的節點的 VM 系列的名稱 |
| 離開節點計數 | 離開群集的節點計數 |
| UnusableNodeCount | 群集的不可用節點計數 |
| IdleNodeCount | 群集的空閒節點計數 |
| RunningNodeCount | 群集的運行節點計數 |
| 準備節點計數 | 準備群集的節點計數 |
| 配額分配 | 為群集分配的配額 |
| 配額使用 | 群集的已用配額 |
| 分配狀態轉換時間 | 從一種狀態轉換到另一種狀態 |
| 群集錯誤代碼 | 群集創建或縮放期間收到的錯誤代碼 |
| 創建 Apiversion | 創建群集時使用的 Api 版本 |

### <a name="amlcomputeclusternodeevents-table"></a>Aml 計算叢集節點事件表

| 屬性 | 描述 |
|:--- |:--- |
| TimeGenerated | 生成日誌條目的時間 |
| OperationName | 與日誌事件關聯的操作的名稱 |
| 類別 | 日誌事件的名稱，Aml 計算叢集節點事件 |
| ClusterName | 群集的名稱 |
| NodeId | 創建的叢集節點的 ID |
| VmSize | 節點的 Vm 大小 |
| Vm 系列名稱 | 節點所屬的 Vm 系列 |
| Vm 優先 | 創建專用/低優先順序的節點的優先順序 |
| 發行者 | vm 映射的發行者，例如，微軟-dsvm |
| 供應項目 | 與 VM 創建關聯的產品/服務 |
| SKU | 創建的節點/VM 的 SKU |
| 版本 | 創建節點/VM 時使用的圖像版本 |
| 群集創建時間 | 創建群集的時間 |
| 調整開始時間 | 群集向上/向下擴展的時間開始 |
| 調整結束時間的大小 | 群集向上/向下擴展的時間結束 |
| 節點分配時間 | 分配節點的時間 |
| 節點引導時間 | 啟動節點的時間 |
| 開始任務開始時間 | 任務分配到節點並啟動的時間 |
| 開始任務結束時間 | 分配給節點的任務結束的時間 |
| 總時間以秒為單位 | 總時間節點處於活動狀態 |

### <a name="metrics"></a>計量

下表列出了為 Azure 機器學習收集的平臺指標 所有指標都存儲在命名空間**Azure 機器學習工作區**中。

**模型**

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 模型部署失敗 | Count | 失敗的模型部署數。 |
| 模型部署已啟動 | Count | 啟動的模型部署數。 |
| 模型部署成功 | Count | 成功的模型部署數。 |
| 模型寄存器失敗 | Count | 失敗的模型註冊數。 |
| 模型註冊成功 | Count | 成功的模型註冊數。 |

**配額**

配額資訊僅適用于 Azure 機器學習計算。

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 活動內核 | Count | 活動計算核心的數量。 |
| 活動節點 | Count | 活動節點數。 |
| 空閒內核 | Count | 空閒計算內核數。 |
| 空閒節點 | Count | 空閒計算節點的數量。 |
| 離開核心 | Count | 離開內核的數量。 |
| 離開節點 | Count | 離開節點的數量。 |
| 搶佔型內核 | Count | 搶佔的內核數。 |
| 搶佔節點 | Count | 搶佔的節點數。 |
| 配額利用率百分比 | 百分比 | 使用的配額百分比。 |
| 核心總數 | Count | 總內核。 |
| 節點總數 | Count | 總節點。 |
| 不可用的內核 | Count | 不可用內核的數量。 |
| 無法使用的節點 | Count | 不可用節點的數量。 |

以下是可用於篩選配額指標的維度：

| 維度 | 提供指標 | 描述 |
| ---- | ---- | ---- |
| 叢集名稱 | 所有配額指標 | 計算實例的名稱。 |
| Vm 姓氏 | 配額利用率百分比 | 群集使用的 VM 系列的名稱。 |
| Vm 優先順序 | 配額利用率百分比 | VM 的優先順序。

**運行**

有關培訓運行的資訊。

| 計量 | 單位 | 描述 |
| ----- | ----- | ----- |
| 已完成的運行 | Count | 已完成的運行數。 |
| 運行失敗 | Count | 失敗的運行數。 |
| 已啟動運行 | Count | 已啟動運行數。 |

以下是可用於篩選運行指標的維度：

| 維度 | 描述 |
| ---- | ---- |
| 計算類型 | 運行中使用的計算類型。 |
| 管道步進類型 | 運行中使用的[管道步驟](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py)的類型。 |
| 已發佈管道Id | 運行中使用的已發佈管道的 ID。 |
| 運行類型 | 運行的類型。 |

RunType 維度的有效值為：

| 值 | 描述 |
| ----- | ----- |
| 實驗 | 非管道運行。 |
| 管道運行 | 管道運行，它是 StepRun 的父級。 |
| 步進運行 | 管道步驟的運行。 |
| 重複使用的步進運行 | 重用上一個運行的管道步驟的運行。 |

## <a name="see-also"></a>另請參閱

- 有關[監視](monitor-azure-machine-learning.md)Azure 機器學習的說明，請參閱監視 Azure 機器學習。
- 有關監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器監視 Azure 資源](/azure/azure-monitor/insights/monitor-azure-resource)。
