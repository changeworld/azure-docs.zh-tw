---
title: 在 Azure DevTest Labs 中部署嵌套的範本環境
description: 瞭解如何部署嵌套的 Azure Resource Manager 範本，以提供 Azure DevTest Labs 的環境。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 39002e286fafd4f813333a14ed86256a517897e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481335"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>部署適用于測試環境的嵌套 Azure Resource Manager 範本
嵌套部署可讓您從主要 Resource Manager 範本內執行其他 Azure Resource Manager 範本。 它可讓您將部署分解成一組目標和特定用途的範本。 它提供測試、重複使用及可讀性方面的優點。 在 [部署 Azure 資源時使用連結的範本](../azure-resource-manager/templates/linked-templates.md) ，可讓您大致瞭解此解決方案的數個程式碼範例。 本文提供 Azure DevTest Labs 特定的範例。 

## <a name="key-parameters"></a>金鑰參數
雖然您可以從頭開始建立自己的 Resource Manager 範本，但我們建議您在 Visual Studio 中使用 [Azure 資源群組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) ，這可讓您輕鬆地開發和偵錯工具範本。 當您將嵌套部署資源加入至 azuredeploy.js時，Visual Studio 會新增數個專案，讓範本更具彈性。 這些專案包括具有次要範本和參數檔案的子資料夾、主要範本檔案內的變數名稱，以及新檔案儲存位置的兩個參數。 **_ArtifactsLocation**和 **_ArtifactsLocationSasToken**是 DevTest Labs 使用的主要參數。 

如果您不熟悉 DevTest Labs 與環境的運作方式，請參閱 [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。 您的範本會儲存在連結至 DevTest Labs 中的實驗室的儲存機制中。 當您使用這些範本建立新的環境時，這些檔案會移至實驗室中 Azure 儲存體的容器。 為了能夠識別和複製嵌套的檔案，DevTest Labs 會識別 _artifactsLocation 並 _artifactsLocationSasToken 參數，並將子資料夾複製到儲存體容器。 然後，它會自動將位置和共用存取簽章 (SaS) 權杖插入參數中。 

## <a name="nested-deployment-example"></a>嵌套部署範例
以下是一個簡單的嵌套部署範例：

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

存放庫中包含此範本的資料夾會有一個子資料夾， `nestedtemplates` 其中包含 **NestOne.js** 的檔案，並 **NestOne.parameters.js**。 在的 **azuredeploy.js**中，範本的 URI 是使用成品位置、嵌套的範本資料夾、嵌套的範本檔案名所建立。 同樣地，參數的 URI 是使用內建範本的成品位置、嵌套的範本資料夾和參數檔來建立。 

以下是 Visual Studio 中相同專案結構的影像： 

![Visual Studio 中的專案結構](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

您可以在主資料夾中加入其他資料夾，但不能在單一層級上新增任何更多資料夾。 

## <a name="next-steps"></a>接下來的步驟
如需環境的詳細資訊，請參閱下列文章： 

- [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
- [在 Azure DevTest Labs 中將環境連線到實驗室的虛擬網路](connect-environment-lab-virtual-network.md)