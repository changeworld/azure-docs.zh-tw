---
title: 部署和取用
titleSuffix: ML Studio (classic) - Azure
description: 您可以使用 Azure Machine Learning Studio （傳統）將機器學習工作流程和模型部署為 web 服務。 然後便可以透過網際網路利用這些 Web 服務從應用程式呼叫機器學習服務模型，進行即時預測或批次模式的預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79204387"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio （傳統） Web 服務：部署和耗用量

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

您可以使用 Azure Machine Learning Studio （傳統）將機器學習工作流程和模型部署為 web 服務。 然後便可以透過網際網路利用這些 Web 服務從應用程式呼叫機器學習服務模型，進行即時預測或批次模式的預測。 由於 Web 服務為 RESTful，您可以從各種程式設計語言與平台 (如 .NET 與 Java) 和應用程式 (如 Excel) 呼叫它們。

下列章節提供可協助您快速開始的逐步解說、程式碼及文件的連結。

## <a name="deploy-a-web-service"></a>部署 Web 服務

### <a name="with-azure-machine-learning-studio-classic"></a>使用 Azure Machine Learning Studio （傳統）

Studio （傳統）入口網站和 Microsoft Azure Machine Learning Web 服務入口網站可協助您部署和管理 web 服務，而不需要撰寫程式碼。

下列連結提供有關如何部署新的 Web 服務的一般資訊︰

* 如需如何部署以 Azure Resource Manager 為基礎的新 Web 服務的概觀，請參閱 [部署新的 Web 服務](deploy-a-machine-learning-web-service.md)。
* 如需如何部署 Web 服務的逐步解說，請參閱 [部署 Azure Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)。
* 如需如何建立和部署 web 服務的完整逐步解說，請從[教學課程1：預測信用風險](tutorial-part1-credit-risk.md)開始。
* 如需部署 Web 服務的特定範例，請參閱︰

  * [教學課程3：部署信用風險模型](tutorial-part3-credit-risk-deploy.md)
  * [如何將 Web 服務部署到多個區域](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>使用 Web 服務資源提供者 API (Azure Resource Manager API)

Web 服務的 Azure Machine Learning Studio （傳統）資源提供者可讓您使用 REST API 呼叫來部署和管理 web 服務。 如需詳細資訊，請參閱 [Machine Learning Web 服務 (REST)](/rest/api/machinelearning/index) 參考資料。

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>使用 PowerShell Cmdlet

Web 服務的 Azure Machine Learning Studio （傳統）資源提供者可讓您使用 PowerShell Cmdlet 來部署和管理 web 服務。

若要使用這些 Cmdlet，您必須先使用[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount)指令程式，從 PowerShell 環境中登入您的 Azure 帳戶。 如果您不熟悉如何呼叫以 Resource Manager 為基礎的 PowerShell 命令，請參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md)。

若要匯出預測實驗，請使用這個 [範例程式碼](https://github.com/ritwik20/AzureML-WebServices)。 由程式碼建立 .exe 檔案之後，您可以輸入︰

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

執行應用程式會建立 Web 服務的 JSON 範本。 若要使用此範本部署 Web 服務，必須新增下列資訊︰

* 儲存體帳戶名稱和金鑰

    您可以從 [Azure 入口網站](https://portal.azure.com/)取得儲存體帳戶名稱和金鑰。
* 承諾用量方案識別碼

    您可以從 [Azure Machine Learning Web 服務](https://services.azureml.net) 入口網站登入，按一下方案名稱取得方案識別碼。

將它們新增至 JSON 範本做為 *Properties* 節點的子系，與 *MachineLearningWorkspace* 節點位於相同層級。

範例如下：

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

如需更詳細的資訊，請參閱下列文章和範例程式碼：

* MSDN 上的[Azure Machine Learning Studio （傳統） Cmdlet](https://docs.microsoft.com/powershell/module/az.machinelearning)參考
* GitHub 上的範例 [逐步解說](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)

## <a name="consume-the-web-services"></a>取用 Web 服務

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>從 Azure Machine Learning Web Services UI (測試)

您可以從 Azure Machine Learning Web Services 入口網站測試您的 Web 服務。 這包括測試要求-回應服務 (RRS) 和批次執行服務 (BES) 介面。

* [部署新的 Web 服務](deploy-a-machine-learning-web-service.md)
* [部署 Azure Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)
* [教學課程3：部署信用風險模型](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>從 Excel

您可以下載可取用 Web 服務的 Excel 範本︰

* [從 Excel 使用 Azure Machine Learning Web 服務](consuming-from-excel.md)
* [適用于 Azure Machine Learning Web 服務的 Excel 增益集](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>從以 REST 為基礎的用戶端

Azure Machine Learning Web 服務是 RESTful API。 您可以從各種平臺（例如 .NET、Python、R、JAVA 等）使用這些 Api。在[Microsoft Azure Machine Learning Web 服務入口](https://services.azureml.net)網站上，web 服務的 [取用] 頁面有可協助您開始**使用**的範例程式碼。 如需詳細資訊，請參閱 [如何取用 Azure Machine Learning Web 服務](consume-web-services.md)。
