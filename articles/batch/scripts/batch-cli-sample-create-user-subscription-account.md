---
title: Azure CLI 指令碼範例 - 建立 Batch 帳戶 - 使用者訂用帳戶
description: 此指令碼會在使用者訂用帳戶模式中建立 Azure Batch 帳戶。 此帳戶會將計算節點配置到您的訂用帳戶。
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: c9b8ba2ef782dcdc99cb18698175b8b53a53f0dd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076770"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI 範例：在使用者訂用帳戶模式中建立 Batch 帳戶

此指令碼會在使用者訂用帳戶模式中建立 Azure Batch 帳戶。 將計算節點配置到您的訂用帳戶的帳戶必須透過 Azure Active Directory 權杖驗證。 配置的計算節點會計入您訂用帳戶的 vCPU (核心) 配額。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教學課程需要 2.0.20 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。  

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az role assignment create](/cli/azure/role) | 為使用者、群組或服務主體建立新的角色指派。 |
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | 建立金鑰保存庫。 |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | 更新指定的 Key Vault 的安全性原則。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | 建立 Batch 帳戶。  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 對指定的 Batch 帳戶驗證以進行進一步的 CLI 互動。  |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。
