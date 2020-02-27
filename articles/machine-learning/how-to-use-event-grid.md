---
title: 建立事件驅動的機器學習工作流程
titleSuffix: Azure Machine Learning
description: 瞭解如何搭配 Azure Machine Learning 使用事件方格，以啟用事件驅動的解決方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: 49ee00d43820d5aeb50e44cff1b6c5a448b4ce81
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623907"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>建立事件驅動機器學習工作流程（預覽）

[Azure 事件方格](https://docs.microsoft.com/azure/event-grid/)支援 Azure Machine Learning 事件。 例如，您可以使用範圍設定為工作區的執行完成、模型註冊、模型部署和資料漂移偵測的事件。

如需詳細資訊，請參閱[Azure Machine Learning 與事件方格整合](concept-event-grid-integration.md)和[Azure Machine Learning 事件方格架構](/azure/event-grid/event-schema-machine-learning)。

使用「事件方格」來啟用常見案例，例如：

* 執行完成時傳送電子郵件
* 在註冊模型之後使用 azure 函式
* 將事件從 Azure Machine Learning 串流至各種端點
* 偵測到漂移時觸發 ML 管線

## <a name="prerequisites"></a>Prerequisites
* 您將為其建立事件之 Azure Machine Learning 工作區的參與者或擁有者存取權。

### <a name="configure-eventgrid-using-the-azure-portal"></a>使用 Azure 入口網站設定 EventGrid

1. 開啟[Azure 入口網站](https://portal.azure.com)並移至您的 Azure Machine Learning 工作區。

1. 從左側列選取 [__事件__]，然後選取 [**事件訂閱**]。 

    ![select-events-in-workspace .png](./media/how-to-use-event-grid/select-event.png)

1. 選取要使用的事件種類。 例如，下列螢幕擷取畫面已選取 [__已註冊的模型__]、已__部署模型__、[__執行已完成__] 和 [偵測__到資料集漂移__]：

    ![新增-事件種類](./media/how-to-use-event-grid/add-event-type.png)

1. 選取要發佈事件的端點。 在下列螢幕擷取畫面中，__事件中樞__是選取的端點：

    ![select-事件處理常式](./media/how-to-use-event-grid/select-event-handler.png)

確認您的選擇之後，請按一下 [__建立__]。 設定之後，這些事件將會推送至您的端點。

### <a name="configure-eventgrid-using-the-cli"></a>使用 CLI 設定 EventGrid

您可以安裝最新的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，或使用 Azure 訂用帳戶中提供的 Azure Cloud Shell。

若要安裝事件方格延伸模組，請從 CLI 使用下列命令：

```azurecli-interactive
az add extension --name eventgrid
```

下列範例示範如何選取 Azure 訂用帳戶，並為 Azure Machine Learning 建立新的事件訂用帳戶：

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="sample-scenarios"></a>範例案例

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>使用 Azure Functions 根據標記來部署模型

Azure Machine Learning 模型物件包含您可以在其中進行資料透視部署的參數，例如模型名稱、版本、標記和屬性。 模型註冊事件可以觸發端點，而您可以使用 Azure 函式，根據這些參數的值來部署模型。

如需範例，請參閱[https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid)存放庫，並遵循**自述**檔中的步驟。

### <a name="use-a-logic-app-to-send-email-alerts"></a>使用邏輯應用程式傳送電子郵件警示

利用[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)來設定所有事件的電子郵件。 使用條件進行自訂，並指定收件者，讓小組共同作業與認知。

1. 在 Azure 入口網站中，移至您的 Azure Machine Learning 工作區，然後從左側列選取 事件 索引標籤。 從這裡選取 [__邏輯應用程式__]。 

    ![選取-邏輯-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 登入邏輯應用程式 UI，然後選取 [Machine Learning 服務] 作為 [主題類型]。 

    ![選取-主題類型](./media/how-to-use-event-grid/select-topic-type.png)

1. 選取要通知的事件。 例如，下列螢幕擷取畫面__RunCompleted__。

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 您也可以新增篩選器，只在事件種類的子集上觸發邏輯應用程式。 在下列螢幕擷取畫面中，會使用 __/datadriftID/runs/__ 的__前置詞篩選準則__。

    ![篩選-事件](./media/how-to-use-event-grid/filtering-events.png)

1. 接下來，新增使用此事件的步驟，並搜尋電子郵件。 有數個不同的郵件帳戶可供您用來接收事件。 您也可以設定何時傳送電子郵件警示的條件。

    ![選取-電子郵件-動作](./media/how-to-use-event-grid/select-email-action.png)

1. 選取 [__傳送電子郵件__] 並填入參數。 在主旨中，您可以包含__事件種類__和__主題__，以協助篩選事件。 您也可以在訊息本文中包含執行之工作區頁面的連結。 

    ![設定-電子郵件-內文](./media/how-to-use-event-grid/configure-email-body.png)

1. 若要儲存此動作，請選取頁面左上角的 [**另存**新檔]。 從出現的右側列中，確認此動作的建立。

    ![確認-邏輯-應用程式-建立](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>使用邏輯應用程式，在發生資料漂移時觸發重新訓練工作流程

模型在一段時間後會過時，而不會在其執行所在的內容中繼續運作。 有一種方法可以得知重新定型模型的時機是偵測資料漂移。 

此範例示範如何使用事件方格搭配 Azure 邏輯應用程式來觸發重新定型。 此範例會在模型定型和服務資料集之間發生資料漂移時，觸發 Azure Data Factory 管線。

開始之前，請執行下列動作：

* 設定資料集監視，以偵測工作區中的[資料漂移]( https://aka.ms/datadrift)
* 建立已發佈的[Azure Data Factory 管線](https://docs.microsoft.com/azure/data-factory/)。

在此範例中，會使用簡單的 Data Factory 管線將檔案複製到 blob 存放區，並執行已發行的 Machine Learning 管線。 如需此案例的詳細資訊，請參閱如何[在 Azure Data Factory 中設定 Machine Learning 步驟](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-階段](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 從建立邏輯應用程式開始。 移至 [ [Azure 入口網站](https://portal.azure.com)]，搜尋 Logic Apps，然後選取 [建立]。

    ![搜尋-邏輯-應用程式](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 填寫所需的資訊。 若要簡化體驗，請使用與 Azure Data Factory 管線和 Azure Machine Learning 工作區相同的訂用帳戶和資源群組。

    ![設定-邏輯應用程式-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 建立邏輯應用程式之後，請選取 [__發生事件方格資源事件時__]。 

    ![選取事件-格線-觸發程式](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 登入並填入事件的詳細資料。 將 [__資源名稱__] 設定為工作區名稱。 將__事件種類__設定為__DatasetDriftDetected__。

    ![登入與新增事件](./media/how-to-use-event-grid/login-and-add-event.png)

1. 新增新的步驟，並搜尋__Azure Data Factory__。 選取 [__建立管線執行__]。 

    ![建立-adfpipeline-執行](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 登入並指定要執行的已發行 Azure Data Factory 管線。

    ![指定-adf-管線](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 使用頁面左上角的 [**儲存**] 按鈕來儲存並建立邏輯應用程式。 若要查看您的應用程式，請移至[Azure 入口網站](https://portal.azure.com)中的工作區，然後按一下 [**事件**]。

    ![顯示-邏輯-應用程式-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

現在，當發生漂移時，會觸發 data factory 管線。 在[新的工作區入口網站](https://ml.azure.com)上，查看資料漂移執行和機器學習管線的詳細資訊。 

![視圖-工作區](./media/how-to-use-event-grid/view-in-workspace.png)


## <a name="next-steps"></a>後續步驟

* 若要深入瞭解可用的事件，請參閱[Azure Machine Learning 事件架構](/azure/event-grid/event-schema-machine-learning)
