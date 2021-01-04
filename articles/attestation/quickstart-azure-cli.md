---
title: 使用 Azure CLI 設定 Azure 證明
description: 如何使用 Azure CLI 安裝和設定證明提供者。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: fb8b0f12844ce1057bd3cfc4716a32ee64ec5586
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937214"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>快速入門：使用 Azure CLI 設定 Azure 證明

使用 Azure CLI 設定證明，以開始使用 Azure 證明。

## <a name="get-started"></a>開始使用

1. 使用下列 CLI 命令安裝此擴充功能

   ```azurecli
   az extension add --name attestation
   ```
   
1. 檢查版本

   ```azurecli
   az extension show --name attestation --query version
   ```

1. 使用下列命令以登入 Azure：

   ```azurecli
   az login
   ```

1. 如有需要，請切換至 Azure 證明的訂用帳戶：

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. 使用 [az provider register](/cli/azure/provider#az_provider_register) 命令，在訂用帳戶中註冊 Microsoft.Attestation 資源提供者：

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   如需關於 Azure 資源提供者和如何加以設定及管理的詳細資訊，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。

   > [!NOTE]
   > 一個訂用帳戶只需註冊一個資源提供者。

1. 為證明提供者建立資源群組。 您可以將其他 Azure 資源放在相同的資源群組中，包括具有用戶端應用程式執行個體的虛擬機器。 執行 [az group create](/cli/azure/group#az_group_create) 命令以建立資源群組，或使用現有的資源群組：

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>建立和管理證明提供者

以下是可用來建立和管理證明提供者的命令：

1. 執行 [az attestation create](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_create) 命令，以建立證明提供者：

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. 執行 [az attestation show](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_show) 命令以擷取證明提供者屬性，例如 status 和 AttestURI：

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   此命令會顯示類似於下列輸出的值：

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

您可以使用 [az attestation delete](/cli/azure/ext/attestation/attestation?view=azure-cli-latest#ext_attestation_az_attestation_delete) 命令來刪除證明提供者：

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>原則管理

使用此處說明的命令，為證明提供者提供原則管理 (一次一個證明類型)。

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_show) 命令會針對指定的 TEE 傳回目前的原則：

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> 此命令會同時以文字和 JWT 格式顯示原則。

以下是支援的 TEE 類型：

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

使用 [az attestation policy set](/cli/azure/ext/attestation/attestation/policy?view=azure-cli-latest#ext_attestation_az_attestation_policy_set) 命令，為指定的證明類型設定新原則。

若要使用檔案路徑為指定證明類型設定文字格式的原則：

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

若要使用檔案路徑為指定證明類型設定 JWT 格式的原則：

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>後續步驟

- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用程式碼範例實作 SGX 記憶體保護區的證明](/samples/browse/?expanded=azure&terms=attestation)
