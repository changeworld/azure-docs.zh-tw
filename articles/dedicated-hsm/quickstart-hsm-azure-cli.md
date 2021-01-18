---
title: 快速入門：使用 Azure CLI 建立 Azure 專用 HSM
description: 使用 Azure CLI 建立、顯示、列出、更新和刪除 Azure 專用 HSM。
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020850"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure 專用 HSM

本文說明如何使用 [az dedicated-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm) Azure CLI 擴充功能來建立及管理 Azure 專用 HSM。

## <a name="prerequisites"></a>先決條件

- Azure 訂用帳戶。 您可以[建立免費帳戶](https://azure.microsoft.com/free/) (如果沒有的話)。
  
  如果您有多個 Azure 訂用帳戶，請使用 Azure CLI [az account set](/cli/azure/account#az_account_set) 命令來設定要用於計費的訂用帳戶。
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- 專用 HSM 符合的所有需求，包括註冊、核准，以及用於佈建的虛擬網路和虛擬機器。 如需專用 HSM 需求和必要條件的詳細資訊，請參閱[教學課程：使用 Azure CLI 將 HSM 部署至現有的虛擬網路](tutorial-deploy-hsm-cli.md)。
  

## <a name="create-a-resource-group"></a>建立資源群組

[Azure 資源群組](../azure-resource-manager/management/overview.md)是一個邏輯容器，可供以群組形式部署和管理 Azure 資源。 如果您還沒有專用 HSM 的資源群組，請使用 [az group create](/cli/azure/group#az_group_create) 命令建立一個。 下列範例會在 `westus` Azure 區域中建立名為 `myRG` 的資源群組：

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>建立專用的 HSM

若要建立專用 HSM，請使用 [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) 命令。 下列範例會在 `westus` 區域、`myRG` 資源群組，以及指定的訂用帳戶、虛擬網路和子網路中，佈建名為 `hsm1` 的專用 HSM。 必要參數為 `name`、`location` 和 `resource group`。

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

部署需要大約 25 到 30 分鐘才能完成。

## <a name="get-a-dedicated-hsm"></a>取得專用 HSM

若要取得目前的專用 HSM，請執行 [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) 命令。 下列範例可取得 `myRG` 資源群組中的 `hsm1` 專用 HSM。

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>更新專用 HSM

使用 [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) 命令來更新專用 HSM。 下列範例可更新 `myRG` 資源群組中的 `hsm1` 專用 HSM，以及其標記：

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>列出專用 HSM

執行 [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) 命令，以取得目前專用 HSM 的相關資訊。 下列範例可列出 `myRG` 資源群組中的專用 HSM：

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>移除專用 HSM

若要移除專用 HSM，請使用 [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) 命令。 下列範例可從 `myRG` 資源群組中刪除 `hsm1` 專用 HSM：

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>刪除資源群組

如果您不再需要為專用 HSM 建立的資源群組，您可執行 [az group delete](/cli/azure/group#az_group_delete) 命令予以刪除。 此命令可刪除群組及其中的所有資源，包括與專用 HSM 無關的任何資源。 下列範例可刪除 `myRG` 資源群組及其中的一切：

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 專用 HSM，請參閱 [Azure 專用 HSM](overview.md)。
