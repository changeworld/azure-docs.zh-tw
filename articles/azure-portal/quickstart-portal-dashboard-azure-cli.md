---
title: 使用 Azure CLI 建立 Azure 入口網站儀表板
description: 快速入門：了解如何使用 Azure CLI 在 Azure 入口網站中建立儀表板。 儀表板是您雲端資源的聚焦及經過組織的檢視。
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ede915df5cd2967c3b6b700bcb9174c89af8f233
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745650"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure 入口網站儀表板

Azure 入口網站中的儀表板是您雲端資源的聚焦及經過組織的檢視。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 如果您有多個 Azure 訂用帳戶，請選擇為資源計費的適當訂用帳戶。
使用 [az account set](/cli/azure/account#az_account_set) 命令來選取訂用帳戶：

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- 使用 [az group create](/cli/azure/group#az_group_create) 命令或使用現有的資源群組建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)：

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令來建立虛擬機器：

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> 必須使用複雜密碼。
> 這是新的使用者名稱和密碼；
> 而不是您用來登入 Azure 的帳戶。
> 如需詳細資訊，請參閱[使用者名稱需求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)和[密碼需求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

部署開始後，通常需要數分鐘才能完成。
部署完成之後，請移至下一節。

## <a name="download-the-dashboard-template"></a>下載儀表板範本

因為 Azure 儀表板屬於資源，所以能夠以 JSON 的形式呈現。
如需詳細資訊，請參閱 [Azure 儀表板的結構](./azure-portal-dashboards-structure.md)。

下載下列檔案：[portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json)。

以您的值取代下列值，自訂已下載的範本：

* `<subscriptionID>`：您的訂用帳戶
* `<rgName>`：資源群組，例如 `myResourceGroup`
* `<vmName>`：虛擬機器名稱，例如 `SimpleWinVM`
* `<dashboardTitle>`：儀表板標題，例如 `Simple VM Dashboard`
* `<location>`：Azure 區域，例如 `centralus`

如需詳細資訊，請參閱 [Microsoft 入口網站儀表板範本參考](/azure/templates/microsoft.portal/dashboards)。

## <a name="deploy-the-dashboard-template"></a>部署儀表板範本

您現在可以在 Azure CLI 中部署範本。

1. 執行 [az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) 命令以部署範本：

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. 執行 [az portal dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) 命令，檢查是否已成功建立儀表板：

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

若要查看目前訂用帳戶的所有儀表板，請使用 [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list)：

```azurecli
az portal dashboard list
```

您也可以查看資源群組的所有儀表板：

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

您可以使用 [az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) 命令來更新儀表板：

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

確認您可以從 Azure 入口網站中查看虛擬機器的相關資料。

1. 在 Azure 入口網站中，選取 [儀表板]。

   ![Azure 入口網站導覽至儀表板](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. 在儀表板頁面上，選取 [簡單的 VM 儀表板]。

   ![瀏覽至簡單的 VM 儀表板](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. 檢閱儀表板。 您可以看到某些內容是靜態的，但是也有圖表顯示 VM 效能。

   ![檢閱簡單的 VM 儀表板](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>清除資源

若要移除虛擬機器和相關聯的儀表板，請刪除包含上述項目的資源群組。

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurecli
az group delete --name myResourceGroup
```

若只要移除儀表板，請使用 [az portal dashboard delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete) 命令：

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>後續步驟

如需儀表板 Azure CLI 支援的詳細資訊，請參閱 [az 入口網站儀表板](/cli/azure/ext/portal/portal/dashboard)。
