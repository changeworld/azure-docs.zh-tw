---
title: 使用 Azure Resource Manager 範本建立 Azure 復原服務保存庫的快速入門。
description: 在本快速入門中，您會了解如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure 復原服務保存庫。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
ms.openlocfilehash: 62bfe71c397367282c696eecdf55ab9c900c69a1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88684111"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>快速入門：使用 ARM 範本建立復原服務保存庫

本快速入門會說明如何使用 Azure Resource Manager 範本 (ARM 範本) 設定 Azure 復原服務保存庫。 [Azure Site Recovery](site-recovery-overview.md) 服務會確保您的商務應用程式可在計劃性與非計劃性中斷期間持續運作，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有作用中的 Azure 訂用帳戶，可以在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/)。

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

範本中定義了兩個 Azure 資源：

- [Microsoft.RecoveryServices vaults](/azure/templates/microsoft.recoveryservices/vaults)：建立保存庫。
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs)：設定保存庫的備份備援設定。

此範本包含保存庫備份組態的選擇性參數。 儲存體備援設定是本地備援儲存體 (LRS) 或異地備援儲存體 (GRS)。 如需詳細資訊，請參閱[設定儲存體備援](../backup/backup-create-rs-vault.md#set-storage-redundancy)。

如需更多 Azure 復原服務範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署範本

若要部署範本，需要**訂用帳戶**、**資源群組**和**保存庫名稱**。

1. 選取 [部署至 Azure] 映像以登入 Azure 並開啟範本。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. 選取或輸入下列值：

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="建立復原服務保存庫的範本":::

   - **訂用帳戶**：選取您的 Azure 訂用帳戶。
   - **資源群組**：選取現有的群組，或選取 [建立新的] 以新增群組。
   - **位置**：預設為資源群組的位置，在選取資源群組後無法使用。
   - **保存庫名稱**：提供保存庫名稱。
   - **變更儲存體類型**：預設值為 **false**。 只有在需要變更保存庫的儲存體類型時，才選取 [True]。
   - **保存庫儲存體類型**：預設值為 **GloballyRedundant**。 如果儲存體類型設為 **True**，請選取 [LocallyRedundant]。
   - **位置**：函式 `[resourceGroup().location]` 預設為資源群組的位置。 若要變更位置，請輸入值，例如 **westus**。
   - 選取 [我同意上方所述的條款及條件] 核取方塊。

1. 若要開始保存庫的部署，請選取 [購買] 按鈕。 部署成功後，就會顯示通知。

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="建立復原服務保存庫的範本":::

## <a name="validate-the-deployment"></a>驗證部署

若要確認已建立保存庫，請使用 Azure CLI 或 Azure PowerShell。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

下列輸出是保存庫資訊的摘要：

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>清除資源

如果您計畫使用新的資源，則不需要採取任何動作。 否則，您可以移除在本快速入門中建立的資源群組和保存庫。 若要刪除資源群組和其資源，請使用 Azure CLI 或 Azure PowerShell。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立復原服務保存庫。 若要深入司解災害復原，請繼續參閱下一篇快速入門文章。

> [!div class="nextstepaction"]
> [設定災害復原](azure-to-azure-quickstart.md)
