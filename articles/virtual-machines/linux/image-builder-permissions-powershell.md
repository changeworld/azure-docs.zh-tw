---
title: 使用 PowerShell 設定 Azure 映射產生器服務許可權
description: 使用 PowerShell 設定 Azure VM Image Builder 服務的需求，包括許可權和許可權
author: danielsollondon
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 4f617d680b42a2bb7590cf5aaed657c0469f811d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882411"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>使用 PowerShell 設定 Azure 映射產生器服務許可權

Azure 映射產生器服務需要先設定許可權和許可權，才能建立映射。 下列各節詳細說明如何使用 PowerShell 設定可能的案例。

> [!IMPORTANT]
> Azure Image Builder 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>註冊各項功能

首先，您必須註冊 Azure 映射建立器服務。 註冊會授與服務許可權，以建立、管理及刪除預備資源群組。 服務也有許可權可新增映射組建所需的群組資源。

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>建立 Azure 使用者指派的受控識別

Azure Image Builder 需要您建立 [azure 使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。 Azure 映射產生器會使用使用者指派的受控識別來讀取映射、寫入映射，以及存取 Azure 儲存體帳戶。 您會授與身分識別許可權，以在您的訂用帳戶中執行特定動作。

> [!NOTE]
> 先前，Azure 映射產生器使用 Azure 映射產生器服務主體名稱 (SPN) 來授與映射資源群組的許可權。 使用 SPN 將會被取代。 請改用使用者指派的受控識別。

下列範例說明如何建立 Azure 使用者指派的受控識別。 取代預留位置設定以設定您的變數。

| 設定 | 描述 |
|---------|-------------|
| \<Resource group\> | 要在其中建立使用者指派受控識別的資源群組。 |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

如需有關 Azure 使用者指派之身分識別的詳細資訊，請參閱 [azure 使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 檔，以瞭解如何建立身分識別。

## <a name="allow-image-builder-to-distribute-images"></a>允許影像產生器發佈影像

若要讓 Azure 映射產生器將映射發佈 (受控映射/共用映射庫) ，必須允許 Azure 映射建立器服務將映射插入這些資源群組。 若要授與所需的許可權，您必須建立使用者指派的受控識別，並授與建立映射之資源群組的許可權。 Azure **Image Builder 沒有存取訂用帳戶** 中其他資源群組中資源的許可權。 您必須採取明確的動作以允許存取，以避免您的組建失敗。

您不需要在資源群組上授與使用者指派的受控識別參與者許可權來發佈映射。 不過，使用者指派的受控識別需要在 `Actions` 散發資源群組中具有下列 Azure 許可權：

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

如果要發佈至共用映射庫，您也需要：

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>自訂現有映射的許可權

若要讓 Azure 映射產生器從來源自訂映射建立映射 (受控映射/共用映射庫) ，必須允許 Azure 映射建立器服務將映射讀入這些資源群組。 若要授與所需的許可權，您必須建立使用者指派的受控識別，並授與該映射所在資源群組的許可權。

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

## <a name="permission-to-customize-images-on-your-vnets"></a>在 Vnet 上自訂影像的許可權

Azure 映射產生器能夠在您的訂用帳戶中部署和使用現有的 VNET，進而允許自訂連線資源的存取權。

您不需要在資源群組上授與使用者指派的受控識別參與者許可權，即可將 VM 部署至現有的 VNET。 不過，使用者指派的受控識別需要 `Actions` VNET 資源群組的下列 Azure 許可權：

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>建立 Azure 角色定義

下列範例會從上一節所述的動作建立 Azure 角色定義。 這些範例會套用至資源群組層級。 評估並測試範例是否夠細微，以滿足您的需求。 針對您的案例，您可能需要將它精簡至特定的共用映射庫。

影像動作允許讀取和寫入。 決定適合您環境的功能。 例如，建立角色以允許 Azure 映射產生器讀取資源群組 *範例-rg-1* 中的映射，並將影像寫入資源群組 *範例-rg-2*。

### <a name="custom-image-azure-role-example"></a>自訂映射 Azure 角色範例

下列範例會建立 Azure 角色，以使用並散發來源自訂映射。 然後，您會將自訂角色授與給 Azure 映射產生器的使用者指派受控識別。

為了簡化範例中的值取代，請先設定下列變數。 取代預留位置設定以設定您的變數。

| 設定 | 描述 |
|---------|-------------|
| \<Subscription ID\> | 您的 Azure 訂用帳戶 ID |
| \<Resource group\> | 自訂映射的資源群組 |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>現有的 VNET Azure 角色範例

下列範例會建立 Azure 角色，以使用並散發現有的 VNET 映射。 然後，您會將自訂角色授與給 Azure 映射產生器的使用者指派受控識別。

為了簡化範例中的值取代，請先設定下列變數。 取代預留位置設定以設定您的變數。

| 設定 | 描述 |
|---------|-------------|
| \<Subscription ID\> | 您的 Azure 訂用帳戶 ID |
| \<Resource group\> | VNET 資源群組 |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 映射](../image-builder-overview.md)建立器總覽。