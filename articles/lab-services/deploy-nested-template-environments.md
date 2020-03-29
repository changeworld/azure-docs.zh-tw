---
title: 在 Azure 開發人員測試實驗室中部署嵌套範本環境
description: 瞭解如何部署嵌套 Azure 資源管理器範本以使用 Azure 開發人員測試實驗室提供環境。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168830"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>為測試環境部署嵌套 Azure 資源管理器範本
嵌套部署允許您從主資源管理器範本中執行其他 Azure 資源管理器範本。 它使您能夠將部署分解為一組針對的目標和特定于目的的範本。 它在測試、重用和可讀性方面提供了優勢。 在[部署 Azure 資源時使用連結範本](../azure-resource-manager/templates/linked-templates.md)的文章提供了此解決方案的良好概述，並包含多個代碼示例。 本文提供了特定于 Azure 開發人員測試實驗室的示例。 

## <a name="key-parameters"></a>關鍵參數
雖然可以從頭開始創建自己的資源管理器範本，但我們建議您在 Visual Studio 中使用[Azure 資源組專案](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)，這樣可以輕鬆地開發和調試範本。 將嵌套部署資源添加到 azuredeploy.json 時，Visual Studio 會添加多個項以使範本更加靈活。 這些專案包括包含輔助範本和參數檔的子資料夾、主範本檔中的變數名稱以及新檔的存儲位置的兩個參數。 **_artifactsLocation**和 **_artifactsLocationSasToken**是 DevTest 實驗室使用的關鍵參數。 

如果您不熟悉 DevTest Labs 如何使用環境，請參閱[使用 Azure 資源管理器範本創建多 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。 範本存儲在連結到 DevTest 實驗室實驗室的存儲庫中。 使用這些範本創建新環境時，檔將移動到實驗室中的 Azure 存儲容器中。 為了能夠識別和複製嵌套檔，DevTest Labs 標識_artifactsLocation，並_artifactsLocationSasToken參數並將子資料夾複製到存儲容器。 然後，它會自動將位置和共用訪問簽名 （SaS） 權杖插入到參數中。 

## <a name="nested-deployment-example"></a>嵌套部署示例
下面是嵌套部署的簡單示例：

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

存儲庫中包含此範本的資料夾有一個子資料夾`nestedtemplates`，其中包含檔**NestOne.json**和**NestOne.parameter.json**。 在**azuredeploy.json**中，範本的 URI 使用專案位置、嵌套範本資料夾、嵌套範本檔案名生成。 同樣，使用嵌套範本的專案位置、嵌套範本資料夾和參數檔構建參數的 URI。 

以下是 Visual Studio 中相同專案結構的圖像： 

![Visual Studio 中的專案結構](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

您可以在主資料夾中添加其他資料夾，但不能比單個級別更深。 

## <a name="next-steps"></a>後續步驟
有關環境的詳細資訊，請參閱以下文章： 

- [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
- [在 Azure 開發人員測試實驗室中將環境連接到實驗室的虛擬網路](connect-environment-lab-virtual-network.md)