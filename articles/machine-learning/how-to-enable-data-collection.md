---
title: 收集生產環境模型的相關資料
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Blob 儲存體中收集 Azure Machine Learning 輸入模型資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 44acc81df9eb6dc6a6af28b5b0f4730aa93adffc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475437"
---
# <a name="collect-data-for-models-in-production"></a>在生產環境中收集模型資料

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Azure Machine Learning 的監視 SDK 即將淘汰。 SDK 仍適用于目前使用 SDK 來監視模型中資料漂移的開發人員。 但對於新的客戶，我們建議使用簡化的[資料監視與 Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)。

本文說明如何從 Azure Machine Learning 收集輸入模型資料。 它也會示範如何將輸入資料部署到 Azure Kubernetes Service （AKS）叢集，並將輸出資料儲存在 Azure Blob 儲存體中。

一旦啟用集合，您收集的資料可協助您：

* 當生產資料進入您的模型時，[監視資料偏離](how-to-monitor-data-drift.md)。

* 針對何時重新訓練或優化您的模型，做出更好的決策。

* 使用所收集的資料重新定型您的模型。

## <a name="what-is-collected-and-where-it-goes"></a>收集的內容及其目標

下列是可收集的資料：

* 從部署在 AKS 叢集中的 web 服務建立輸入資料模型。 *不*會收集語音音訊、影像和影片。
  
* 使用生產輸入資料的模型預測。

>[!NOTE]
> 此資料上的 Preaggregation 和 precalculations 目前不是集合服務的一部分。

輸出會儲存在 Blob 儲存體中。 由於資料會新增至 Blob 儲存體，因此您可以選擇您最愛的工具來執行分析。

Blob 中輸出資料的路徑遵循此語法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> 在 0.1.0 a16 之前的 Azure Machine Learning SDK for Python 版本中， `designation`引數名為。 `identifier` 如果您使用較舊的版本開發程式碼，則需要據以更新。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree)。

- AzureMachine 學習工作區、包含您腳本的本機目錄，以及適用于 Python 的 Azure Machine Learning SDK 都必須安裝。 若要瞭解如何安裝，請參閱[如何設定開發環境](how-to-configure-environment.md)。

- 您需要已定型的機器學習模型，才能部署至 AKS。 如果您沒有模型，請參閱[訓練影像分類模型](tutorial-train-models-with-aml.md)教學課程。

- 您需要 AKS 叢集。 如需如何建立和部署至其中的資訊，請參閱[如何部署和位置](how-to-deploy-and-where.md)。

- [設定您的環境](how-to-configure-environment.md)並安裝[Azure Machine Learning 監視 SDK](https://aka.ms/aml-monitoring-sdk)。

## <a name="enable-data-collection"></a>啟用資料收集

不論您透過 Azure Machine Learning 或其他工具部署的模型，都可以啟用資料收集。

若要啟用資料收集，您需要：

1. 開啟評分檔案。

1. 在檔案開頭處新增[下列程式碼](https://aka.ms/aml-monitoring-sdk)：

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. 在 `init` 函式中宣告資料收集變數：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId*是選擇性參數。 如果您的模型不需要，則不需要使用它。 使用*CorrelationId*可協助您更輕鬆地對應其他資料，例如*LoanNumber*或*CustomerId*。
    
    稍後會使用*Identifier*參數來建立 blob 中的資料夾結構。 您可以使用它來區分未經處理資料中的原始資料。

1. 將下列程式碼行新增至 `run(input_df)` 函式：

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. 當您在 AKS 中部署服務時，資料收集*不*會自動設定為**true** 。 更新您的設定檔，如下列範例所示：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    您也可以藉由變更此設定來啟用服務監視的 Application Insights：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 若要建立新的映射並部署機器學習模型，請參閱[如何部署和位置](how-to-deploy-and-where.md)。

如果您已在環境檔案和評分檔案中安裝具有相依性的服務，請遵循下列步驟來啟用資料收集：

1. 移至 [Azure Machine Learning](https://ml.azure.com)。

1. 開啟工作區。

1. 選取 [**部署** > ]**選取 [服務** > **編輯**]。

   ![編輯服務](././media/how-to-enable-data-collection/EditService.PNG)

1. 在 [**高級設定**] 中，選取 [**啟用 Application Insights 診斷和資料收集**]。

1. 選取 [**更新**] 以套用變更。

## <a name="disable-data-collection"></a>停用資料收集

您可以隨時停止收集資料。 使用 Python 程式碼或 Azure Machine Learning 來停用資料收集。

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>選項 1-停用 Azure Machine Learning 中的資料收集

1. 登入 [Azure Machine Learning](https://ml.azure.com)。

1. 開啟工作區。

1. 選取 [**部署** > ]**選取 [服務** > **編輯**]。

   [![選取 [編輯] 選項](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. 在 [**高級設定**] 中，清除 [**啟用 Application Insights 診斷和資料收集**]。

1. 選取 [更新]**** 以套用變更。

您也可以在[Azure Machine Learning](https://ml.azure.com)中存取工作區中的這些設定。

### <a name="option-2---use-python-to-disable-data-collection"></a>選項 2-使用 Python 來停用資料收集

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>驗證和分析您的資料

您可以選擇您偏好的工具來分析您的 Blob 儲存體中收集的資料。

### <a name="quickly-access-your-blob-data"></a>快速存取您的 blob 資料

1. 登入 [Azure Machine Learning](https://ml.azure.com)。

1. 開啟工作區。

1. 選取 [**儲存體**]。

    [![選取儲存體選項](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. 使用下列語法，遵循 blob 輸出資料的路徑：

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>使用 Power BI 分析模型資料

1. 下載並開啟[Power BI Desktop](https://www.powerbi.com)。

1. 選取 [**取得資料**]，然後選取 [ [**Azure Blob 儲存體**](https://docs.microsoft.com/power-bi/desktop-data-sources)]。

    [![Power BI blob 設定](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. 新增您的儲存體帳戶名稱並輸入您的儲存體金鑰。 您可以藉由選取 blob 中的 [**設定** > ] [**存取金鑰**] 來尋找此資訊。

1. 選取**模型資料**容器，然後選取 [**編輯**]。

    [![Power BI 導覽器](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 在 [查詢編輯器] 中，按一下 [**名稱**] 資料行下方，然後新增您的儲存體帳戶。

1. 在篩選準則中輸入您的模型路徑。 如果您只想要查看特定一年或一個月的檔案，只要展開篩選路徑即可。 例如，若只要查看三月份資料，請使用此篩選器路徑：

   /modeldata/\<subscriptionid>/\<resourcegroupname>/\<workspacename>/\<webservicename>/\<modelname>/\<modelversion>/\<指定>/\<年>/3

1. 根據**名稱**值篩選與您相關的資料。 如果您儲存了預測和輸入，就必須為每個建立查詢。

1. 選取 [**內容**] 資料行標題旁的向下雙箭號，以合併檔案。

    [![Power BI 內容](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. 選取 [確定]  。 預先載入的資料。

    [![Power BI 合併檔案](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 選取 [**關閉並**套用]。

1. 如果您已新增輸入和預測，您的資料表會依**RequestId**值自動排序。

1. 開始建置模型資料的自訂報告。

### <a name="analyze-model-data-using-azure-databricks"></a>使用 Azure Databricks 分析模型資料

1. 建立[Azure Databricks 工作區](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

1. 移至 Databricks 工作區。

1. 在您的 Databricks 工作區中，選取 **[上傳資料**]。

    [![選取 Databricks 上傳資料選項](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 選取 [**建立新資料表**]，然後選取 [**其他資料來源** > ]**Azure Blob 儲存體** > **在筆記本中建立資料表**。

    [![建立 Databricks 資料表](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 更新資料的位置。 範例如下：

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks 設定](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 依照範本上的步驟來查看和分析您的資料。
