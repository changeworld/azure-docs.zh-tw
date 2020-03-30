---
title: 使用 Azure 映射產生器創建 Linux VM（預覽）
description: 使用 Azure 映射產生器創建 Linux VM。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066676"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>預覽：使用 Azure 映射產生器創建 Linux VM

本文介紹如何使用 Azure 映射產生器和 Azure CLI 創建自訂 Linux 映射。 本文中的示例使用三個不同的[自訂器](image-builder-json.md#properties-customize)來自訂映射：

- 外殼（腳本） - 下載並運行[一個shell腳本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)。
- 命令程式（內聯） - 運行特定命令。 在此示例中，內聯命令包括創建目錄和更新作業系統。
- 檔 -[將 GitHub 的檔](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)複製到 VM 上的目錄中。

還可以指定 。 `buildTimeoutInMinutes` 預設值為 240 分鐘，您可以增加生成時間以允許較長的運行生成。

我們將使用示例 .json 範本來配置映射。 我們使用的 .json 檔在這裡： [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

> [!IMPORTANT]
> Azure 映射產生器當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>註冊功能
要在預覽期間使用 Azure 映射產生器，需要註冊新功能。

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

如果他們不說已註冊，則運行以下內容：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>設置示例變數

我們將反復使用一些資訊片段，因此我們將創建一些變數來存儲該資訊。


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

為訂閱 ID 創建變數。 您可以使用 獲取此`az account show | grep id`功能。

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>建立資源群組。
這用於存儲圖像配置範本專案和映射。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>設置資源組的許可權
授予圖像產生器"參與者"在資源組中創建映射的許可權。 如果沒有適當的許可權，映射生成將失敗。 

該`--assignee`值是映射產生器服務的應用註冊 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>下載範本示例

已創建參數化的示例映射配置範本供您使用。 下載示例 .json 檔，然後使用前面設置的變數對其進行配置。

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

您可以根據需要修改此示例 .json。 例如，可以增加 的值`buildTimeoutInMinutes`以允許較長的運行生成。 您可以使用 文字編輯器（如`vi`）在雲殼中編輯檔。

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 對於源映射，必須始終[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)，不能使用`latest`。
>
> 如果添加或更改正在分發映射的資源組，則需要確保[為資源組設置了許可權](#set-permissions-on-the-resource-group)。


## <a name="submit-the-image-configuration"></a>提交映射配置
將映射配置提交到 VM 映射產生器服務

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

如果成功完成，它將返回一條成功消息，並在$imageResourceGroup中創建映射產生器配置範本專案。 如果啟用"顯示隱藏類型"，則可以在門戶中查看資源組。

此外，在後臺，圖像產生器會在訂閱中創建暫存資源組。 映射產生器使用暫存資源組進行映射生成。 資源組的名稱將採用此格式： `IT_<DestinationResourceGroup>_<TemplateName>`。

> [!IMPORTANT]
> 不要直接刪除暫存資源組。 如果刪除圖像範本專案，它將自動刪除暫存資源組。 有關詳細資訊，請參閱本文末尾的["清理](#clean-up)"部分。

如果服務在映射配置範本提交期間報告失敗，請參閱[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步驟。 在重試提交生成之前，還需要刪除範本。 要刪除範本：

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>啟動映射生成

啟動映射生成。


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

等待生成完成，對於此示例，可能需要 10-15 分鐘。

如果您遇到任何錯誤，請查看這些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步驟。


## <a name="create-the-vm"></a>建立 VM

使用生成的映射創建 VM。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

從創建 VM 的輸出中獲取 IP 位址，並將其用於 VM 的 SSH。

```bash
ssh azureuser@<pubIp>
```

一旦建立 SSH 連接，您應該會看到圖像是使用當天消息自訂的！

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

完成`exit`關閉 SSH 連接時鍵入。

## <a name="check-the-source"></a>檢查源

在圖像產生器範本中，在"屬性"中，您將看到源映射、運行的自訂腳本以及分發位置。

```bash
cat helloImageTemplateLinux.json
```

有關此 .json 檔的詳細資訊，請參閱[圖像產生器範本引用](image-builder-json.md)

## <a name="clean-up"></a>清除

完成後，可以刪除資源。

刪除映射產生器範本。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

刪除圖像資源組。

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>後續步驟

要瞭解有關本文中使用的 .json 檔元件的更多內容，請參閱[圖像產生器範本引用](image-builder-json.md)。
