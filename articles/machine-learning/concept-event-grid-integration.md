---
title: 取用 Azure Machine Learning 事件
titleSuffix: Azure Machine Learning
description: 在本文中，您將瞭解如何使用 Azure 事件方格來訂閱、回應和取消訂閱 Azure Machine Learning 所產生的事件。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139040"
---
# <a name="consume-azure-machine-learning-events-preview"></a>使用 Azure Machine Learning 事件（預覽）

Azure Machine Learning 管理機器學習程式的整個生命週期，包括模型定型、模型部署和監視。 Azure Machine Learning 事件可讓應用程式在機器學習週期期間回應事件，例如完成定型執行、註冊和部署模型，以及偵測資料漂移（使用新式無伺服器）映射. 

這些事件會透過[Azure 事件方格](https://azure.microsoft.com/services/event-grid/)發佈。 藉由使用 Azure 入口網站、Powershell 或 Azure CLI，客戶可以藉由[指定一或多個事件種類，以及篩選準則](/azure/event-grid/event-filtering)，輕鬆地訂閱事件。 客戶也可以選擇建立範圍廣泛的事件處理常式，例如 Azure Functions、Azure Logic Apps 或一般 webhook。 除了 Azure 事件方格外，Azure Machine Learning 還提供了高可用性、可靠且容錯的事件傳遞平臺，讓您建立事件驅動的應用程式。

如需搭配使用 Azure Machine Learning 與 Event Grid 的相關資訊，請參閱[建立事件驅動 Machine Learning 工作流程（預覽）](how-to-use-event-grid.md)。

## <a name="the-event-model"></a>事件模型 

Azure 事件方格會從來源讀取事件，例如 Azure Machine Learning 和其他 Azure 服務。 這些事件接著會傳送至事件處理常式，例如 Azure 事件中樞、Azure Functions、Logic Apps 和其他。 下圖顯示事件方格如何連接來源和處理常式，但不是完整的支援整合清單。

![Azure 事件格線運作模型](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

如需事件來源和事件處理常式的詳細資訊，請參閱[什麼是事件方格？](/azure/event-grid/overview)。

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning 事件種類

Azure Machine Learning 在機器學習服務生命週期的各個點中提供事件： 

| 事件類型 | 描述 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 在機器學習實驗執行完成時引發 |
| `Microsoft.MachineLearningServices.ModelRegistered` | 在工作區中註冊機器學習模型時引發 |
| `Microsoft.MachineLearningServices.ModelDeployed` | 當具有一或多個模型的推斷服務部署完成時引發 |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 當兩個資料集的資料漂移偵測作業完成時引發 |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 當執行狀態變更時引發，目前只有在執行狀態為「失敗」時才會引發 |

## <a name="subscribe-to-machine-learning-events"></a>訂閱 Machine Learning 事件

Azure Machine Learning 事件的訂閱受到角色型存取控制（RBAC）的保護。 只有工作區的[參與者或擁有](how-to-assign-roles.md#default-roles)者可以建立、更新和刪除事件訂閱。

事件訂閱可以根據各種條件進行篩選。 篩選可以在事件訂用帳戶的[建立](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)期間或[稍後](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)套用至事件訂用帳戶。 

### <a name="filter-by-event-type"></a>依事件類型進行篩選
事件訂用帳戶可以指定一或多個 Azure Machine Learning 事件種類。

### <a name="filter-by-event-subject"></a>依事件主旨篩選
Azure 事件方格支援以__開頭__和__結尾為__符合的主旨篩選器，以便將具有相符主體的事件傳遞給訂閱者。 不同的機器學習事件有不同的主體格式。

| 事件類型 | 主體格式 | 範例主旨 |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>進階篩選

Azure 事件方格也支援以已發佈的事件架構為基礎的先進篩選。 您可以在[適用于 Azure Machine Learning 的 Azure 事件方格事件架構](../event-grid/event-schema-machine-learning.md)中找到 Azure Machine Learning 事件架構詳細資料。

您可以執行的一些範例 advanced filterings 包括：

* 針對 `Microsoft.MachineLearningServices.ModelRegistered` 事件，篩選模型的標記值：

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

若要深入瞭解如何套用篩選器，請參閱[篩選事件方格的事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

## <a name="consume-machine-learning-events"></a>取用 Machine Learning 事件

處理 Machine Learning 事件的應用程式應該遵循幾個建議做法：

> [!div class="checklist"]
> * 因為有多個訂用帳戶可設定為將事件路由傳送至相同的事件處理常式，所以請務必假設事件來自特定來源，但要檢查訊息的主題，以確保其來自您預期的機器學習服務工作區。
> * 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
> * 由於訊息可能會在延遲一段時間後以錯誤順序送達，請使用 [etag] 欄位以了解您的物件資訊是否仍是最新狀態。  使用 [排序器] 欄位以了解任何特定物件上的事件順序。
> * 請忽略您不了解的欄位。 此做法將有助於保持未來可能新增功能的彈性。
> * [失敗] 或 [已取消] Azure Machine Learning 作業不會觸發事件。 例如，如果模型部署失敗，Microsoft.machinelearningservices 將不會觸發 ModelDeployed。 設計您的應用程式時，請考慮這種失敗模式。 您一律可以使用 Azure Machine Learning SDK、CLI 或入口網站來檢查操作的狀態，並瞭解詳細的失敗原因。

Azure 事件方格可讓客戶建立可由 Azure Machine Learning 事件觸發的解除耦合訊息處理常式。 一些值得注意的訊息處理常式範例包括：
* Azure Functions
* Azure Logic Apps
* Azure 事件中心
* Azure Data Factory 管線
* 可裝載于 Azure 平臺或其他位置的一般 webhook

## <a name="next-steps"></a>後續步驟

深入瞭解 Event Grid 並提供 Azure Machine Learning 的事件嘗試：

- [關於 Event Grid](../event-grid/overview.md)
- [適用于 Azure Machine Learning 的 Azure 事件方格事件架構](../event-grid/event-schema-machine-learning.md)
- [使用 Azure Machine Learning 建立事件驅動的工作流程](how-to-use-event-grid.md)
