---
title: 使用 Azure CLI 設定 Azure 映射產生器服務許可權
description: 使用 Azure CLI 設定 Azure VM 映射產生器服務的需求，包括許可權和許可權
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 58bbe01c8de0bbe606f4fc428032cd213f05d386
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068106"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>使用 Azure CLI 設定 Azure 映射產生器服務許可權

在建立映射之前，Azure 映射產生器服務需要設定許可權和許可權。 下列各節將詳細說明如何使用 Azure CLI 設定可能的案例。

> [!IMPORTANT]
> Azure Image Builder 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>註冊各項功能

首先，您必須註冊 Azure 映射產生器服務。 註冊會授與服務許可權，以建立、管理和刪除暫存資源群組。 此服務也具有將映射組建所需的群組加入資源的許可權。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>建立 Azure 使用者指派的受控識別

Azure 映射產生器會要求您建立[azure 使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。 Azure 映射產生器會使用使用者指派的受控識別來讀取影像、寫入映射，以及存取 Azure 儲存體帳戶。 您會授與身分識別許可權，以便在您的訂用帳戶中執行特定動作。

> [!NOTE]
> 先前，Azure 映射產生器會使用 Azure 映射產生器服務主體名稱 (SPN) ，將許可權授與映射資源群組。 使用 SPN 將會被取代。 請改用使用者指派的受控識別。

下列範例說明如何建立 Azure 使用者指派的受控識別。 取代預留位置設定以設定您的變數。

| 設定 | 描述 |
|---------|-------------|
| \<Resource group\> | 要在其中建立使用者指派受控識別的資源群組。 |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

如需有關 Azure 使用者指派身分識別的詳細資訊，請參閱[azure 使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)檔，以瞭解如何建立身分識別。

## <a name="allow-image-builder-to-distribute-images"></a>允許影像產生器發佈影像

若要讓 Azure 映射產生器將映射發佈 (受控映射/共用映射資源庫) ，必須允許 Azure 映射產生器服務將映射插入這些資源群組。 若要授與所需的許可權，您必須建立使用者指派的受控識別，並將該映射建立所在之資源群組的許可權授與它。 Azure 映射產生器沒有許可權，**無法**存取訂用帳戶的其他資源群組中的資源。 您必須採取明確的動作，以允許存取，以避免您的組建失敗。

您不需要在資源群組上授與使用者指派的受控識別參與者許可權來散發映射。 不過，使用者指派的受控識別需要 `Actions` 發佈資源群組中的下列 Azure 許可權：

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

如果發佈到共用映射資源庫，您也需要：

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>自訂現有映射的許可權

若要讓 Azure 映射產生器從來源自訂映射建立映射 (受控映射/共用映射資源庫) ，必須允許 Azure 映射產生器服務將映射讀入這些資源群組中。 若要授與所需的許可權，您必須建立使用者指派的受控識別，並將該映射所在之資源群組的許可權授與它。

從現有的自訂映射建立：

```Actions
Microsoft.Compute/galleries/read
```

從現有的共用映射庫版本建立：

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>在您的 Vnet 上自訂影像的許可權

Azure 映射產生器具有在您的訂用帳戶中部署和使用現有 VNET 的功能，因此可讓您自訂對已連線資源的存取權。

您不需要在資源群組上授與使用者指派的受控識別參與者許可權，即可將 VM 部署至現有的 VNET。 不過，使用者指派的受控識別需要 `Actions` VNET 資源群組上的下列 Azure 許可權：

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>建立 Azure 角色定義

下列範例會從上一節所述的動作建立 Azure 角色定義。 這些範例適用于資源群組層級。 評估和測試範例是否已足夠細微地滿足您的需求。 針對您的案例，您可能需要將它精簡到特定的共用映射資源庫。

影像動作允許讀取和寫入。 決定適合您環境的內容。 例如，建立角色以允許 Azure 映射產生器從資源群組中讀取影像*範例-rg-1* ，並將映射寫入資源群組*範例-rg 2*。

### <a name="custom-image-azure-role-example"></a>自訂映射 Azure 角色範例

下列範例會建立 Azure 角色以使用和散發來源自訂映射。 接著，您會將自訂角色授與 Azure 映射產生器的使用者指派受控識別。

若要簡化範例中的值取代，請先設定下列變數。 取代預留位置設定以設定您的變數。

| 設定 | 描述 |
|---------|-------------|
| \<Subscription ID\> | 您的 Azure 訂用帳戶 ID |
| \<Resource group\> | 自訂映射的資源群組 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>現有的 VNET Azure 角色範例

下列範例會建立 Azure 角色以使用和散發現有的 VNET 映射。 接著，您會將自訂角色授與 Azure 映射產生器的使用者指派受控識別。

若要簡化範例中的值取代，請先設定下列變數。 取代預留位置設定以設定您的變數。

| 設定 | 描述 |
|---------|-------------|
| \<Subscription ID\> | 您的 Azure 訂用帳戶 ID |
| \<Resource group\> | VNET 資源群組 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>使用受控識別進行 Azure 儲存體存取

如果您想要完美地驗證 Azure 儲存體並使用私人容器，azure 映射產生器 Azure 需要使用者指派的受控識別。 Azure 映射產生器會使用身分識別來驗證 Azure 儲存體。

> [!NOTE]
> Azure 映射產生器只會在映射範本提交時間使用身分識別。 組建 VM 在映射組建期間無法存取身分識別。

使用 Azure CLI 建立使用者指派的受控識別。

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

在映射產生器範本中，您必須提供使用者指派的受控識別。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

取代下列預留位置設定：

| 設定 | 描述 |
|---------|-------------|
| \<Region\> | 範本區域 |
| \<Resource group\> | 資源群組 |
| \<Storage account container\> | 儲存體帳戶容器名稱 |
| \<Subscription ID\> | Azure 訂用帳戶 |

如需使用使用者指派的受控識別的詳細資訊，請參閱[建立自訂映射，將會使用 Azure 使用者指派的受控識別來完美地 access 檔案 Azure 儲存體](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage)。 本快速入門會逐步解說如何建立和設定使用者指派的受控識別，以存取儲存體帳戶。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Azure 映射](image-builder-overview.md)產生器總覽。