---
title: 建立虛擬機器映射，並使用使用者指派的受控識別來存取 Azure 儲存體中的檔案（預覽）
description: 使用 Azure 映射產生器建立虛擬機器映射，可以使用使用者指派的受控識別來存取儲存在 Azure 儲存體中的檔案。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 36016e462e3f4906c4dfe8c58501c82fd554f3bd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720583"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>建立映射，並使用使用者指派的受控識別來存取 Azure 儲存體中的檔案 

Azure 映射產生器支援使用腳本，或從多個位置複製檔案，例如 GitHub 和 Azure 儲存體等。若要使用這些功能，它們必須可從外部存取 Azure 映射產生器，但您可以使用 SAS 權杖來保護 Azure 儲存體 blob。

本文說明如何使用 Azure VM 映射產生器來建立自訂映射，其中服務會使用[使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)來存取 Azure 儲存體中的檔案以進行映射自訂，而不必讓檔案可公開存取，或設定 SAS 權杖。

在下列範例中，您將建立兩個資源群組，一個將用於自訂映射，另一個則主控包含腳本檔案的 Azure 儲存體帳戶。 這會模擬真實的案例，在此情況下，您可能會在映射產生器以外的不同儲存體帳戶中有組建成品或影像檔案。 您將建立使用者指派的身分識別，然後授與該腳本檔案的讀取權限，但您不會設定該檔案的任何公用存取權。 接著，您將使用 Shell 自訂程式，從儲存體帳戶下載並執行該腳本。


> [!IMPORTANT]
> Azure 映射產生器目前為公開預覽版。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>註冊功能
若要在預覽期間使用 Azure 映射產生器，您必須註冊新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

檢查功能註冊的狀態。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

檢查您的註冊。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果沒有顯示 [已註冊]，請執行下列動作：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>建立資源群組

我們會重複使用一些資訊，因此我們將建立一些變數來儲存該資訊。


```azurecli-interactive
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

建立訂用帳戶識別碼的變數。 您可以使用 `az account show | grep id`來取得。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

建立映射和腳本儲存體的資源群組。

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


建立儲存體，並從 GitHub 將範例腳本複製到其中。

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



授與映射產生器許可權，以在映射資源群組中建立資源。 `--assignee` 值是映射產生器服務的應用程式註冊識別碼。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>建立使用者指派的受控識別

建立身分識別，並指派腳本儲存體帳戶的許可權。 如需詳細資訊，請參閱[使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)。

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>修改範例

下載範例 json 檔案，並使用您建立的變數加以設定。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>建立映像

將映射設定提交至 Azure 映射產生器服務。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

啟動映射組建。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

等待建置完成。 這可能需要大約15分鐘的時間。

## <a name="create-a-vm"></a>建立 VM

從映射建立 VM。 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

建立 VM 之後，請啟動與 VM 的 SSH 會話。

```azurecli-interactive
ssh aibuser@<publicIp>
```

您應該會在建立 SSH 連線後，看到該映射已自訂一天的訊息！

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>清除

當您完成時，您可以刪除不再需要的資源。

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>後續步驟

如果您在使用 Azure 映射產生器時遇到任何問題，請參閱[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)。