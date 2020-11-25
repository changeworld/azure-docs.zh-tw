---
title: 使用 Azure CLI 設定 Azure 證明
description: 如何使用 Azure CLI 安裝和設定證明提供者。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5ffcd56be1ce7427697cd3a75bd7c4232cc3b8a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663968"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>快速入門：使用 Azure CLI 設定 Azure 證明

使用 Azure CLI 設定證明，以開始使用 Azure 證明。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>開始使用

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

依照下列程序建立和管理證明提供者。

1. 執行 [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) 命令，以建立證明提供者：

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   **--certs-input-path** 參數會指定一組受信任的簽署金鑰。 如果您為此參數指定檔案名稱，則必須使用已簽署 JWT 格式的原則來設定證明提供者。 若未指定，則可以使用文字或未簽署的 JWT 格式來設定原則。 如需 JWT 的相關資訊，請參閱[基本概念](basic-concepts.md)。 如需憑證範例，請參閱[證明原則簽署者憑證的範例](policy-signer-examples.md)。

1. 執行 [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) 命令以擷取證明提供者屬性，例如 status 和 AttestURI：

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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

您可以使用 [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) 命令來刪除證明提供者：

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>原則管理

若要管理原則，Azure AD 使用者必須具有 `Actions` 的下列權限：

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

您可以透過適當角色將這些權限指派給 Azure AD 使用者，例如 `Owner` (萬用字元權限)、`Contributor` (萬用字元權限) 或 `Attestation Contributor` (僅限 Azure 證明的特定權限) 角色。  

若要讀取原則，Azure AD 使用者必須具有 `Actions` 的下列權限：

- `Microsoft.Attestation/attestationProviders/attestation/read`

您可以透過適當角色將此權限指派給 Azure AD 使用者，例如 `Reader` (萬用字元權限) 或 `Attestation Reader` (僅限 Azure 證明的特定權限) 角色。

使用此處說明的命令，為證明提供者提供原則管理 (一次一個 TEE)。

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) 命令會針對指定的 TEE 傳回目前的原則：

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> 此命令會同時以文字和 JWT 格式顯示原則。

以下是支援的 TEE 類型：

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

使用 [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) 命令，為指定的 TEE 設定新的原則。

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

JWT 格式的證明原則必須包含名為 `AttestationPolicy` 的宣告。 對於已簽署的原則，必須使用與任何現有的原則簽署者憑證相對應的金鑰進行簽署。

如需原則範例，請參閱[證明原則的範例](policy-examples.md)。

[az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) 命令會為指定的 TEE 設定新的原則。

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>原則簽署者憑證管理

使用下列命令，管理證明提供者的原則簽署者憑證：

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

原則簽署者憑證是已簽署的 JWT，具有名為 `maa-policyCertificate` 的宣告。 宣告的值是 JWK，其中包含要新增的受信任簽署金鑰。 對於 JWT，必須使用與任何現有的原則簽署者憑證相對應的私密金鑰進行簽署。 如需 JWT 和 JWK 的相關資訊，請參閱[基本概念](basic-concepts.md)。

原則簽署者憑證的所有語意操作都必須在 Azure CLI 外部完成。 就 Azure CLI 而言，這是一個簡單的字串。

如需憑證範例，請參閱[證明原則簽署者憑證的範例](policy-signer-examples.md)。

## <a name="next-steps"></a>後續步驟

- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用程式碼範例實作 SGX 記憶體保護區的證明](/samples/browse/?expanded=azure&terms=attestation)
