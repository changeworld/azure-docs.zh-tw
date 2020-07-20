---
title: 重新定型傳統 web 服務
titleSuffix: ML Studio (classic) - Azure
description: 瞭解如何重新定型模型，並更新傳統 web 服務，以在 Azure Machine Learning Studio （傳統）中使用新定型的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: c1dd91a800c8e807d527f24a381262bde97d792c
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86080307"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>重新定型和部署傳統 Studio （傳統） web 服務

若想要確保機器模型保持精準，並確定模型所根據的是相關性最高的可用資料，其中一種方式就是重新定型機器模型。 本文將說明如何重新訓練傳統 Studio （傳統） web 服務。 如需如何重新訓練新 Studio （傳統） web 服務的指南，請[參閱這篇操作說明文章。](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>必要條件

本文假設您進行過了重新定型實驗和預測性實驗。 [重新定型和部署機器學習模型](/azure/machine-learning/studio/retrain-machine-learning-model)一文有講解這些步驟。 不過，無需將您的機器學習模型部署為新 Web 服務，而是要將預測性實驗部署為傳統 Web 服務。
     
## <a name="add-a-new-endpoint"></a>新增端點

您部署的預測性 Web 服務包含預設評分端點，它會與原始定型和評分實驗定型的模型保持同步。 若要將您的 Web 服務更新為新訓練的模型，您必須建立新的評分端點。

有兩種方式可在 Web 服務新增端點︰

* 以程式設計方式
* 使用 Azure Web 服務入口網站

> [!NOTE]
> 請確定您將端點新增至預測性 Web 服務，而不是定型 Web 服務。 如果您正確部署定型和預測性 Web 服務，您應該會看到列出兩個不同的 Web 服務。 預測性 Web 服務應是以 "[predictive exp.]" 結尾。
>

### <a name="programmatically-add-an-endpoint"></a>以程式設計方式新增端點

您可以使用此[GitHub 存放庫](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)中提供的範例程式碼來新增評分端點。

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>使用 Azure Web 服務入口網站新增端點

1. 在 [Machine Learning Studio （傳統）] 中，按一下左側導覽列上的 [Web 服務]。
1. 在 web 服務儀表板底部，按一下 [**管理端點預覽**]。
1. 按一下 **[新增]** 。
1. 輸入新端點的名稱和描述。 選取記錄層級，以及是否啟用範例資料。 如需記錄的詳細資訊，請參閱[啟用 Machine Learning web 服務的記錄](web-services-logging.md)。

## <a name="update-the-added-endpoints-trained-model"></a>更新新增端點的定型模型

### <a name="retrieve-patch-url"></a>擷取 PATCH URL

請按照下列步驟，使用 Web 入口網站取得正確的 PATCH URL：

1. 登入 [Azure Machine Learning Web 服務](https://services.azureml.net/)入口網站。
1. 按一下頂端的 [Web 服務]**** 或 [傳統 Web 服務]****。
1. 按一下您正在處理的評分 Web 服務 (如果您並未修改 Web 服務的預設名稱，它的結尾是「[Scoring Exp.]」)。
1. 按一下 [ **+ 新增**]。
1. 在新增端點之後，按一下其端點名稱。
1. 在 [修補程式]**** 之下，按一下 [API 說明]**** 以開啟修補說明頁面。

> [!NOTE]
> 如果您將端點新增至訓練 Web 服務，而不是預測性 Web 服務，當您按一下 [**更新資源**] 連結時，將會收到下列錯誤：「抱歉，但此功能在此內容中不受支援或無法使用。 此 Web 服務有沒有可更新的資源。 造成您的不便我們深感抱歉，並將致力於改善這個工作流程。」
>

PATCH 說明頁面包含必須使用的 PATCH URL，並提供可用來呼叫它的範例程式碼。

![修補 URL。](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>更新端點

您現在可以使用定型模型來更新您先前建立的評分端點。

下列範例程式碼示範如何使用 *BaseLocation*、*RelativeLocation*、*SasBlobToken* 和 PATCH URL 來更新端點。

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

在端點儀表板上可以看到呼叫的 *apiKey* 與 *endpointUrl*。

*資源*中*Name*參數的值應該符合預測實驗中已儲存之定型模型的資源名稱。 取得資源名稱：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左側功能表中，按一下 [ **Machine Learning**]。
1. 在 [名稱] 下，按一下您的工作區，然後按一下 [Web 服務] ****。
1. 在 [名稱] 底下，按一下 **[人口普查模型 [預測 exp]**。
1. 按一下您新增的端點。
1. 在端點儀表板中，按一下 [更新資源] ****。
1. 在 Web 服務的 [更新資源 API 文件] 頁面，您可以在 [可更新的資源]**** 下找到 [資源名稱]****。

如果在您完成端點更新之前 SAS 權杖已到期，您必須執行 GET 以作業識別碼取得新的權杖。

程式碼執行成功後，新的端點應該會在大約 30 秒後開始使用重新定型模型。

## <a name="next-steps"></a>後續步驟

若要深入了解如何管理 Web 服務或追蹤多個實驗執行，請參閱下列文章：

* [探索 Web 服務入口網站](manage-new-webservice.md)
* [管理實驗反覆項目](manage-experiment-iterations.md)