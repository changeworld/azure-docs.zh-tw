---
title: 使用 Cloud Shell 部署範本
description: 使用 Azure Resource Manager 和 Cloud Shell 將資源部署到 Azure。 資源會定義在 Azure Resource Manager 範本中。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d0b519955cb877f5fb9640cc0cf95a898e1743ef
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681406"
---
# <a name="deploy-arm-templates-from-cloud-shell"></a>從 Cloud Shell 部署 ARM 範本

您可以使用 [Cloud Shell](../../cloud-shell/overview.md) 部署 Azure Resource Manager 範本 (ARM 範本) 。 您可以部署儲存在遠端的 ARM 範本，或是儲存在本機儲存體帳戶中 Cloud Shell 的 ARM 範本。

您可以部署到任何範圍。 本文說明如何部署至資源群組。

## <a name="deploy-remote-template"></a>部署遠端範本

若要部署外部範本，請提供範本的 URI，就如同您對任何外部部署所做的一樣。 外部範本可能位於 GitHub 存放庫中，也可能是外部儲存體帳戶。

1. 開啟 Cloud Shell 提示。

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="開啟 Cloud Shell":::

1. 若要部署範本，請使用下列命令：

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>部署本機範本

若要部署本機範本，您必須先將您的範本上傳至連線至 Cloud Shell 會話的儲存體帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取您的 Cloud Shell 資源群組。 名稱模式為 `cloud-shell-storage-<region>`。

   ![選取資源群組](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. 選取 Cloud Shell 的儲存體帳戶。

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="開啟 Cloud Shell":::

1. 選取 [檔案 **共用** ]。

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="開啟 Cloud Shell":::

1. 選取 Cloud Shell 的預設檔案共用。 檔案共用的名稱格式為 `cs-<user>-<domain>-com-<uniqueGuid>` 。

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="開啟 Cloud Shell":::

1. 加入新的目錄來保存您的範本。 選取該目錄。

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="開啟 Cloud Shell":::

1. 選取 [上傳] 。

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="開啟 Cloud Shell":::

1. 尋找並上傳您的範本。

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="開啟 Cloud Shell":::

1. 開啟 Cloud Shell 提示。

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="開啟 Cloud Shell":::

1. 流覽至 **clouddrive** 目錄。 流覽至您為了保存範本所加入的目錄。

1. 若要部署範本，請使用下列命令：

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>後續步驟

- 如需部署命令的詳細資訊，請參閱 [使用 arm 範本部署資源和 Azure CLI](deploy-cli.md) ，以及 [使用 arm 範本和 Azure PowerShell 部署資源](deploy-powershell.md)。
- 若要在部署範本之前預覽變更，請參閱 [ARM 範本部署假設](template-deploy-what-if.md)作業。
