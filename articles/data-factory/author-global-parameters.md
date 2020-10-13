---
title: 全域參數
description: 設定每個 Azure Data Factory 環境的全域參數
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/31/2020
ms.openlocfilehash: a936fbec23a38c5b96c678b38b92eed9346b88bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567531"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure Data Factory 中的全域參數

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

全域參數是在資料處理站中的常數，可供任何運算式中的管線使用。 當您有多個具有相同參數名稱和值的管線時，它們很有用。 使用持續整合和部署程式將資料處理站升級 (CI/CD) 時，您可以在每個環境中覆寫這些參數。 

## <a name="creating-global-parameters"></a>建立全域參數

若要建立全域參數，請移至 [*管理*] 區段中的 [*全域參數*] 索引標籤。 選取 [ **新增** ] 以開啟建立的側邊導覽。

![反白顯示您選取用來建立全域參數的 [新增] 按鈕的螢幕擷取畫面。](media/author-global-parameters/create-global-parameter-1.png)

在側邊導覽中，輸入名稱、選取資料類型，然後指定參數的值。

![螢幕擷取畫面，顯示您為新的全域參數新增名稱、資料類型和值的位置。](media/author-global-parameters/create-global-parameter-2.png)

建立全域參數之後，您可以按一下參數的名稱來編輯它。 若要一次修改多個參數，請選取 [ **全部編輯**]。

![建立全域參數](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>在管線中使用全域參數

全域參數可以在任何 [管線運算式](control-flow-expression-language-functions.md)中使用。 如果管線參考其他資源（例如資料集或資料流程），您可以透過該資源的參數來傳遞全域參數值。 全域參數會參考為 `pipeline().globalParameters.<parameterName>` 。

![使用全域參數](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> CI/CD 中的全域參數

有兩種方式可在您的持續整合和部署解決方案中整合全域參數：

* 在 ARM 範本中包含全域參數
* 透過 PowerShell 腳本部署全域參數

在大部分的使用案例中，建議您在 ARM 範本中包含全域參數。 這會以原生方式與 [CI/CD](continuous-integration-deployment.md)檔中所述的解決方案進行整合。全域參數預設會新增為 ARM 範本參數，因為它們通常會從環境變更為環境。 您可以從管理中樞啟用在 ARM 範本中包含全域參數。

![包含在 ARM 範本中](media/author-global-parameters/include-arm-template.png)

將全域參數新增至 ARM 範本會新增可在其他環境中覆寫其他工廠層級設定（例如客戶管理的金鑰或 git 設定）的原廠層級設定。 如果您已在更高許可權的環境中（例如 UAT 或生產環境）啟用這些設定，最好是透過下列步驟中所強調步驟的 PowerShell 腳本來部署全域參數。

### <a name="deploying-using-powershell"></a>使用 PowerShell 進行部署

下列步驟概述如何透過 PowerShell 部署全域參數。 當您的目標 factory 具有原廠層級設定（例如客戶管理的金鑰）時，這會很有用。

當您發佈處理站或匯出具有全域參數的 ARM 範本時，會建立名為 *globalParameters* 的資料夾，其中包含名為 *your-factory-name_GlobalParameters.js*的檔案。 此檔案是一個 JSON 物件，其中包含已發行之處理站中的每個全域參數類型和值。

![發行全域參數](media/author-global-parameters/global-parameters-adf-publish.png)

如果您要部署到新的環境，例如測試或生產環境，建議您建立此全域參數檔的複本，並覆寫適當的環境特定值。 當您重新發佈原始的全域參數檔案時，將會覆寫該檔案，但其他環境的複本將不會保持不變。

例如，如果您有一個名為「ADF-DEV」的 factory，以及名為「環境」且值為「DEV」的全域參數，則當您發行名為 *ADF-DEV_GlobalParameters.js的* 檔案時，將會產生。 如果部署至名為 ' ADF_TEST ' 的測試處理站，請建立 JSON 檔案的複本 (例如) 上名為 ADF-TEST_GlobalParameters.js，並以環境特定的值取代參數值。 參數 ' 環境 ' 現在可以有值 ' test '。 

![部署全域參數](media/author-global-parameters/powershell-task.png)

使用下列 PowerShell 腳本，將全域參數升階至其他環境。 在 ARM 範本部署之前新增 Azure PowerShell DevOps 工作。 在 DevOps 工作中，您必須指定新參數檔案、目標資源群組和目標 data factory 的位置。

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
* 瞭解如何使用 [控制流程運算式語言](control-flow-expression-language-functions.md)