---
title: 在 Azure 中的 Linux Vm 上執行自訂腳本擴充功能
description: 使用自訂指令碼延伸模組 v2，將 Linux VM 設定工作自動化
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: 367116948034fd4bedbeec15e655a09b179865d6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085719"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>搭配 Linux 虛擬機器使用 Azure 自訂指令碼擴充功能第 1 版
自訂指令碼擴充功能第 2 版會在 Azure 虛擬機器上下載並執行指令碼。 此擴充功能適用於部署後設定、軟體安裝或其他任何設定/管理工作。 您可以從 Azure 儲存體或其他可存取的網際網路位置下載指令碼，或是將指令碼提供給擴充功能執行階段。 

自訂指令碼功能可以與 Azure Resource Manager 範本整合。 您也可以使用 Azure CLI、PowerShell 或 Azure 虛擬機器 REST API 來執行它。

本文將詳細說明如何從 Azure CLI 使用自訂指令碼擴充功能，以及如何使用 Azure Resource Manager 範本來執行擴充功能。 本文也提供適用於 Linux 系統的疑難排解步驟。


Linux 自訂指令碼擴充功能有兩個：
* 第 1 版 - Microsoft.OSTCExtensions.CustomScriptForLinux
* 第 2 版 - Microsoft.Azure.Extensions.CustomScript

請切換新的和現有的部署，以改用新的第 2 版。 新版本可供簡易替換 (drop-in replacement)。 因此，移轉就像變更名稱和版本一樣簡單，而不需要變更您的擴充功能設定。


### <a name="operating-system"></a>作業系統

適用于 Linux 的自訂腳本擴充功能將在擴充功能支援的擴充功能 OS 上執行。如需詳細資訊，請參閱這[篇文章](../linux/endorsed-distros.md)。

### <a name="script-location"></a>指令碼位置

您可以利用擴充功能來使用 Azure Blob 儲存體認證，以存取 Azure Blob 儲存體。 或者，指令碼可以位於任何位置，前提是 VM 可以路由傳送至該端點，例如 GitHub、內部檔案伺服器等。

### <a name="internet-connectivity"></a>網際網路連線
如果您需要在外部下載指令碼 (例如 GitHub 或 Azure 儲存體)，則必須開放額外的防火牆/網路安全性群組連接埠。 例如，如果您的腳本位於 Azure 儲存體中，您可以使用 Azure NSG 服務標記來允許存取[儲存體](../../virtual-network/security-overview.md#service-tags)。

如果您的指令碼是在本機伺服器上，則仍然可能需要開放額外的防火牆/網路安全性群組連接埠。

### <a name="tips-and-tricks"></a>秘訣與技巧
* 這個擴充功能的最常見失敗原因是指令碼語法錯誤。請測試指令碼執行時沒有錯誤，而且也在指令碼中放入額外記錄，以方便找出失敗之處。
* 撰寫具有等冪性的指令碼，因此即使意外執行多次，也不會造成系統變更。
* 請確定在指令碼執行時，不需要使用者輸入。
* 指令碼可執行的時間為 90 分鐘。若超過這個時間，將會導致擴充功能佈建失敗。
* 請不要在指令碼內放置重新開機指令，這會造成正在安裝的其他擴充功能發生問題。也不要放置後續重新開機指令，因為擴充功能在重新啟動後不會繼續執行。 
* 如果您的腳本將會造成重新開機，則請安裝應用程式並執行腳本等。您應該使用 Cron 作業，或使用 DSC、Chef、Puppet 擴充功能之類的工具來排程重新開機。
* 擴充功能只會執行指令碼一次。如果您想要在每次開機時執行指令碼，則可以使用 [cloud-init image](../linux/using-cloud-init.md)，並使用 [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) 模組。 或者，您可以使用腳本來建立 SystemD 服務單位。
* 您只能將一個版本的擴充功能套用至 VM。 若要執行第二個自訂腳本，您必須移除自訂腳本延伸模組，並使用更新後的腳本重新套用它。 
* 如果您想要排程指令碼的執行時間，則應該使用擴充功能來建立 Cron 作業。 
* 當指令碼正在執行時，只能從 Azure 入口網站或 CLI 看到「正在轉換」擴充功能狀態。 如果您需要執行中指令碼更頻繁的狀態更新，便必須建立自己的解決方案。
* 自訂腳本擴充功能原本就不支援 proxy 伺服器，不過您可以使用檔案傳輸工具，在您的腳本中支援 proxy 伺服器，例如*捲曲*。 
* 請留意指令碼或命令所依賴的非預設目錄位置是否具備處理此情形的邏輯。
*  將自訂腳本部署至生產 VMSS 實例時，建議透過 json 範本進行部署，並儲存您的腳本儲存體帳戶，您可以在其中控制 SAS 權杖。 


## <a name="extension-schema"></a>擴充功能結構描述

「自訂指令碼擴充功能」組態會指定指令碼位置和要執行命令等項目。 您可將此組態儲存在組態檔中、在命令列中指定組態，或在 Azure Resource Manager 範本中指定組態。 

您可將敏感性資料儲存在受保護的組態中，此組態會經過加密，並且只會在虛擬機器內解密。 當執行命令包含機密資料 (例如密碼) 時，受保護的組態會相當有用。

這些項目應被視為敏感性資料，並在擴充功能保護的設定組態中指定。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
       "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> managedIdentity 屬性**不得**與 storageAccountName 或 storageAccountKey 屬性一起使用

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft.Compute.Extensions | 字串 |
| type | CustomScript | 字串 |
| typeHandlerVersion | 2.1 | int |
| fileUris (例如) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute (例如) | python MyPythonScript.py\<my-param1> | 字串 |
| 指令碼 | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | 字串 |
| skipDos2Unix (範例) | false | boolean |
| timestamp (範例) | 123456789 | 32 位元整數 |
| storageAccountName (例如) | examplestorageacct | 字串 |
| storageAccountKey (例如) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 字串 |
| managedIdentity (例如) | { } 或 { "clientId":"31b403aa-c364-4240-a7ff-d85fb6cd7232" } 或 { "objectId":"12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | JSON 物件 |

### <a name="property-value-details"></a>屬性值詳細資料
* `apiVersion`：您可以使用[資源總管](https://resources.azure.com/)或從 Azure CLI 使用下列命令，找到最新的 apiVersion`az provider list -o json`
* `skipDos2Unix`：(選擇性，布林值) 略過指令碼型檔案 URL 或指令碼的 dos2unix 轉換。
* `timestamp` (選擇性，32 位元整數) 只有在透過變更此欄位的值來觸發指令碼的重新執行時，才需使用此欄位。  任何整數值都是可接受的；只要與先前的值不同即可。
* `commandToExecute`：(若未設定指令碼則為**必要**，字串) 要執行的進入點指令碼。 如果您的命令包含機密資料 (例如密碼)，請改用此欄位。
* `script`：(若未設定 commandToExecute 則為**必要**，字串) /bin/sh 所執行的 base64 編碼 (並選擇性地使用 gzip) 指令碼。
* `fileUris`：(選擇性，字串陣列) 要下載之檔案的 URL。
* `storageAccountName`：(選用，字串) 儲存體帳戶的名稱。 如果您指定儲存體證明資料，則所有 `fileUris` 都必須是 Azure Blob 的 URL。
* `storageAccountKey`：(選用，字串) 儲存體帳戶的存取金鑰
* `managedIdentity`：(選用，JSON 物件) 用來下載檔案的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
  * `clientId`：(選用，字串) 受控識別的用戶端識別碼
  * `objectId`：(選用，字串) 受控識別的物件識別碼


下列值可以在公開或受保護的設定中設定，擴充功能將會拒絕任何同時在公開和受保護的設定中設定下列值的組態。
* `commandToExecute`
* `script`
* `fileUris`

使用公開設定可能有助於偵錯，但強烈建議您使用受保護的設定。

公開設定會以純文字的格式，傳送到執行指令碼所在的 VM。  受保護的設定會使用只有 Azure 和 VM 知道的金鑰來加密。 這些設定會在傳送時儲存到 VM 中。亦即，如果這些設定經過加密，也會在 VM 上以加密的形式儲存。 用來解密加密值的憑證會儲存在 VM 上，並在執行階段用於解密設定 (如有必要)。

#### <a name="property-skipdos2unix"></a>屬性：skipDos2Unix

預設值為 false，表示**會**執行 dos2unix 轉換。

舊版的 CustomScript (Microsoft.OSTCExtensions.CustomScriptForLinux) 會藉由將 `\r\n` 轉譯為 `\n`，自動將 DOS 檔案轉換為 UNIX 檔案。 這項轉譯仍存在，並預設為開啟。 這項轉換會根據下列任何準則，套用至所有從 fileUris 下載的檔案或指令碼設定。

* 如果副檔名是 `.sh`、`.txt`、`.py` 或 `.pl`，則會進行轉換。 指令碼設定將一律符合此準則，因為它假設為會以 /bin/sh 執行的指令碼，並且會在 VM 上儲存為 script.sh。
* 如果檔案的開頭為 `#!`。

dos2unix 轉換可藉由將 skipDos2Unix 設定為 true 來略過。

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>屬性：指令碼

CustomScript 支援執行使用者定義的指令碼。 這是要將 commandToExecute 和 fileUris 結合成單一設定的指令碼設定。 您無須設定要從 Azure 儲存體或 GitHub gist 下載的檔案，而只需要將指令碼編碼為設定即可。 指令碼可用來取代 commandToExecute 和 fileUris。

指令碼**必須**採用 base64 編碼。  指令碼可以**選擇性地**使用 gzip。 指令碼設定可在公用或受保護的設定中使用。 指令碼參數的資料大小上限為 256 KB。 如果指令碼超過此大小，則不會執行。

例如，假設下列指令碼儲存至檔案 /script.sh/。

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

利用下列命令的輸出，將可建構正確的 CustomScript 指令碼設定。

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

指令碼可選擇性地使用 gzip 進一步縮減大小 (在大部分情況下)。 (CustomScript 會自動偵測是否使用了 gzip 壓縮。)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript 會使用下列演算法來執行指令碼。

 1. 宣告指令碼值的長度不超過 256 KB。
 1. base64 將指令碼的值解碼
 1. _嘗試_對 base64 解碼的值使用 gunzip
 1. 將解碼 (並選擇性地解壓縮) 的值寫入至磁碟 (/var/lib/waagent/custom-script/#/script.sh)
 1. 使用 _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh 執行指令碼。

####  <a name="property-managedidentity"></a>屬性：managedIdentity
> [!NOTE]
> **必須**在受保護的設定中，才能指定此屬性。

CustomScript （版本2.1 後）支援從 "fileUris" 設定中提供的 Url 下載檔案的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。 其可讓 CustomScript 存取 Azure 儲存體私人 Blob 或容器，而不需要使用者傳遞 SAS 權杖或儲存體帳戶金鑰之類的秘密。

若要使用這項功能，使用者必須將[系統指派的](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)或[使用者指派的](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)身分識別新增至應執行 CustomScript 的 VM 或 VMSS，並[向受控識別授與 Azure 儲存體容器或 Blob 的存取權](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access)。

若要在目標 VM/VMSS 上使用系統指派的身分識別，請將 [managedidentity] 欄位設定為空的 JSON 物件。 

> 範例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

若要在目標 VM/VMSS 上使用使用者指派的身分識別，請使用用戶端識別碼或受控識別的物件識別碼來設定 [managedidentity] 欄位。

> 範例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity 屬性**不得**與 storageAccountName 或 storageAccountKey 屬性一起使用

## <a name="template-deployment"></a>範本部署
也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節詳述的 JSON 結構描述可以用於 Azure Resource Manager 範本，以在 Azure Resource Manager 範本部署期間執行自訂指令碼擴充功能。 在 [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux) 可以找到包含自訂指令碼擴充功能的範例範本。


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>這些屬性名稱會區分大小寫。 為了避免發生部署問題，請使用如下所示的名稱。

## <a name="azure-cli"></a>Azure CLI
當您要使用 Azure CLI 來執行自訂指令碼擴充功能時，請建立組態檔。 您至少必須擁有 'commandToExecute'。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

您可以選擇在命令中以 JSON 格式化字串的形式指定設定。 這可讓您在執行期間指定組態，而不需使用個別的組態檔。

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 範例

#### <a name="public-configuration-with-script-file"></a>含指令檔的公用組態

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI 命令：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>不含指令檔的公用組態

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI 命令：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>公用且受保護的組態檔

您可以使用公用組態檔來指定指令碼檔案 URI。 您可以使用受保護的組態檔來指定要執行的命令。

公用組態檔：

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

受保護的組態檔：  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI 命令：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>疑難排解
當「自訂指令碼擴充功能」執行時，會建立指令碼，或將指令碼下載到類似下列範例的目錄。 命令輸出也會儲存到這個目錄的 `stdout` 和 `stderr` 檔案中。

```bash
/var/lib/waagent/custom-script/download/0/
```

若要進行疑難排解，請先檢查 Linux 代理程式記錄檔，確認擴充功能已執行，然後檢查：

```bash
/var/log/waagent.log 
```

您應該尋找如下所示的擴充功能執行：

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

注意事項：
1. 命令開始執行時啟用。
2. 下載與從 Azure 下載 CustomScript 擴充功能套件有關，與在 fileUris 中指定的指令碼檔案無關。


「Azure 指令碼擴充功能」會產生一個記錄檔，位置如下：

```bash
/var/log/azure/custom-script/handler.log
```

您應該尋找個別執行，它看起來會像這樣：

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

您可以在此發現：
* 啟動「啟用」命令的是這個記錄檔
* 傳遞至擴充功能的設定
* 擴充功能下載檔案及其結果。
* 正在執行的命令和結果。

您也可以使用 Azure CLI，抓取自訂腳本擴充功能的執行狀態，包括當做傳遞的實際引數 `commandToExecute` ：

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

輸出看起來會像下列文字：

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>後續步驟
若要查看程式碼、目前問題和版本，請參閱 [custom-script-extension-linux 存放庫](https://github.com/Azure/custom-script-extension-linux)。
