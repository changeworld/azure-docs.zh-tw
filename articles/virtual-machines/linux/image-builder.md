---
title: 使用 Azure 映射產生器建立 Linux VM （預覽）
description: 使用 Azure 映射產生器建立 Linux VM。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: b50b2a9bfca99e1868c083759cce26bb176789f4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792406"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>預覽：使用 Azure 映射產生器建立 Linux VM

本文說明如何使用 Azure 映射產生器和 Azure CLI 來建立自訂的 Linux 映射。 本文中的範例會使用三個[不同的](image-builder-json.md#properties-customize)自訂程式來自訂映射：

- Shell （ScriptUri）-下載並執行[shell 腳本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)。
- Shell （內嵌）-執行特定的命令。 在此範例中，內嵌命令包括建立目錄和更新 OS。
- File-將檔案[從 GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)複製到 VM 上的目錄。

您也可以指定`buildTimeoutInMinutes`。 預設值是240分鐘，您可以增加組建時間以允許較長的執行中組建。

我們將使用範例. json 範本來設定映射。 我們所使用的. json 檔案位於這裡： [helloImageTemplateLinux。](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json) 

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

## <a name="setup-example-variables"></a>設定範例變數

我們會重複使用一些資訊，因此我們將建立一些變數來儲存該資訊。


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

建立訂用帳戶識別碼的變數。 您可以使用`az account show | grep id`來取得。

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>建立資源群組。
這是用來儲存影像設定範本成品和映射。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>設定資源群組的許可權
提供映射產生器的「參與者」許可權，以在資源群組中建立映射。 如果沒有適當的許可權，映射組建將會失敗。 

`--assignee`值是映射產生器服務的應用程式註冊識別碼。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>下載範本範例

已建立參數化範例影像設定範本供您使用。 下載範例 json 檔案，並使用您稍早設定的變數加以設定。

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

您可以視需要修改此範例. json。 例如，您可以增加的值， `buildTimeoutInMinutes`以允許較長的執行中組建。 您可以使用像`vi`是的文字編輯器，在 Cloud Shell 中編輯檔案。

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 針對 [來源映射]，您必須一律[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)，而不`latest`能使用。
>
> 如果您新增或變更要發佈映射的資源群組，您必須確定[已針對資源群組設定許可權](#set-permissions-on-the-resource-group)。


## <a name="submit-the-image-configuration"></a>提交映射設定
將映射設定提交至 VM 映射產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

如果成功完成，它會傳回成功訊息，並在 $imageResourceGroup 中建立映射產生器設定範本成品。 如果您啟用「顯示隱藏的類型」，您可以在入口網站中看到資源群組。

此外，在背景中，「映射產生器」會在您的訂用帳戶中建立預備資源群組。 映射產生器會使用暫存資源群組來建立映射。 資源群組的名稱會採用下列格式： `IT_<DestinationResourceGroup>_<TemplateName>`。

> [!IMPORTANT]
> 請勿直接刪除暫存資源群組。 如果您刪除映射範本成品，它會自動刪除暫存資源群組。 如需詳細資訊，請參閱本文結尾的[清理](#clean-up)一節。

如果服務在映射設定範本提交期間報告失敗，請參閱[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步驟。 您也必須先刪除範本，然後再重試提交組建。 若要刪除範本：

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>啟動映射組建

啟動映射組建。


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

等到組建完成後，在此範例中，可能需要10-15 分鐘的時間。

如果您遇到任何錯誤，請參閱這些[疑難排解](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步驟。


## <a name="create-the-vm"></a>建立 VM

使用您建立的映射建立 VM。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

從建立 VM 的輸出中取得 IP 位址，並使用它來透過 SSH 連線到 VM。

```bash
ssh azureuser@<pubIp>
```

您應該會在建立 SSH 連線後，看到該映射已自訂一天的訊息！

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

當`exit`您完成關閉 SSH 連線時，請輸入。

## <a name="check-the-source"></a>檢查來源

在 [映射產生器] 範本的 [屬性] 中，您會看到來源影像、其執行的自訂腳本，以及其散發位置。

```bash
cat helloImageTemplateLinux.json
```

如需有關此 json 檔案的詳細資訊，請參閱影像產生器[範本參考](image-builder-json.md)

## <a name="clean-up"></a>清除

當您完成時，您可以刪除資源。

刪除映射產生器範本。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

刪除映射資源群組。

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中所使用之 json 檔案的元件，請參閱影像產生器[範本參考](image-builder-json.md)。
