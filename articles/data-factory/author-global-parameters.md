---
title: 全域參數
description: 為每個 Azure Data Factory 環境設定全域參數
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854222"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure Data Factory 中的全域參數

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

全域參數是跨資料處理站的常數，可由任何運算式中的管線使用。 當您有多個管線具有相同的參數名稱和值時，它們就很有用。 使用持續整合和部署程式升級資料處理站時 (CI/CD) 時，您可以在每個環境中覆寫這些參數。 

## <a name="creating-global-parameters"></a>建立全域參數

若要建立全域參數，請移至 [*管理*] 區段中的 [*全域參數*] 索引標籤。 選取 [**新增**] 以開啟 [建立] 側邊導覽。

![建立全域參數](media/author-global-parameters/create-global-parameter-1.png)

在側邊導覽中，輸入名稱、選取資料類型，並指定參數的值。

![建立全域參數](media/author-global-parameters/create-global-parameter-2.png)

建立全域參數之後，您可以按一下參數的名稱來編輯它。 若要一次修改多個參數，請選取 [**全部編輯**]。

![建立全域參數](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>在管線中使用全域參數

全域參數可以在任何[管線運算式](control-flow-expression-language-functions.md)中使用。 如果管線參考另一種資源，例如資料集或資料流程，您可以透過該資源的參數來傳遞全域參數值。 全域參數會當做來參考 `pipeline().globalParameters.<parameterName>` 。

![使用全域參數](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>CI/CD 中的全域參數

在 Azure Data Factory 中，全域參數具有相對於其他實體的唯一 CI/CD 進程。 當您發佈處理站或匯出具有全域參數的 ARM 範本時，會使用名為 GlobalParameters 的檔案來建立名為*globalParameters*的資料夾，其檔案為*your-factory-name_GlobalParameters.js開啟*。 此檔案是 JSON 物件，其中包含已發行之處理站中的每個全域參數類型和值。

![發行全域參數](media/author-global-parameters/global-parameters-adf-publish.png)

如果您要部署到新的環境（例如測試或生產），建議您建立此全域參數檔案的複本，並覆寫適當的環境特定值。 當您重新發佈時，將會覆寫原始的全域參數檔案，但其他環境的複本將不會改變。

例如，如果您有一個名為「ADF-DEV」的 factory，以及一個名稱為「環境」且值為 ' DEV ' 的全域參數，則當您發行名為*ADF-DEV_GlobalParameters.js的*檔案時，就會產生該檔案。 如果部署到名為 ' ADF_TEST ' 的測試 factory，請在) 上建立 JSON 檔案的複本 (例如命名 ADF-TEST_GlobalParameters.js，並將參數值取代為環境特定的值。 參數 ' 環境 ' 現在可能會有值「測試」。 

![部署全域參數](media/author-global-parameters/powershell-task.png)

使用下列 PowerShell 腳本將全域參數升級至其他環境。 在 ARM 範本部署之前新增 Azure PowerShell DevOps 工作。 在 DevOps 工作中，您必須指定新參數檔案、目標資源群組和目標 data factory 的位置。

> [!NOTE]
> 若要使用 PowerShell 部署全域參數，您必須至少使用 Az 模組的版本4.4.0。

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>後續步驟

* 瞭解 Azure Data Factory 的[持續整合和部署](continuous-integration-deployment.md)程式
* 瞭解如何使用[控制流程運算式語言](control-flow-expression-language-functions.md)