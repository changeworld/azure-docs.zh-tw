---
title: '使用適用于 Linux Vm 的 Azure 映射產生器，以允許存取現有的 Azure VNET (預覽版) '
description: 使用 Azure 映射建立器建立 Linux VM 映射，以允許存取現有的 Azure VNET
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: cbff2358dcf8685298e31f18c430c35ec7de4948
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437332"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>使用適用于 Linux Vm 的 Azure 映射產生器，以允許存取現有的 Azure VNET

本文說明如何使用 Azure 映射產生器來建立可存取 VNET 上現有資源的基本自訂 Linux 映射。 您所建立的組建 VM 會部署到您在訂用帳戶中指定的新或現有的 VNET。 當您使用現有的 Azure VNET 時，Azure 映射產生器服務不需要公用網路連線能力。

> [!IMPORTANT]
> Azure Image Builder 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>註冊各項功能

首先，您必須註冊 Azure 映射建立器服務。 註冊會授與服務許可權，以建立、管理及刪除預備資源群組。 服務也有許可權可新增映射組建所需的群組資源。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>設定變數和授權 

您將會重複使用部分資訊。 建立一些變數來儲存該資訊。

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

建立資源群組。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>設定網路功能

如果您沒有現有的 VNET\Subnet\NSG，請使用下列腳本來建立一個。

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>新增網路安全性群組規則

此規則可讓您從 Azure 映射產生器的負載平衡器連線到 proxy VM。 埠60001適用于 Linux OSs，埠60000適用于 Windows Os。 Proxy VM 會使用適用于 Linux OSs 的埠22或適用于 Windows Os 的埠5986來連線至組建 VM。

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>在子網上停用私人服務原則

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

如需有關 Image Builder 網路功能的詳細資訊，請參閱 [Azure 映射產生器服務網路功能選項](image-builder-networking.md)。

## <a name="modify-the-example-template-and-create-role"></a>修改範例範本並建立角色

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>設定資源群組的許可權

Image Builder 會使用所提供的[使用者身分識別](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)，來將映像插入 Azure 共用映像庫 (SIG)。 在此範例中，您將建立 Azure 角色定義，其中包括將映像發佈到 SIG 的細微動作。 然後此將角色定義指派給使用者身分識別。

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

您可以建立兩個角色，而不是授與 Image Builder 更低的細微性和提高許可權。 其中一個會提供建立程式的建立者許可權，另一個則允許它將組建 VM 和負載平衡器連線至您的 VNET。

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

如需許可權的詳細資訊，請參閱 [使用 Azure CLI 設定 Azure 映射產生器服務許可權](image-builder-permissions-cli.md) 或 [使用 PowerShell 設定 azure 映射產生器服務許可權](image-builder-permissions-powershell.md)。

## <a name="create-the-image"></a>建立映像

將映像設定提交至 Azure Image Builder 服務。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

啟動映像建置。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

建立映像並將其同時複寫到兩個區域，可能需要一些時間。 請等候此部分完成，再繼續建立 VM。


## <a name="create-the-vm"></a>建立 VM

根據 Azure Image Builder 所建立的映像版本建立 VM。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

SSH 連線 至VM。

```bash
ssh aibuser@<publicIpAddress>
```

當您建立 SSH 連線時，您應該會看到映像是以 *當天的訊息* 進行自訂！

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>清除資源

如果您想要立即嘗試 recustomizing 映射版本以建立相同映射的新版本，請略過下一個步驟，並繼續 [使用 Azure 映射產生器來建立另一個映射版本](image-builder-gallery-update-image-version.md)。


以下會刪除已建立的映射，以及所有其他資源檔。 在刪除資源前，請先確定您已完成此部署。

刪除映像庫資源時，您必須先刪除所有映像版本，才能刪除用來建立它們的映像定義。 若要刪除資源庫，您必須先刪除資源庫中的所有映像定義。

刪除映像建立器範本。

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

刪除許可權指派、角色和身分識別
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

刪除資源群組。

```azurecli-interactive
az group delete -n $imageResourceGroup
```

如果您已在此快速入門中建立 VNET，您可以刪除不再使用的 VNET。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure 共用映像資源庫](shared-image-galleries.md)。