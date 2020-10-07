---
title: 在您的組織外部共用 (ARM 範本) - Azure Data Share 快速入門
description: 了解如何使用本快速入門中的 Azure Data Share 和 Resource Manager 範本來與客戶及合作夥伴共用資料。
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 76ef44d438b9af7ada6c1c464705a22ee10f4c58
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88654101"
---
# <a name="quickstart-share-data-using-azure-data-share-and-resource-manager-templates"></a>快速入門：使用 Azure Data Share 和 Resource Manager 範本共用資料

使用 Azure Resource Manager 範本，了解如何在 Azure 儲存體帳戶中設定新的 Azure Data Share，並開始與 Azure 組織外的客戶及合作夥伴共用資料。 如需支援的資料存放區清單，請參閱 [Azure Data Share 中支援的資料存放區](./supported-data-stores.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/)。

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

範本中會定義下列資源：

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)：
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts](/rest/api/datashare/accounts/create)
* [Microsoft.DataShare/accounts/shares](/rest/api/datashare/shares/create)
* [Microsoft.DataShare/accounts/shares/dataSets](/rest/api/datashare/datasets/create)
* [Microsoft.DataShare/accounts/shares/invitations](/rest/api/datashare/invitations/create)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

這個範本會執行下列作業：

* 建立儲存體帳戶和用來作為資料共用資料來源的容器。
* 建立 Data Share 帳戶和資料共用。
* 建立角色指派，將儲存體 Blob 資料讀者角色授與來源資料共用資源。 請參閱 [Azure Data Share 的角色和需求](./concepts-roles-permissions.md)。
* 將資料集新增至資料共用。
* 將收件者新增至資料共用。
* 為資料共用啟用快照集排程。

此範本是針對學習用途而建立的。 在實務上，現有的儲存體帳戶中通常會有一些資料。 您必須先建立角色指派，才能執行範本或指令碼來建立資料集。 有時候在部署範本時，可能會收到下列錯誤訊息：

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

這是因為在 RBAC 指派完成之前，部署嘗試建立資料集。 儘管出現錯誤訊息，部署還是會成功。  您仍然可以依[檢閱已部署的資源](#review-deployed-resources)來逐步完成。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. 選取或輸入下列值：

    * **訂用帳戶**：選取用來建立資料共用和其他資源的 Azure 訂用帳戶。
    * **資源群組**：選取 [新建] 以建立新資源群組或選取現有的資源群組。
    * **位置**：選取資源群組的位置。
    * **專案名稱**：輸入專案名稱。  專案名稱可用來產生資源名稱。  請參閱先前範本中的變數定義。
    * **位置**：選取資源的位置。  您可以讓資源群組使用相同的位置。
    * **邀請電子郵件**：輸入資料共用收件者的 Azure 登入電子郵件地址。  電子郵件別名無法使用。

    其餘設定請使用預設值。
1. 選取 [我同意上方所述的條款及條件]，然後選取 [購買]。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 開啟您建立的資料共用帳戶。
1. 在左側功能表中，選取 [傳送共用]。  您應該會看到系統列出了儲存體帳戶。
1. 選取 [儲存體帳戶]。  在**詳細資料**下，您應該會看到在範本中設定的同步處理設定。

    ![Azure Data Share 儲存體帳戶同步處理設定](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. 從頂端選取 [邀請]。 您應該會看到在部署範本時所指定的電子郵件地址。 **狀態**應該是**暫止**。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除資源群組，這會刪除資源群組中的資源。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Data Share 並邀請收件者。 若要深入了解資料取用者要如何接受和接收資料共用，請繼續閱讀[接受和接收資料](subscribe-to-data-share.md)教學課程。
