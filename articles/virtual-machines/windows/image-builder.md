---
title: '使用 Azure 映射建立器建立 Windows VM (預覽版) '
description: 使用 Azure 映射建立器建立 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: ca9f9ad0fb724884e865469ba118af4c7e6cea2f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202839"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>預覽：使用 Azure 映射建立器建立 Windows VM

本文說明如何使用 Azure VM 映射產生器來建立自訂的 Windows 映像。 本文 [中的範例會使用自](../linux/image-builder-json.md#properties-customize) 定義自訂映射：
- PowerShell (ScriptUri) -下載並執行 [PowerShell 腳本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)。
- Windows 重新開機-重新開機 VM。
- PowerShell (內嵌) -執行特定命令。 在此範例中，它會使用在 VM 上建立目錄 `mkdir c:\\buildActions` 。
- 將檔案從 GitHub 複製到 VM。 此範例會將 [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) 複製到 `c:\buildArtifacts\index.html` VM 上的。
- buildTimeoutInMinutes-增加組建時間以允許較長的執行組建，預設值為240分鐘，而您可以增加組建時間以允許較長的執行組建。
- vmProfile-指定 vmSize 和網路屬性
- osDiskSizeGB-您可以增加影像的大小
- 身分識別-提供 Azure 映射產生器在組建期間使用的身分識別


您也可以指定 `buildTimeoutInMinutes` 。 預設值為240分鐘，而您可以增加組建時間以允許較長的執行組建。

我們將會使用樣本 .json 範本來設定映像。 我們正在使用的 json 檔案如下： [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)。 


> [!IMPORTANT]
> Azure Image Builder 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="register-the-features"></a>註冊各項功能

若要在預覽期間使用 Azure Image Builder，您必須註冊新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

檢查功能註冊的狀態。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

檢查註冊。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

如果沒有顯示已註冊，請執行下列動作：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables"></a>設定變數

由於我們會重複使用某些資訊，因此我們將建立一些變數來儲存這些資訊。


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

為訂用帳戶識別碼建立變數。 您可以使用 `az account show | grep id` 取得此項目。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>建立資源群組
此資源群組是用來儲存映射設定範本成品和映射。


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>建立使用者指派的身分識別，並在資源群組上設定權限
影像產生器會使用提供的 [使用者身分識別](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) ，將映射插入資源群組中。 在此範例中，您將建立一個 Azure 角色定義，其具有執行發佈映射的細微動作。 然後此將角色定義指派給使用者身分識別。

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>建立使用者指派的受控識別並授與許可權 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>下載映射設定範本範例

已為您建立參數化映射設定範本以供您試用。 下載範例 json 檔案，並使用您先前設定的變數進行設定。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

您可以使用類似的文字編輯器，在終端機中修改這個範例 `vi` 。

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> 針對來源影像，您必須一律 [指定版本](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)，而不能使用 `latest` 。
> 如果您新增或變更要將映射發佈至其中的資源群組，您必須在資源群組上 [設定許可權](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) 。
 
## <a name="create-the-image"></a>建立映像

將映射設定提交至 VM 映射產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

完成時，這會將成功訊息傳回主控台，並 `Image Builder Configuration Template` 在中建立 `$imageResourceGroup` 。 如果您啟用 [顯示隱藏的類型]，您可以在 Azure 入口網站的資源群組中看到此資源。

在背景中，影像產生器也會在您的訂用帳戶中建立預備資源群組。 此資源群組會用於映射組建。 它的格式如下： `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> 您不得直接刪除暫存資源群組。 先刪除映射範本成品，這將會刪除暫存資源群組。

如果服務在映射設定範本提交期間報告失敗：
-  請參閱這些 [疑難排解](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) 步驟。 
- 您將需要先使用下列程式碼片段刪除範本，再重試提交。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>啟動映射組建
使用 [az 資源叫用-action](/cli/azure/resource#az-resource-invoke-action)來啟動影像建立流程。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

等候組建完成。 這可能需要大約15分鐘的時間。

如果您遇到任何錯誤，請參閱這些 [疑難排解](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) 步驟。


## <a name="create-the-vm"></a>建立 VM

使用您建立的映射來建立 VM。 *\<password>* 以您自己 `aibuser` 在 VM 上的密碼取代。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>確認自訂

使用您在建立 VM 時所設定的使用者名稱與密碼，建立與該 VM 的遠端桌面連線。 在 VM 中，開啟命令提示字元並輸入：

```console
dir c:\
```

您應該會看到這兩個在映射自訂期間建立的目錄：
- buildActions
- buildArtifacts

## <a name="clean-up"></a>清除

當您完成時，請刪除資源。

### <a name="delete-the-image-builder-template"></a>刪除 image builder 範本

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>刪除角色指派、角色定義和使用者身分識別。
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>刪除映射資源群組

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中所使用之 json 檔案的元件，請參閱影像產生器 [範本參考](../linux/image-builder-json.md)。
