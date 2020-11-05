---
title: 資料擷取管線的 DevOps
titleSuffix: Azure Machine Learning
description: 瞭解如何套用 DevOps 實務來建立資料內嵌管線，以使用 Azure Data Factory 和 Azure Databricks 來準備資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: fe2f35708f6a148f8db9ef6fd0a598e19e746fbd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358621"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>資料擷取管線的 DevOps

在大部分的情況下，資料內嵌解決方案是腳本、服務調用和協調所有活動的管線組合。 在本文中，您將瞭解如何將 DevOps 實務套用至常見資料內嵌管線的開發生命週期，以準備資料以進行機器學習模型定型。 管線是使用下列 Azure 服務所建立：

* __Azure Data Factory__ ：讀取原始資料並協調資料準備。
* __Azure Databricks__ ：執行可轉換資料的 Python 筆記本。
* __Azure Pipelines__ ：自動化持續整合和開發程式。

## <a name="data-ingestion-pipeline-workflow"></a>資料內嵌管線工作流程

資料內嵌管線會執行下列工作流程：

1. 原始資料會讀入 Azure Data Factory (ADF) 管線中。
1. ADF 管線會將資料傳送到 Azure Databricks 叢集，以執行 Python 筆記本來轉換資料。
1. 資料會儲存至 blob 容器，Azure Machine Learning 可以使用它來定型模型。

![資料內嵌管線工作流程](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>持續整合與傳遞總覽

就像許多軟體解決方案一樣，有一個小組 (例如，資料工程師) 處理它。 它們會共同作業並共用相同的 Azure 資源，例如 Azure Data Factory、Azure Databricks 和 Azure 儲存體帳戶。 這些資源的集合是開發環境。 資料工程師會參與相同的原始程式碼基底。

持續整合和傳遞系統會將建立、測試及傳遞 (部署) 解決方案的程式自動化。 持續整合 (CI) 進程會執行下列工作：

* 組合程式碼
* 使用程式碼品質測試進行檢查
* 執行單元測試
* 產生成品，例如已測試的程式碼和 Azure Resource Manager 範本

持續傳遞 (CD) 程式會將構件部署至下游環境。

![cicd 資料內嵌圖表](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

本文示範如何使用 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)將 CI 和 CD 處理常式自動化。

## <a name="source-control-management"></a>原始檔控制管理

需要原始檔控制管理才能追蹤變更，以及啟用小組成員之間的共同作業。
例如，程式碼會儲存在 Azure DevOps、GitHub 或 GitLab 存放庫中。 共同作業工作流程是以分支模型為基礎。 例如， [>gitflow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)。

### <a name="python-notebook-source-code"></a>Python 筆記本來源程式碼

資料工程師可以在 IDE 本機使用 Python 筆記本原始程式碼 (例如 [Visual Studio Code](https://code.visualstudio.com)) 或直接在 Databricks 工作區中。 程式碼變更完成後，就會將它們合併至分支原則之後的儲存機制。

> [!TIP] 
> 建議您將程式碼儲存在檔案中， `.py` 而不是 `.ipynb` Jupyter Notebook 格式。 它可提升程式碼的可讀性，並在 CI 程式中啟用自動程式碼品質檢查。

### <a name="azure-data-factory-source-code"></a>Azure Data Factory 來源程式碼

Azure Data Factory 管線的原始程式碼是 Azure Data Factory 工作區所產生的 JSON 檔案集合。 資料工程師通常會在 Azure Data Factory 工作區中使用視覺化設計工具，而不是直接使用原始程式碼檔。 

若要將工作區設定為使用原始檔控制儲存機制，請參閱 [使用 Azure Repos Git 整合的作者](../data-factory/source-control.md#author-with-azure-repos-git-integration)。   

## <a name="continuous-integration-ci"></a>持續整合 (CI)

持續整合程式的最終目標是要從原始程式碼收集聯合小組工作，並準備好部署到下游環境。 如同原始程式碼管理，Python 筆記本和 Azure Data Factory 管線的此程式不同。 

### <a name="python-notebook-ci"></a>Python 筆記本 CI

Python 筆記本的 CI 進程會從共同作業分支取得程式碼 (例如 * **master** _ 或 _*_開發_*_ ) 並執行下列活動： _ code linting
* 單元測試
* 將程式碼儲存為成品

下列程式碼片段示範如何在 Azure DevOps * **yaml** _ 管線中執行這些步驟：

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

管線會使用 [flake8](https://pypi.org/project/flake8/) 來進行 Python 程式碼 linting。 它會執行在原始程式碼中定義的單元測試，併發布 linting 和測試結果，使其可在 Azure 管線執行畫面中使用：

![linting 單元測試](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

如果 linting 和單元測試成功，管線就會將原始程式碼複製到成品存放庫，以供後續的部署步驟使用。

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Azure Data Factory 管線的 CI 進程是資料內嵌管線的瓶頸。 沒有持續整合。 Azure Data Factory 可部署的成品是 Azure Resource Manager 範本的集合。 產生這些範本的唯一方法是按一下 [Azure Data Factory] 工作區中的 [ **發佈** ] 按鈕。

1. 資料工程師會將其功能分支中的原始程式碼合併到共同作業分支，例如 _*_master_*_ 或 _*_開發_*_ 。 
1. 具有授與許可權的使用者按一下 [ _*_發佈_*_ ] 按鈕，從共同作業分支的原始程式碼產生 Azure Resource Manager 範本。 
1. 工作區會驗證管線 (將它視為 linting 和單元測試) 、產生 Azure Resource Manager 的範本 (將它視為建立) ，然後將產生的範本儲存至相同程式碼存放 _*_庫中的技術分支 adf_publish，_*_ (在發行成品) 時將其視為。 此分支是由 Azure Data Factory 工作區自動建立。 

如需此程式的詳細資訊，請參閱 [Azure Data Factory 中的持續整合和傳遞](../data-factory/continuous-integration-deployment.md)。

請務必確定產生的 Azure Resource Manager 範本是環境中立的。 這表示在環境之間可能會有不同的值參數化。 Azure Data Factory 的智慧足以將大部分的這類值公開為參數。 例如，在下列範本中，Azure Machine Learning 工作區的連接屬性會公開為參數：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

不過，根據預設，您可能會想要公開不是由 Azure Data Factory 工作區處理的自訂屬性。 在本文的案例中，Azure Data Factory 管線會叫用處理資料的 Python 筆記本。 筆記本接受具有輸入資料檔案名稱的參數。

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

針對 _*_開發_*_ 、 _*_QA_*_ 、 _*_UAT_*_ 和 _*_生產_*_ 環境，此名稱是不同的。 在具有多個活動的複雜管線中，可以有數個自訂屬性。 最好是在一個位置收集所有這些值，並將它們定義為管線 _*_變數_*_ ：

![螢幕擷取畫面會顯示名為 PrepareData 和 M L 執行管線的筆記本，其頂端有選取 [變數] 索引標籤，其中包含 [變數] 索引標籤，並使用 [名稱]、[類型] 和 [預設值] 選項來加入新的變數。](media/how-to-cicd-data-ingestion/adf-variables.png)

管線活動可能會在實際使用時參考管線變數：

![螢幕擷取畫面顯示名為 PrepareData 和 M L 執行管線的筆記本，其頂端的 [設定] 索引標籤位於下方選取的 [設定] 索引標籤。](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Azure Data Factory 工作區預設 _*_不會_*_ 將管線變數公開為 Azure Resource Manager 範本參數。 工作區會使用 [預設的參數化範本](../data-factory/continuous-integration-deployment.md#default-parameterization-template) ，以聽寫哪些管線屬性應該公開為 Azure Resource Manager 範本參數。 若要將管線變數加入至清單，請 `"Microsoft.DataFactory/factories/pipelines"` 使用下列程式碼片段更新 [預設參數化範本](../data-factory/continuous-integration-deployment.md#default-parameterization-template) 的區段，並將結果 json 檔案放在源資料夾的根目錄中：

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "_": {
                    "defaultValue": "="
                }
            }
        }
    }
```

這麼做會強制 Azure Data Factory 工作區在按下 [ **發行** _] 按鈕時，將變數新增至 [參數] 清單：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

JSON 檔案中的值是在管線定義中設定的預設值。 部署 Azure Resource Manager 範本時，應以目標環境值覆寫這些值。

## <a name="continuous-delivery-cd"></a>持續傳遞 (CD)

持續傳遞程式會使用成品，並將其部署至第一個目標環境。 它會藉由執行測試來確定解決方案是否正常運作。 如果成功，則會繼續進行下一個環境。 

CD Azure 管線是由代表環境的多個階段所組成。 每個階段都包含執行下列步驟的 [部署](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) 和 [作業](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) ：

_ 將 Python 筆記本部署到 Azure Databricks 工作區
* 部署 Azure Data Factory 管線 
* 執行管線
* 檢查資料內嵌結果

您可以使用 [核准](/azure/devops/pipelines/process/approvals?tabs=check-pass&view=azure-devops&preserve-view=true) 和網 [關](/azure/devops/pipelines/release/approvals/gates?view=azure-devops&preserve-view=true) 來設定管線階段，以提供部署程式如何透過環境鏈進行的額外控制。

### <a name="deploy-a-python-notebook"></a>部署 Python 筆記本

下列程式碼片段會定義將 Python 筆記本複製到 Databricks 叢集的 Azure 管線 [部署](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) ：

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

CI 所產生的成品會自動複製到部署代理程式，並可在資料夾中使用 `$(Pipeline.Workspace)` 。 在此情況下，部署工作是指 `di-notebooks` 包含 Python 筆記本的成品。 此 [部署](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) 會使用 [Databricks Azure DevOps 擴充](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) 功能，將筆記本檔案複製到 Databricks 工作區。

`Deploy_to_QA`階段包含 `devops-ds-qa-vg` Azure DevOps 專案中定義之變數群組的參考。 此階段中的步驟會參考此變數群組中的變數 (例如， `$(DATABRICKS_URL)` 以及 `$(DATABRICKS_TOKEN)`) 。 其構想是下一個階段 (例如， `Deploy_to_UAT`) 將會使用其本身 UAT 範圍變數群組中定義的相同變數名稱來運作。

### <a name="deploy-an-azure-data-factory-pipeline"></a>部署 Azure Data Factory 管線

Azure Data Factory 可部署的成品是 Azure Resource Manager 的範本。 它會使用 * **Azure 資源群組部署** _ 工作進行部署，如下列程式碼片段所示：

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
資料檔案名參數的值來自于 `$(DATA_FILE_NAME)` QA 階段變數群組中定義的變數。 同樣地，您可以覆寫 _*_ARMTemplateForFactory.js_*_ 中定義的所有參數。 如果不是，則會使用預設值。

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>執行管線並檢查資料內嵌結果

下一步是確定已部署的解決方案可正常運作。 下列工作定義會使用 [PowerShell 腳本](https://github.com/microsoft/DataOps/tree/master/adf/utils) 來執行 Azure Data Factory 管線，並在 Azure Databricks 叢集上執行 Python 筆記本。 筆記本會檢查資料是否已正確內嵌，並以名稱驗證結果資料檔案 `$(bin_FILE_NAME)` 。

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

作業中的最後一個工作會檢查筆記本執行的結果。 如果傳回錯誤，則會將管線執行的狀態設定為 failed。

## <a name="putting-pieces-together"></a>將元件放在一起

完整的 CI/CD Azure 管線包含下列階段： _ CI
* 部署至 QA
    * 部署至 Databricks + 部署至 ADF
    * 整合測試

它包含數個與您擁有的目標環境數目相等的 [ **部署** ] 階段。 每個 _*_部署_*_ 階段包含兩個平行執行的 [部署](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) ，以及在部署後執行的 [作業](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) ，以便在環境上測試解決方案。

管線的範例執行會在下列 _*_yaml_*_ 程式碼片段中組合：

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>後續步驟

* [Azure Data Factory 中的原始檔控制](../data-factory/source-control.md)
* [Azure Data Factory 中的持續整合和傳遞](../data-factory/continuous-integration-deployment.md)
* [Azure Databricks 的 DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)