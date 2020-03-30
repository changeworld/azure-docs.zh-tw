---
title: 使用 Azure 機器學習事件
titleSuffix: Azure Machine Learning
description: 在本文中，瞭解如何使用 Azure 事件網格訂閱、回應和取消訂閱 Azure 機器學習生成的事件。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139040"
---
# <a name="consume-azure-machine-learning-events-preview"></a>使用 Azure 機器學習事件（預覽）

Azure 機器學習管理機器學習過程的整個生命週期，包括模型培訓、模型部署和監視。 Azure 機器學習事件允許應用程式通過使用現代無伺服器功能對機器學習生命週期中的事件做出反應，例如完成培訓運行、模型註冊和部署以及檢測資料漂移架構。 

這些事件通過 Azure[事件網格](https://azure.microsoft.com/services/event-grid/)發佈。 使用 Azure 門戶、Powershell 或 Azure CLI，客戶可以通過[指定一個或多個事件種類和篩選準則](/azure/event-grid/event-filtering)來輕鬆訂閱事件。 客戶還可以選擇構建各種事件處理常式，如 Azure 函數、Azure 邏輯應用或通用 Webhook。 Azure 機器學習以及 Azure 事件網格為構建事件驅動應用程式提供了高可用性、可靠且容錯的事件傳遞平臺。

有關將 Azure 機器學習與事件網格結合使用的資訊，請參閱[創建事件驅動的機器學習工作流（預覽）。](how-to-use-event-grid.md)

## <a name="the-event-model"></a>事件模型 

Azure 事件網格從源（如 Azure 機器學習和其他 Azure 服務）讀取事件。 然後，這些事件將發送到事件處理常式，如 Azure 事件中心、Azure 函數、邏輯應用和其他。 下圖顯示了事件網格如何連接源和處理常式，但不是支援集成的完整清單。

![Azure 事件格線運作模型](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

有關事件源和事件處理常式的詳細資訊，請參閱[什麼是事件網格？](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Azure 機器學習事件種類

Azure 機器學習在機器學習生命週期的各個點提供事件： 

| 事件類型 | 描述 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 機器學習實驗運行時引發 |
| `Microsoft.MachineLearningServices.ModelRegistered` | 在工作區中註冊機器學習模型時引發 |
| `Microsoft.MachineLearningServices.ModelDeployed` | 完成具有一個或多個模型的推理服務部署時引發 |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 完成兩個資料集的資料漂移檢測作業時引發 |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 運行時更改運行狀態時引發，當前僅在運行狀態為"失敗"時引發 |

## <a name="subscribe-to-machine-learning-events"></a>訂閱機器學習活動

Azure 機器學習事件的訂閱受基於角色的存取控制 （RBAC） 的保護。 只有[工作區的參與者或擁有者](how-to-assign-roles.md#default-roles)才能創建、更新和刪除事件訂閱。

可以根據各種條件篩選事件訂閱。 篩選可以在事件訂用帳戶的[建立](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)期間或[稍後](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)套用至事件訂用帳戶。 

### <a name="filter-by-event-type"></a>依事件類型進行篩選
事件訂閱可以指定一個或多個 Azure 機器學習事件種類。

### <a name="filter-by-event-subject"></a>按事件主題篩選
Azure 事件網格支援基於__開頭__和__以匹配結尾__的主題篩選器，以便將具有匹配主題的事件傳遞到訂閱者。 不同的機器學習事件具有不同的主題格式。

| 事件類型 | 主題格式 | 示例主題 |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>進階篩選

Azure 事件網格還支援基於已發佈事件架構的高級篩選。 Azure 機器學習事件架構詳細資訊可在 Azure[機器學習的 Azure 事件網格事件架構](../event-grid/event-schema-machine-learning.md)中找到。

您可以執行的一些高級高級篩選示例包括：

* 對於`Microsoft.MachineLearningServices.ModelRegistered`事件，要篩選模型的標記值：

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

要瞭解有關如何應用篩選器的更多內容，請參閱[事件網格的篩選器事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

## <a name="consume-machine-learning-events"></a>使用機器學習事件

處理機器學習事件的應用程式應遵循一些建議的做法：

> [!div class="checklist"]
> * 由於可以將多個訂閱配置為將事件路由到同一事件處理常式，因此不要假定事件來自特定源，而是檢查消息的主題以確保它來自您期望的機器學習工作區。"
> * 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
> * 由於訊息可能會在延遲一段時間後以錯誤順序送達，請使用 [etag] 欄位以了解您的物件資訊是否仍是最新狀態。  使用 [排序器] 欄位以了解任何特定物件上的事件順序。
> * 請忽略您不了解的欄位。 此做法將有助於保持未來可能新增功能的彈性。
> * 失敗的或取消的 Azure 機器學習操作不會觸發事件。 例如，如果模型部署失敗 Microsoft.機器學習服務.模型部署將不會觸發。 在設計應用程式時，請考慮這種故障模式。 您可以隨時使用 Azure 機器學習 SDK、CLI 或閘戶來檢查操作的狀態並瞭解詳細的故障原因。

Azure 事件網格允許客戶生成取消耦合的訊息處理常式，這些處理常式可以由 Azure 機器學習事件觸發。 訊息處理常式的一些值得注意的示例包括：
* Azure Functions
* Azure Logic Apps
* Azure 事件中心
* Azure 資料工廠管道
* 通用 Webhook，可託管在 Azure 平臺或其他地方

## <a name="next-steps"></a>後續步驟

瞭解有關事件網格的更多資訊，並嘗試 Azure 機器學習事件：

- [關於 Event Grid](../event-grid/overview.md)
- [Azure 事件網格事件架構，用於 Azure 機器學習](../event-grid/event-schema-machine-learning.md)
- [使用 Azure 機器學習創建事件驅動的工作流](how-to-use-event-grid.md)
