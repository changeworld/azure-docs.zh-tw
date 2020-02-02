---
title: 資料內嵌管線的 DevOps
titleSuffix: Azure Machine Learning
description: 瞭解如何將 DevOps 實務套用到資料內嵌管線執行，用來準備模型定型的資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: 49b384e9e2d9b77179a0154bf2d96524c064c2ca
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960967"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>資料內嵌管線的 DevOps

在大部分的情況下，資料內嵌解決方案是腳本、服務叫用的組合，以及協調所有活動的管線。 在本文中，您將瞭解如何將 DevOps 做法套用至通用資料內嵌管線的開發週期。 管線會準備 Machine Learning 模型定型的資料。

## <a name="the-solution"></a>解決方案

請考慮下列資料內嵌工作流程：

![資料內嵌-管線](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

在此方法中，定型資料會儲存在 Azure blob 儲存體中。 Azure Data Factory 管線會從輸入 blob 容器中提取資料、進行轉換，並將資料儲存至輸出 blob 容器。 此容器可作為 Azure Machine Learning 服務的[資料存放區](https://docs.microsoft.com/azure/machine-learning/concept-data#access-data-in-storage)。 備妥資料之後，Data Factory 管線會叫用定型 Machine Learning 管線來定型模型。 在此特定範例中，資料轉換是由在 Azure Databricks 叢集上執行的 Python 筆記本進行。 

## <a name="what-we-are-building"></a>我們所建立的內容

如同任何軟體解決方案，有一個小組（例如，資料工程師）處理它。 

![cicd-資料內嵌](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

它們會共同作業和共用相同的 Azure 資源，例如 Azure Data Factory、Azure Databricks、Azure 儲存體帳戶等等。 這些資源的集合是一個開發環境。 資料工程師會參與相同的原始程式碼基底。 持續整合程式會組合程式碼、使用程式碼品質測試進行檢查、單元測試，以及產生成品，例如已測試的程式碼和 Azure Resource Manager 範本。 持續傳遞程式會將構件部署至下游環境。 本文示範如何使用[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)將 CI 和 CD 進程自動化。

## <a name="source-control-management"></a>原始檔控制管理

小組成員的工作方式與在 Python 筆記本原始程式碼和 Azure Data Factory 原始程式碼之間共同作業的方法稍有不同。 不過，在這兩種情況下，程式碼會儲存在原始檔控制儲存機制中（例如 Azure DevOps、GitHub、GitLab），而共同作業通常是以某種分支模型（例如[GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)）為基礎。

### <a name="python-notebook-source-code"></a>Python 筆記本原始程式碼

資料工程師會在 IDE 中的本機（例如[Visual Studio Code](https://code.visualstudio.com)），或直接在 Databricks 工作區中使用 Python 筆記本原始碼。 後者可讓您在開發環境上進行程式碼的偵錯工具。 在任何情況下，程式碼都會依照分支原則合併到存放庫。 強烈建議將程式碼儲存在 `.py` 檔案中，而不是 `.ipynb` Jupyter 筆記本格式中。 它可以改善程式碼的可讀性，並在 CI 進程中啟用自動程式碼品質檢查。

### <a name="azure-data-factory-source-code"></a>Azure Data Factory 原始碼

Azure Data Factory 管線的原始程式碼是工作區所產生之 json 檔案的集合。 一般來說，資料工程師會在 Azure Data Factory 工作區中使用視覺化設計工具，而不是直接使用原始程式碼檔案。 使用原始檔控制存放庫來設定工作區，如[Azure Data Factory 檔](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)中所述。 設定好這項設定之後，資料工程師就能夠依照慣用的分支工作流程，在原始程式碼上共同作業。    

## <a name="continuous-integration-ci"></a>持續整合 (CI)

持續整合程式的最終目標，是要收集來自原始程式碼的聯合小組工作，並準備將它部署到下游環境。 如同原始程式碼管理，Python 筆記本和 Azure Data Factory 管線的此程式不同。 

### <a name="python-notebook-ci"></a>Python 筆記本 CI

Python 筆記本的 CI 程式會從共同作業分支（例如，***主要***或***開發***）取得程式碼，並執行下列活動：
* 程式碼 linting
* 單元測試
* 將程式碼儲存為成品

下列程式碼片段示範如何在 Azure DevOps ***yaml***管線中執行這些步驟：

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

管線會使用***flake8***來執行 Python 程式碼 linting。 它會執行原始程式碼中定義的單元測試，併發布 linting 和測試結果，使其可在 Azure 管線執行畫面中使用：

![linting-單元測試](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

如果 linting 和單元測試成功，管線會將原始程式碼複製到成品存放庫，以供後續的部署步驟使用。

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Azure Data Factory 管線的 CI 程式是資料內嵌管線的整個 CI/CD 案例中的瓶頸。 沒有***持續***整合。 Azure Data Factory 的可部署成品是 Azure Resource Manager 範本的集合。 產生這些範本的唯一方式，是按一下 [Azure Data Factory] 工作區中的 [***發佈***] 按鈕。 這裡沒有任何自動化。
資料工程師會將原始程式碼從其功能分支合併到共同作業分支中，例如***master***或***開發***。 然後，具有 [已授與] 許可權的人按一下 [***發佈***] 按鈕，從共同作業分支中的原始程式碼產生 Azure Resource Manager 範本。 當按下按鈕時，工作區會驗證管線（從 linting 和單元測試的角度來看），產生 Azure Resource Manager 範本（從大樓的角度來思考），並將產生的範本儲存至相同程式碼存放庫中的技術分支***adf_publish*** （將其視為發佈成品）。 Azure Data Factory 工作區會自動建立此分支。 如[Azure Data Factory 檔](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)中的詳細資料，請參閱此程式。

請務必確定產生的 Azure Resource Manager 範本與環境無關。 這表示參數化環境中可能會有不同的所有值。 Azure Data Factory 非常聰明，可將大部分的這類值公開為參數。 例如，在下列範本中，Azure Machine Learning 工作區的連接屬性會公開為參數：

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

不過，您可能會想要公開預設不是由 Azure Data Factory 工作區處理的自訂屬性。 在本文的案例中，Azure Data Factory 管線會叫用處理資料的 Python 筆記本。 筆記本接受具有輸入資料檔案名稱的參數。

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

針對***Dev***、 ***QA***、 ***UAT***和***生產***環境，此名稱不同。 在具有多個活動的複雜管線中，可以有數個自訂屬性。 最佳做法是在一個位置收集所有這些值，並將它們定義為管線***變數***：

![adf-變數](media/how-to-cicd-data-ingestion/adf-variables.png)

管線活動可能會參考管線變數，而實際使用它們：

![adf-筆記本-參數](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Azure Data Factory 工作區預設***不會***將管線變數公開為 Azure Resource Manager 樣板參數。 工作區會使用[預設參數化範本](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)，以決定應該將哪些管線屬性公開為 Azure Resource Manager 範本參數。 若要將管線變數新增至清單，請使用下列程式碼片段來更新[預設參數化範本](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)的 "DataFactory/factory/管線" 區段，並將結果 json 檔案放在源資料夾的根目錄中：

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

這麼做會在按一下 [***發佈***] 按鈕時，強制 Azure Data Factory 工作區將變數新增至 [參數] 清單：

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

Json 檔案中的值是在管線定義中設定的預設值。 部署 Azure Resource Manager 範本時，應該會使用目標環境值來覆寫它們。

## <a name="continuous-delivery-cd"></a>持續傳遞 (CD)

持續傳遞程式會接受成品，並將它們部署到第一個目標環境。 它會藉由執行測試，確保解決方案運作正常。 如果成功，它會繼續到下一個環境。 CD Azure 管線包含代表環境的多個階段。 每個階段都包含執行下列步驟的[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)和[作業](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)：
* 將 Python 筆記本部署到 Azure Databricks 工作區
* 部署 Azure Data Factory 管線 
* 執行管道
* 檢查資料內嵌結果

您可以使用[核准](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass)和網[關](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops)來設定管線階段，讓您進一步控制部署程式如何透過環境鏈來發展。

### <a name="deploy-a-python-notebook"></a>部署 Python 筆記本

下列程式碼片段會定義將 Python 筆記本複製到 Databricks 叢集的 Azure 管線[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)：

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

CI 所產生的成品會自動複製到部署代理程式，並可在 ***$ （管線. 工作區）*** 資料夾中使用。 在此情況下，部署工作會參考包含 Python 筆記本的***di-筆記本***構件。 此[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)會使用[Databricks Azure DevOps 擴充](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)功能，將筆記本檔案複製到 Databricks 工作區。
***Deploy_to_QA***階段包含 Azure DevOps 專案中定義之***devops-ds-QA-vg***變數群組的參考。 此階段中的步驟會參考此變數群組中的變數（例如 $ （DATABRICKS_URL）、$ （DATABRICKS_TOKEN））。 其概念是，下一個階段（例如***Deploy_to_UAT***）將會使用其本身的 UAT 範圍變數群組中所定義的相同變數名稱來運作。

### <a name="deploy-an-azure-data-factory-pipeline"></a>部署 Azure Data Factory 管線

Azure Data Factory 的可部署成品是 Azure Resource Manager 範本。 因此，它會使用***Azure 資源群組部署***工作進行部署，如下列程式碼片段所示：

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
資料 filename 參數的值來自于 QA 階段變數群組中定義的 $ （DATA_FILE_NAME）變數。 同樣地，您可以覆寫***ARMTemplateForFactory***中定義的所有參數。 如果不是，則會使用預設值。

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>執行管線並檢查資料內嵌結果

下一個步驟是確定已部署的解決方案可以運作。 下列作業定義會使用[PowerShell 腳本](https://github.com/microsoft/DataOps/tree/master/adf/utils)執行 Azure Data Factory 管線，並在 Azure Databricks 叢集上執行 Python 筆記本。 筆記本會檢查是否已正確內嵌資料，並以 $ （bin_FILE_NAME）名稱驗證結果資料檔案。

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

## <a name="putting-pieces-together"></a>將片段放在一起

本文的結果是由下列階段組成的 CI/CD Azure 管線：
* CI
* 部署至 QA
    * 部署至 Databricks + 部署至 ADF
    * 整合測試

其中包含數個***部署***階段，與您擁有的目標環境數目相等。 每個***部署***階段都包含兩個平行執行的[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)，以及在部署之後執行的[作業](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)，以在環境上測試解決方案。

下列***yaml***程式碼片段中會組合管線的範例執行：

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
* [Azure Data Factory 中的持續整合與傳遞](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [Azure Databricks 的 DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
