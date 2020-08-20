---
title: '在 ML 工作流程中觸發事件 (預覽) '
titleSuffix: Azure Machine Learning
description: 在 Azure Machine Learning 中設定事件驅動的應用程式、程序或 CI/CD 機器學習工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 464d945708fba83877fe6cef9ec1b64ec444bd95
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650412"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>根據 Azure Machine Learning 事件來觸發應用程式、進程或 CI/CD 工作流程 (preview) 

在本文中，您將瞭解如何在 [Azure 事件方格](https://docs.microsoft.com/azure/event-grid/)偵測到特定條件時，根據 Azure Machine Learning 事件 (例如失敗通知電子郵件、ML 管線執行) 來設定事件驅動的應用程式、程序或 CI/CD 工作流程。

Azure Machine Learning 可管理機器學習程序的整個生命週期，包括模型定型、模型部署和監視。 使用新式無伺服器架構，您可以利用事件方格來回應 Azure Machine Learning 事件，例如定型執行完成、模型的註冊和部署、偵測到資料漂移等。 接著，您可以在工作區中訂閱和使用事件，例如執行狀態變更、執行完成、模型註冊、模型部署、資料漂移偵測。

何時將事件方格用於事件導向動作：
* 在執行失敗和執行完成時傳送電子郵件
* 在註冊模型之後使用 Azure 函式
* 將來自 Azure Machine Learning 的事件串流至各種端點
* 偵測到漂移時觸發 ML 管線

> [!NOTE] 
> 目前，只有當執行狀態為**失敗**時，才會觸發 runStatusChanged 事件

## <a name="prerequisites"></a>Prerequisites
若要使用「事件方格」，您需要參與者或擁有者存取權，才能使用您要建立事件的 Azure Machine Learning 工作區。

## <a name="the-event-model--types"></a>事件模型與類型

Azure 事件方格會從來源 (例如 Azure Machine Learning 和其他 Azure 服務) 讀取事件。 這些事件接著會傳送至事件處理常式，例如 Azure 事件中樞、Azure Functions、Logic Apps 等等。 下圖顯示事件方格如何連接來源和處理常式，但這並非完整的支援整合清單。

![Azure 事件格線運作模型](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

如需事件來源和事件處理常式的詳細資訊，請參閱 [什麼是事件方格？](/azure/event-grid/overview)。

### <a name="event-types-for-azure-machine-learning"></a>Azure Machine Learning 的事件類型

Azure Machine Learning 在機器學習服務生命週期的各個點中提供事件： 

| 事件類型 | 描述 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 在機器學習實驗執行完成時引發 |
| `Microsoft.MachineLearningServices.ModelRegistered` | 在工作區中註冊機器學習模型時引發 |
| `Microsoft.MachineLearningServices.ModelDeployed` | 當具有一或多個模型的推斷服務部署完成時引發 |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 當兩個資料集的資料漂移偵測作業完成時引發 |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 當執行狀態變更時引發，目前只有在執行狀態為「失敗」時才會引發 |

### <a name="filter--subscribe-to-events"></a>篩選及訂閱事件

這些事件會透過 Azure 事件方格發佈。 使用 Azure 入口網站、PowerShell 或 Azure CLI，客戶可以[指定一或多個事件種類以及的篩選條件](/azure/event-grid/event-filtering)，輕鬆地訂閱事件。 

設定事件時，您可以只將篩選條件套用至特定事件資料的觸發程序。 在下列範例中，針對執行狀態已變更的事件，您可以依執行類型進行篩選。 只有條件符合時，才會觸發事件。 請參閱 [Azure Machine Learning 事件方格架構](/azure/event-grid/event-schema-machine-learning)，以瞭解您可以篩選的事件資料。 

Azure Machine Learning 事件的訂閱受到角色型存取控制 (RBAC) 的保護。 只有工作區的[參與者和擁有者](how-to-assign-roles.md#default-roles)可以建立、更新、刪除事件訂閱。  可以在事件訂閱的[建立](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)期間或之後，將篩選套用至事件訂閱。 


1. 移至 Azure 入口網站，選取新的訂閱或現有的訂閱。 

1. 選取 [篩選] 索引標籤，向下捲動至 [進階篩選]。 請在 [金鑰] 和 [值] 提供您想要篩選的屬性類型。 在這裡，您可以看到只有在執行類型為管線執行或管線步驟執行時，才會觸發事件。  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="篩選事件":::


+ **依事件類型篩選：** 事件訂閱可以指定一或多個 Azure Machine Learning 事件類型。

+ **依事件主旨篩選：** Azure 事件方格支援的主旨篩選可根據 [開頭為] 和 [結尾為] 的相符來進行篩選，將具有相符主旨的事件傳遞給訂閱者。 不同的機器學習事件有不同的主旨格式。

  | 事件類型 | 主旨格式 | 範例主旨 |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **進階篩選**：Azure 事件方格也支援依據已發佈的事件架構進行進階篩選。 您可以在 [Azure Machine Learning 的 Azure 事件方格事件架構](../event-grid/event-schema-machine-learning.md)中找到 Azure Machine Learning 事件架構的詳細資訊。  以下是執行進階篩選的其中一些範例：

  針對 `Microsoft.MachineLearningServices.ModelRegistered` 事件，篩選模型的標記值：

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  若要深入瞭解如何套用篩選，請參閱[事件方格的篩選事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

## <a name="consume-machine-learning-events"></a>取用 Machine Learning 事件

處理 Machine Learning 事件的應用程式，應該遵循幾個建議做法：

> [!div class="checklist"]
> * 由於可設定多個訂用帳戶以將事件路由至相同的事件處理常式，因此重要的是，不要假設事件來自於特定來源，而要檢查訊息主題以確定其來自預期的機器學習工作區。
> * 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
> * 由於訊息可能會在延遲一段時間後以錯誤順序送達，請使用 [etag] 欄位以了解您的物件資訊是否仍是最新狀態。  使用 [排序器] 欄位以了解任何特定物件上的事件順序。
> * 請忽略您不了解的欄位。 此做法將有助於保持未來可能新增功能的彈性。
> * 失敗或已取消的 Azure Machine Learning 作業不會觸發事件。 例如，如果模型部署失敗，就不會觸發 Microsoft.MachineLearningServices.ModelDeployed。 設計您的應用程式時，請考慮這種失敗模式。 您一律可以使用 Azure Machine Learning SDK、CLI 或入口網站來查看作業的狀態，及瞭解詳細的失敗原因。

Azure 事件方格可讓客戶建立可由 Azure Machine Learning 事件觸發的分離訊息處理常式。 一些值得注意的訊息處理常式範例包括：
* Azure Functions
* Azure Logic Apps
* Azure 事件中心
* Azure Data Factory 管線
* 可裝載於 Azure 平台或其他位置的一般 Webhook

## <a name="set-up-in-azure-portal"></a>在 Azure 入口網站中設定

1. 開啟 [Azure 入口網站](https://portal.azure.com)，移至您的 Azure Machine Learning 工作區。

1. 從左側欄中選取 [事件]，然後選取 [事件訂閱]。 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. 選取要使用的事件類型。 例如，下列螢幕擷取畫面中已選取 [已註冊的模型]、[已部署的模型]、[執行已完成]、[偵測到資料集漂移]：

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 選取要發佈事件至什麼端點。 在下列螢幕擷取畫面中，選取的端點是 [事件中樞] ：

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

確認您的選擇之後，按一下 [建立]。 設定之後，這些事件將會推送至您的端點。


### <a name="set-up-with-the-cli"></a>使用 CLI 進行設定

您可以安裝最新的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，或使用 Azure 訂用帳戶中所提供的 Azure Cloud Shell。

若要安裝事件方格擴充功能，從 CLI 使用下列命令：

```azurecli-interactive
az add extension --name eventgrid
```

下列範例示範如何選取 Azure 訂用帳戶，並為 Azure Machine Learning 建立新的事件訂閱：

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>範例

### <a name="example-send-email-alerts"></a>範例：傳送電子郵件警示

使用 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) 設定所有事件的電子郵件。 自訂條件，並指定收件者，讓跨小組的共同作業與認知得以實現。

1. 在 Azure 入口網站中，移至您的 Azure Machine Learning 工作區，從左側欄中選取 [事件] 索引標籤。 從這裡選取 [邏輯應用程式]。 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 登入邏輯應用程式使用者介面，選取 Machine Learning 服務做為主題類型。 

    ![select-topic-type](./media/how-to-use-event-grid/select-topic-type.png)

1. 選取要通知的事件。 例如，下列螢幕擷取畫面中的 [RunCompleted]。

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 您可以使用上一節的篩選方法，或新增篩選器，只針對事件類型的子集觸發邏輯應用程式。 在下列螢幕擷取畫面，使用的 [前置詞篩選] 是 __/datadriftID/runs/__ 。

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. 接下來，新增使用此事件的步驟，並搜尋電子郵件。 有數個不同的郵件帳戶可供您用來接收事件。 您也可以設定何時傳送電子郵件警示的條件。

    ![select-email-action](./media/how-to-use-event-grid/select-email-action.png)

1. 選取 [傳送電子郵件] 並填入參數。 您可以在主旨中包含 [事件種類] 和 [主題] 以協助篩選事件。 您也可以在訊息本文中包含執行之工作區頁面的連結。 

    ![configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. 若要儲存此動作，選取頁面左側的 [另存新檔]。 在出現的右側欄中，確認建立此動作。

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>範例：資料漂移觸發重新定型

模型在一段時間後會過時，在其執行情境中不再實用。 有一種方法可以得知重新定型模型的時機，就是偵測資料漂移。 

此範例示範如何使用事件方格和 Azure Logic App 來觸發重新定型。 範例會在模型定型和服務資料集之間發生資料漂移時，觸發 Azure Data Factory 管線。

開始之前，請執行下列動作：

* 設定資料集監視以[偵測工作區中的資料漂移](how-to-monitor-datasets.md)
* 建立已發佈的 [Azure Data Factory 管線](https://docs.microsoft.com/azure/data-factory/)。

在此範例中，會使用簡單的 Data Factory 管線將檔案複製到 blob 存放區，並執行已發佈的 Machine Learning 管線。 如需此案例的詳細資訊，請參閱如何[在 Azure Data Factory 中設定 Machine Learning 步驟](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)。

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 從建立邏輯應用程式開始。 移至 [Azure 入口網站](https://portal.azure.com)，搜尋 [邏輯應用程式]，然後選取 [建立]。

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 填寫所需的資訊。 為了簡化體驗，請使用與您的 Azure Data Factory 管線和 Azure Machine Learning 工作區相同的訂用帳戶和資源群組。

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 建立邏輯應用程式之後，選取 [事件方格資源事件發生時]。 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 登入並填入事件的詳細資料。 將 [資源名稱] 設定為工作區名稱。 將 [事件種類] 設定為 [DatasetDriftDetected]。

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. 新增新的步驟，然後搜尋 [Azure Data Factory]。 選取 [建立管線執行]。 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 登入並指定要執行的已發佈 Azure Data Factory 管線。

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 使用頁面左上角的 [儲存] 按鈕，儲存並建立邏輯應用程式。 若要檢視您的應用程式，移至 [Azure 入口網站](https://portal.azure.com) 中您的工作區，然後按一下 [事件]。

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

現在，發生漂移時，會觸發 Data Factory 管線。 在[新的工作區入口網站](https://ml.azure.com)上，檢視資料漂移執行和機器學習管線的詳細資訊。 

![view-in-workspace](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>範例：根據標記部署模型

Azure Machine Learning 模型物件包含了可以做為部署樞紐的參數，例如模型名稱、版本、標記、屬性。 模型註冊事件可以觸發端點，而您可以使用 Azure Function，根據這些參數的值來部署模型。

如需範例，請參閱 [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 存放庫，並遵循**讀我檔案**中的步驟。

## <a name="next-steps"></a>後續步驟

深入瞭解事件方格，並嘗試使用 Azure Machine Learning 事件：

- [關於 Event Grid](../event-grid/overview.md)

- [Azure Machine Learning 的事件架構](../event-grid/event-schema-machine-learning.md)

