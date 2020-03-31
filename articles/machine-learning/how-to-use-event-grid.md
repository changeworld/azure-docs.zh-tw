---
title: 創建事件驅動的機器學習工作流
titleSuffix: Azure Machine Learning
description: 瞭解如何將事件網格與 Azure 機器學習一起啟用事件驅動解決方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129709"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>創建事件驅動的機器學習工作流（預覽）

[Azure 事件網格](https://docs.microsoft.com/azure/event-grid/)支援 Azure 機器學習事件。 您可以在工作區中訂閱和使用事件，如運行狀態更改、運行完成、模型註冊、模型部署和資料漂移檢測。

有關事件種類的詳細資訊，請參閱[Azure 機器學習與事件網格](concept-event-grid-integration.md)和 Azure[機器學習事件網格架構](/azure/event-grid/event-schema-machine-learning)的集成。

使用事件網格啟用常見方案，例如：

* 在運行失敗時發送電子郵件並完成運行
* 註冊模型後使用 azure 函數
* 從 Azure 機器學習流式傳輸事件到各種終結點
* 檢測到漂移時觸發 ML 管道

> [!NOTE] 
> 目前，僅在運行狀態**失敗**時運行狀態變更事件
>

## <a name="prerequisites"></a>Prerequisites
* 參與者或擁有者對要為其創建事件的 Azure 機器學習工作區的存取權限。

### <a name="configure-eventgrid-using-the-azure-portal"></a>使用 Azure 門戶配置事件網格

1. 打開[Azure 門戶](https://portal.azure.com)並轉到 Azure 機器學習工作區。

1. 從左側欄中，選擇 __"事件"，__ 然後選擇 **"事件訂閱**"。 

    ![工作區中的選定事件.png](./media/how-to-use-event-grid/select-event.png)

1. 選擇要使用的事件種類。 例如，以下螢幕截圖已選擇__已註冊的模型__、__已部署的模型__、__運行已完成__的和__檢測到的資料集漂移__：

    ![附加事件類型](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 選擇要將事件發佈到的終結點。 在以下螢幕截圖中，__事件中心__是選定的終結點：

    ![選擇事件處理常式](./media/how-to-use-event-grid/select-event-handler.png)

確認選擇後，按一下"__創建__"。 配置後，這些事件將推送到終結點。


### <a name="configure-eventgrid-using-the-cli"></a>使用 CLI 配置事件網格

您可以安裝最新的 Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，也可以使用作為 Azure 訂閱的一部分提供的 Azure 雲外殼。

要安裝事件網格擴展，請使用 CLI 中的以下命令：

```azurecli-interactive
az add extension --name eventgrid
```

以下示例演示如何選擇 Azure 訂閱並為 Azure 機器學習創建新的事件訂閱：

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

## <a name="filter-events"></a>篩選事件

設置事件時，可以將篩選器應用於僅對特定事件資料觸發。 在下面的示例中，對於運行狀態更改的事件，您可以按運行類型進行篩選。 僅當滿足條件時，才會觸發該事件。 請參閱[Azure 機器學習事件網格架構](/azure/event-grid/event-schema-machine-learning)，瞭解可以篩選的事件資料。 

1. 轉到 Azure 門戶，選擇新訂閱或現有訂閱。 

1. 選擇篩選器選項卡並向下滾動到"高級"篩選器。 在 **"鍵**和**值**"中提供了要篩選的屬性類型。 在這裡，您可以看到事件僅在運行類型是管道運行或管道步驟運行時觸發。  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="篩選器事件":::

## <a name="sample-scenarios"></a>範例案例

### <a name="use-a-logic-app-to-send-email-alerts"></a>使用邏輯應用發送電子郵件警報

利用[Azure 邏輯應用](https://docs.microsoft.com/azure/logic-apps/)為所有事件配置電子郵件。 自訂條件並指定收件者，以便跨協作的團隊進行協作和感知。

1. 在 Azure 門戶中，轉到 Azure 機器學習工作區，然後從左側欄中選擇事件選項卡。 在此處選擇__邏輯應用__。 

    ![選擇邏輯-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 登錄到邏輯應用 UI 並選擇機器學習服務作為主題類型。 

    ![選擇主題類型](./media/how-to-use-event-grid/select-topic-type.png)

1. 選擇要通知的事件。 例如，下面的螢幕截圖__運行完成__。

    ![選擇事件運行完成](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 您可以在上述部分中使用篩選方法，或添加篩選器以僅觸發事件種類子集的邏輯應用。 在下面的螢幕截圖中，使用 __/資料漂移 ID/運行/的前____綴篩選器__。

    ![篩選器事件](./media/how-to-use-event-grid/filtering-events.png)

1. 接下來，添加一個步驟以使用此事件並搜索電子郵件。 可以使用多個不同的郵件帳戶來接收事件。 您還可以配置有關何時發送電子郵件警報的條件。

    ![選擇電子郵件操作](./media/how-to-use-event-grid/select-email-action.png)

1. 選擇 __"發送電子郵件__並填寫參數"。 在主題中，您可以包括__事件種類__和__主題__，以説明篩選事件。 您還可以包含指向郵件內文中運行的工作區頁面的連結。 

    ![配置電子郵件正文](./media/how-to-use-event-grid/configure-email-body.png)

1. 要保存此操作，請選擇"在頁面左角**保存為"** 從顯示的右側欄中，確認此操作的創建。

    ![確認邏輯-應用程式創建](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>使用邏輯應用在發生資料漂移時觸發重新訓練工作流

模型會隨著時間推移而過時，並且在它正在運行的上下文中不會保持有用。 判斷是否該重新訓練模型的方法之一是檢測資料漂移。 

此示例演示如何將事件網格與 Azure 邏輯應用一起觸發重新訓練。 當資料在模型訓練資料集和服務資料集之間發生資料漂移時，該示例將觸發 Azure 資料工廠管道。

開始之前，執行以下操作：

* 設置資料集監視器以檢測工作區中[的資料漂移]( https://aka.ms/datadrift)
* 創建已發佈的[Azure 資料工廠管道](https://docs.microsoft.com/azure/data-factory/)。

在此示例中，一個簡單的資料工廠管道用於將檔案複製到 Blob 存儲並運行已發佈的機器學習管道。 有關此方案的詳細資訊，請參閱如何在[Azure 資料工廠中設置機器學習步驟](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-ml 管道階段](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 從創建邏輯應用開始。 轉到[Azure 門戶](https://portal.azure.com)，搜索邏輯應用，然後選擇"創建"。

    ![搜索邏輯應用](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 填寫所需的資訊。 要簡化體驗，請使用與 Azure 資料工廠管道和 Azure 機器學習工作區相同的訂閱和資源組。

    ![設置邏輯-應用程式-為 adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 創建邏輯應用後，選擇事件__網格資源事件發生時間__。 

    ![選擇事件網格觸發器](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 登錄並填寫活動的詳細資訊。 將__資源名稱__設置為工作區名稱。 將__事件種類__設置為 __"已檢測到的資料集漂移__"。

    ![登錄和添加事件](./media/how-to-use-event-grid/login-and-add-event.png)

1. 添加新步驟，然後搜索 Azure__資料工廠__。 選擇 __"創建管道運行__"。 

    ![創建-開發管道運行](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 登錄並指定要運行的已發佈的 Azure 資料工廠管道。

    ![指定-adf-管道](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 使用頁面左上角的**保存**按鈕保存和創建邏輯應用。 要查看應用，請轉到[Azure 門戶](https://portal.azure.com)中的工作區，然後按一下 **"事件**"。

    ![顯示邏輯-應用-Webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

現在，當漂移發生時，將觸發資料工廠管道。 [在新工作區門戶](https://ml.azure.com)上查看有關資料漂移運行和機器學習管道的詳細資訊。 

![工作區中的視圖](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>使用 Azure 函數基於標記部署模型

Azure 機器學習模型物件包含可以透視部署的參數，例如模型名稱、版本、標記和屬性。 模型註冊事件可以觸發終結點，您可以使用 Azure 函數根據這些參數的值部署模型。

例如，[https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid)請參閱存儲庫，然後按照**readme**檔中的步驟操作。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關可用事件的更多詳細資訊，請參閱[Azure 機器學習事件架構](/azure/event-grid/event-schema-machine-learning)
