---
title: 移動 Microsoft 資源的區域
description: 示範如何將位於 Microsoft .Resources 命名空間中的資源移動到新的區域。
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88951050"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>將 Microsoft .Resources 資源移至新區域

您可能需要將現有資源移至新的區域。 本文說明如何移動位於 Microsoft .Resources 命名空間的兩個資源類型： templateSpecs 和 deploymentScripts。

## <a name="move-template-specs-to-new-region"></a>將範本規格移至新區域

如果您在一個區域中有一個 [範本規格](../templates/template-specs.md) ，而且想要將它移到新的區域，您可以匯出範本規格並重新部署它。

1. 使用命令匯出現有的範本規格。針對參數值，提供符合您想要匯出之範本規格的值。

   如果是 Azure PowerShell，請使用：

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   對於 Azure CLI，請使用：

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. 使用匯出的範本規格來建立新的範本規格。下列範例顯示 `westus` 新區域，但您可以提供所需的區域。

   如果是 Azure PowerShell，請使用：

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   對於 Azure CLI，請使用：

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>將部署腳本移至新區域

1. 選取包含您想要移至新區域之部署腳本的資源群組。

1. [匯出範本](../templates/export-template-portal.md)。 匯出時，請選取部署腳本和任何其他必要的資源。

1. 在匯出的範本中，刪除下列屬性：

   * tenantId
   * principalId
   * clientId

1. 匯出的範本會有部署腳本區域的硬式編碼值。

   ```json
   "location": "westus2",
   ```

   變更範本以允許設定位置的參數。 如需詳細資訊，請參閱 [在 ARM 範本中設定資源位置](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [部署匯出的範本](../templates/deploy-powershell.md) ，並指定部署腳本的新區域。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何將資源移至新的資源群組或訂用帳戶，請參閱 [將資源移至新的資源群組或訂用](move-resource-group-and-subscription.md)帳戶。
* 若要瞭解如何將資源移至新的區域，請參閱 [跨區域移動 Azure 資源](move-region.md)。
