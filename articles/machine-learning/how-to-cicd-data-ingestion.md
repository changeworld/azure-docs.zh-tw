---
title: 資料引入管道的 DevOps
titleSuffix: Azure Machine Learning
description: 瞭解如何將 DevOps 實踐應用於用於為模型培訓準備資料的資料引入管道實現。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247175"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>資料引入管道的 DevOps

在大多數情況下，資料引入解決方案是腳本、服務調用和管道的組成，負責協調所有活動。 在本文中，您將瞭解如何將 DevOps 實踐應用於常見資料引入管道的開發生命週期。 管道為機器學習模型培訓準備資料。

## <a name="the-solution"></a>解決方案

請考慮以下資料引入工作流：

![資料引入-管道](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

在此方法中，訓練資料存儲在 Azure Blob 存儲中。 Azure 資料工廠管道從輸入 Blob 容器提取資料，轉換資料並將資料保存到輸出 Blob 容器。 此容器用作 Azure 機器學習服務的[資料存儲](concept-data.md)。 資料工廠管道在準備資料後，將調用一個訓練機器學習管道來訓練模型。 在此特定示例中，資料轉換由在 Azure 資料磚塊群集上運行的 Python 筆記本執行。 

## <a name="what-we-are-building"></a>我們正在建造什麼

與任何軟體解決方案一樣，有一個團隊（例如資料工程師）在研究它。 

![cicd 資料引入](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

它們協作並共用相同的 Azure 資源，如 Azure 資料工廠、Azure 資料塊、Azure 存儲帳戶等。 這些資源的集合是一個開發環境。 資料工程師為相同的原始程式碼庫做出了貢獻。 持續集成過程將代碼組裝，使用代碼品質測試、單元測試對其進行檢查，並生成已測試的代碼和 Azure 資源管理器範本等工件。 連續交付過程將工件部署到下游環境。 本文演示如何使用[Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)自動執行 CI 和 CD 進程。

## <a name="source-control-management"></a>原始程式碼管理

團隊成員以略有不同的方式協作處理 Python 筆記本原始程式碼和 Azure 資料工廠原始程式碼。 但是，在這兩種情況下，代碼都存儲在原始程式碼管理存儲庫中（例如，Azure DevOps、GitHub、GitLab），並且協作通常基於某些分支模型（例如[GitFlow）。](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Python 筆記本原始程式碼

資料工程師在 IDE（例如[，視覺化工作室代碼](https://code.visualstudio.com)）中本地使用 Python 筆記本原始程式碼，或者直接在 Databricks 工作區中使用。 後者提供了在開發環境中調試代碼的能力。 在任何情況下，代碼都將在分支策略之後合併到存儲庫。 強烈建議將代碼存儲在`.py`檔中，而不是以`.ipynb`Jupyter 筆記本格式存儲。 它提高了代碼的可讀性，並在 CI 流程中實現了自動代碼品質檢查。

### <a name="azure-data-factory-source-code"></a>Azure 資料工廠原始程式碼

Azure 資料工廠管道的原始程式碼是工作區生成的 json 檔的集合。 通常，資料工程師在 Azure 資料工廠工作區中使用視覺化設計器，而不是直接使用原始程式碼檔。 使用原始程式碼管理存儲庫配置工作區，如[Azure 資料工廠文檔中](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)所述。 有了此配置，資料工程師能夠按照首選分支工作流就原始程式碼進行協作。    

## <a name="continuous-integration-ci"></a>持續集成 （CI）

持續集成過程的最終目標是從原始程式碼收集聯合團隊工作，並為部署到下游環境做好準備。 與原始程式碼管理一樣，對於 Python 筆記本和 Azure 資料工廠管道，此過程是不同的。 

### <a name="python-notebook-ci"></a>Python 筆記本 CI

Python 筆記本的 CI 進程從協作分支（例如，***主控制***或***開發***）獲取代碼，並執行以下活動：
* 代碼林廷
* 單元測試
* 將代碼保存為專案

以下程式碼片段演示了在 Azure DevOps ***yaml***管道中這些步驟的實現：

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
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

管道使用***flake8***執行 Python 代碼林點。 它運行原始程式碼中定義的單元測試，併發布林丁和測試結果，以便在 Azure 管道執行螢幕中可用：

![林丁單元測試](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

如果林丁和單元測試成功，管道將複製原始程式碼到專案存儲庫，以便後續部署步驟使用。

### <a name="azure-data-factory-ci"></a>Azure 資料工廠 CI

Azure 資料工廠管道的 CI 進程是資料引入管道的整個 CI/CD 故事中的瓶頸。 沒有***持續***集成。 Azure 資料工廠的可部署專案是 Azure 資源管理器範本的集合。 生成這些範本的唯一方法是按一下 Azure 資料工廠工作區中的***發佈***按鈕。 這裡沒有自動化。
資料工程師將原始程式碼從他們的功能分支合併到協作分支中，例如，***主控制***或***開發***。 然後，具有授予許可權的人員按一下***發佈***按鈕，從協作分支中的原始程式碼生成 Azure 資源管理器範本。 按一下該按鈕時，工作區將驗證管道（將其視為林丁和單元測試），生成 Azure 資源管理器範本（將其視為生成範本），並將生成的範本保存到同一代碼存儲庫***中的技術分支adf_publish（*** 將其視為發佈工件）。 此分支由 Azure 資料工廠工作區自動創建。 此過程在[Azure 資料工廠文檔中](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)進行了詳細介紹。

請務必確保生成的 Azure 資源管理器範本與環境無關。 這意味著所有可能不同于環境的值都被參數化。 Azure 資料工廠足夠智慧，能夠公開大多數此類值作為參數。 例如，在以下範本中，與 Azure 機器學習工作區的連接屬性作為參數公開：

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

但是，您可能希望公開預設情況下未由 Azure 資料工廠工作區處理的自訂屬性。 在本文中，Azure 資料工廠管道調用處理資料的 Python 筆記本。 筆記本接受具有輸入資料檔案名稱的參數。

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

此名稱對於***開發人員******、QA、UAT***和***PROD***環境不同。 ***UAT*** 在具有多個活動的複雜管道中，可以有多個自訂屬性。 最好在一個位置收集所有這些值並將其定義為管道***變數***：

![adf 變數](media/how-to-cicd-data-ingestion/adf-variables.png)

管道活動在實際使用管道變數時可能引用它們：

![adf 筆記本參數](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

預設情況下，Azure 資料工廠工作區***不會***公開管道變數作為 Azure 資源管理器範本參數。 工作區使用[預設參數化範本](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)，指示應作為 Azure 資源管理器範本參數公開哪些管道屬性。 為了將管道變數添加到清單中，請使用以下程式碼片段更新[預設參數化範本](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)的"Microsoft.DataFactory/工廠/管道"部分，並將結果 json 檔放在源資料夾的根目錄中：

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

這樣做將強制 Azure 資料工廠工作區在按一下***發佈***按鈕時將變數添加到參數清單中：

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

json 檔中的值是管道定義中配置的預設值。 部署 Azure 資源管理器範本時，預期目標環境值將覆蓋它們。

## <a name="continuous-delivery-cd"></a>連續交付 （CD）

連續交付過程將專案並部署到第一個目標環境。 它確保解決方案通過運行測試工作。 如果成功，它將繼續到下一個環境。 CD Azure 管道由表示環境的多個階段組成。 每個階段都包含執行以下步驟[的部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)和[作業](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)：
* 將 Python 筆記本部署到 Azure 資料磚塊工作區
* 部署 Azure 資料工廠管道 
* 執行管道
* 檢查資料引入結果

管道階段可以配置批准和門，這些[審批](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass)和[門](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops)可對部署過程如何通過環境鏈演變提供額外控制。

### <a name="deploy-a-python-notebook"></a>部署 Python 筆記本

以下程式碼片段定義將 Python 筆記本複製到 Databricks 群集的 Azure 管道[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)：

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

CI 生成的專案將自動複製到部署代理，並在 ***$（Pipeline.工作區）*** 資料夾中可用。 在這種情況下，部署任務引用包含 Python 筆記本的***di 筆記本***專案。 此[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)使用[資料塊 Azure DevOps 副檔名](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)將筆記本檔案複製到資料磚塊工作區。
***Deploy_to_QA***階段包含對 Azure DevOps 專案中定義的***devops-ds-qa-vg***變數組的引用。 此階段的步驟引用此變數組的變數（例如，$（DATABRICKS_URL）、$（DATABRICKS_TOKEN）。 其理念是，下一階段（例如 ***，Deploy_to_UAT***）將使用在其自己的 UAT 範圍變數組中定義的相同變數名稱。

### <a name="deploy-an-azure-data-factory-pipeline"></a>部署 Azure 資料工廠管道

Azure 資料工廠的可部署專案是 Azure 資源管理器範本。 因此，它將與***Azure 資源組部署***任務一起部署，如下段程式碼片段所示：

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
資料檔案名參數的值來自 QA 階段變數組中定義的 $（DATA_FILE_NAME） 變數。 同樣，可以重寫***ARMTemplateForFactory.json***中定義的所有參數。 如果不是，則使用預設值。

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>運行管道並檢查資料引入結果

下一步是確保部署的解決方案正常工作。 以下作業定義使用[PowerShell 腳本](https://github.com/microsoft/DataOps/tree/master/adf/utils)運行 Azure 資料工廠管道，並在 Azure 資料磚塊群集上執行 Python 筆記本。 筆記本檢查資料是否正確引入，並驗證結果資料檔案的名稱為 $（bin_FILE_NAME）。

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

作業中的最後任務檢查筆記本執行的結果。 如果它返回錯誤，它將管道執行的狀態設置為失敗。

## <a name="putting-pieces-together"></a>將碎片放在一起

本文的結果是 CI/CD Azure 管道，它包括以下階段：
* CI
* 部署到 QA
    * 部署到資料塊 + 部署到 ADF
    * 集成測試

它包含許多***部署***階段，等於您擁有的目標環境數。 每個***部署***階段包含兩個並行運行[的部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)，以及一個在部署後運行以在環境中測試解決方案的[作業](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)。

管道的示例實現在以下***yaml***片段中組裝：

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
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
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

* [Azure Data Factory 中的原始檔控制](https://docs.microsoft.com/azure/data-factory/source-control)
* [Azure 資料工廠中的持續集成和交付](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [Azure 資料塊的 DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
