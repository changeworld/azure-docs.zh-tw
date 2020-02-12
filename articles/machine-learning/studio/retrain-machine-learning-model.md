---
title: 重新定型 web 服務
titleSuffix: ML Studio (classic) - Azure
description: 瞭解如何在 Azure Machine Learning Studio （傳統）中更新 web 服務，以使用新定型的機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 867d104b58980679dc815238fef14050e7d9e8c7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152851"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>重新定型和部署機器學習模型

若想要確保機器模型保持精準，並確定模型所根據的是相關性最高的可用資料，其中一種方式就是重新定型。 本文說明如何將機器學習服務模型重新定型並部署為 Studio （傳統）中的新 web 服務。 如果是要重新定型傳統的 Web 服務，請[參閱操作說明文章](retrain-classic-web-service.md)。

本文假設您已部署了預測性 Web 服務。 如果您還沒有預測性 web 服務，請[在這裡瞭解如何部署 Studio （傳統） web 服務。](deploy-a-machine-learning-web-service.md)

請按照這些步驟重新定型並部署機器學習新 Web 服務：

1. 部署**重新定型 Web 服務**
1. 使用您的**重新定型 Web 服務**定型新模型
1. 更新現有的**預測性實驗**使用新模型

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>部署重新訓練的 Web 服務

重新定型 Web 服務，即可使用一組新的參數 (例如新資料) 來重新定型您的模型，並將模型儲存供稍後使用。 當您將 **Web 服務輸出**連接至**定型模型**時，定型實驗會輸出新模型供您使用。

請按照以下步驟來部署重新定型 Web 服務：

1. 將 **Web 服務輸入** 模組連接至您的資料輸入。 通常，您會想要確保您的輸入資料與原始的訓練資料以相同方式處理。
1. 將 **Web 服務輸出** 模組連接至**定型模型**的輸出。
1. 如果您有 **評估模型** 模組，可以連接 **Web 服務輸出** 模組，以輸出評估結果
1. 執行您的實驗。

    執行實驗後，產生的工作流程應該如下圖所示：

    ![產生的工作流程](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    現在，您可將定型實驗部署為重新定型的 Web 服務，此服務可輸出定型的模型與模型評估結果。

1. 在實驗畫布底端，按一下 [設定 Web 服務]
1. 選取 [部署 Web 服務 [新]]。 Azure Machine Learning Web Services 入口網站會開啟 [部署 Web 服務] 頁面。
1. 輸入您 Web 服務的名稱，然後選擇付款方案。
1. 選取 [部署]。

## <a name="retrain-the-model"></a>重新定型模型

在此範例中，我們使用 C# 建立重新訓練應用程式。 您也可以使用 Python 或 R 範例程式碼來完成這項工作。

使用下列步驟呼叫重新定型 API：

1. 在 Visual Studio 中建立 C# 主控台應用程式：[新增] > [專案] > [Visual C#] > [Windows 傳統桌面] > [主控台應用程式 (.NET Framework)]。
1. 登入 Machine Learning Web 服務入口網站。
1. 按一下您要使用的 Web 服務。
1. 按一下 [取用]。
1. 在 [取用] 頁面底部的 [範例程式碼] 區段，按一下 [批次]。
1. 複製可用於批次執行的範例 C# 程式碼，然後貼入 Program.cs 檔案。 請確定命名空間保持不變。

新增 NuGet 套件 Microsoft.AspNet.WebApi.Client，如註解中所述。 若要新增指向 Microsoft.WindowsAzure.Storage.dll 的參考，您可能需要安裝 [Azure 儲存體服務的用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage)。

下列螢幕擷取畫面顯示 Azure Machine Learning Web 服務入口網站的 [取用]頁面。

![取用頁面](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>更新 apikey 宣告

找到 **apikey** 宣告：

    const string apiKey = "abc123"; // Replace this with the API key for the web service

在 [取用] 頁面的 [基本取用資訊] 區段中，找到主索引鍵，將其複製到 **apikey** 宣告。

### <a name="update-the-azure-storage-information"></a>更新 Azure 儲存體資訊

BES 範例程式碼會將檔案從本機磁碟機 (例如，C:\temp\CensusInput.csv) 上傳至 Azure 儲存體、加以處理後，再將結果寫回 Azure 儲存體。

1. 登入 Azure 入口網站
1. 在左側導覽中，按一下 [更多服務]，搜尋 [儲存體帳戶]，並加以選取。
1. 在儲存體帳戶的清單中，選取要儲存重新定型模型的帳戶。
1. 在左側導覽中，按一下 [存取金鑰]。
1. 複製並儲存 [主要存取金鑰]。
1. 在左側導覽資料行中，按一下 [ **blob**]。
1. 您可以使用現有容器，或建立新的容器並儲存名稱。

找到 StorageAccountName、StorageAccountKey、StorageContainerName 宣告，更新為您從入口網站儲存的值。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

您也必須確保程式碼中指定的位置有輸入檔案。

### <a name="specify-the-output-location"></a>指定輸出位置

在要求承載中指定輸出位置時，必須將 RelativeLocation 中指定檔案的副檔名指定為 `ilearner`。

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

以下是重新定型輸出的範例︰

![重新定型輸出](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>評估重新定型結果

當您執行應用程式時，輸出會包含存取評估結果所需的 URL 和共用存取簽章權杖。

您可以組合 output2 輸出結果中的 BaseLocation、RelativeLocation、SasBlobToken，再將完整 URL 貼入瀏覽器網址列，便能看到重新訓練模型的執行結果。

查看結果以判斷新定型的模型其效能是否比現有的模型好。

儲存輸出結果中的 BaseLocation、RelativeLocation、SasBlobToken。

## <a name="update-the-predictive-experiment"></a>更新預測性實驗

### <a name="sign-in-to-azure-resource-manager"></a>登入 Azure Resource Manager

首先，使用[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet，從 PowerShell 環境中登入您的 Azure 帳戶。

### <a name="get-the-web-service-definition-object"></a>取得 Web 服務定義物件

接下來，藉由呼叫[AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) Cmdlet 來取得 Web 服務定義物件。

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

若要判斷現有 web 服務的資源組名，請執行 AzMlWebService 指令程式而不使用任何參數，以顯示您的訂用帳戶中的 web 服務。 找出 Web 服務，再查看其 Web 服務識別碼。 資源群組的名稱是識別碼中的第四個元素，緊接在 resourceGroups 元素之後。 在下列範例中，資源群組名稱是 Default-MachineLearning-SouthCentralUS。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

或者，若要判斷現有 Web 服務的資源群組名稱，登入 Azure Machine Learning Web 服務入口網站。 選取 Web 服務。 資源群組名稱是 Web 服務 URL 的第五個元素，緊接在 resourceGroups 元素之後。 在下列範例中，資源群組名稱是 Default-MachineLearning-SouthCentralUS。

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>將 Web 服務定義物件匯出為 JSON

若要修改定型模型的定義以使用新定型的模型，您必須先使用[AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) Cmdlet 將它匯出至 JSON 格式的檔案。

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>將參考更新為 ilearner blob

在資產中，找出 [定型模型]，使用 ilearner blob 的 URI 更新 locationInfo 節點中的 uri 值。 URI 的產生方式為結合來自 BES 重新定型呼叫輸出的 BaseLocation 和 RelativeLocation。

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>將 JSON 匯入至 Web 服務定義物件

使用[AzMlWebService 指令程式](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice)，將修改過的 JSON 檔案轉換回 Web 服務定義物件，您可以用來更新預測性實驗。

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>更新 Web 服務

最後，使用[AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) Cmdlet 來更新預測性實驗。

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>後續步驟

若要深入了解如何管理 Web 服務或追蹤多個實驗執行，請參閱下列文章：

* [探索 Web 服務入口網站](manage-new-webservice.md)
* [管理實驗反覆項目](manage-experiment-iterations.md)
