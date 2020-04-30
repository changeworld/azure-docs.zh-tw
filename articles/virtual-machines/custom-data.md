---
title: 自訂資料和 Azure 虛擬機器
description: 在 Azure 上使用自訂資料和雲端 Init 的詳細資訊虛擬機器
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759130"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure 上的自訂資料和雲端 Init 虛擬機器

## <a name="what-is-custom-data"></a>什麼是自訂資料？

客戶通常會詢問如何在布建時間將腳本或其他中繼資料插入 Microsoft Azure 的虛擬機器中。  在其他雲端中，此概念通常稱為使用者資料。  在 Microsoft Azure 中，我們有類似的功能，稱為自訂資料。 

只有在第一次開機/初始設定期間，VM 才可使用自訂資料，我們稱之為「布建」。 布建是 vm 建立參數（例如，主機名稱、使用者名稱、密碼、憑證、自訂資料、金鑰等）可供 VM 使用的程式，而布建代理程式會處理它們，例如[Linux 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)和[雲端 init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)。 


## <a name="passing-custom-data-to-the-vm"></a>將自訂資料傳遞至 VM
若要使用自訂資料，您必須先對內容進行 base64 編碼，再將它傳遞給 API，除非您使用的 CLI 工具會為您執行轉換，例如 AZ CLI。 大小不得超過 64 KB。

在 CLI 中，您可以將自訂資料當做檔案傳遞，並將其轉換為 base64。
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

在 Azure Resource Manager （ARM）中，有一個[base64 函數](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)。

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>處理自訂資料
安裝在 Vm 上的布建代理程式會處理與平臺的互動，並將其放在檔案系統上。 

### <a name="windows"></a>Windows
自訂資料會以二進位檔案的形式放在 *%SYSTEMDRIVE%\AzureData\CustomData.bin*中，但不會進行處理。 如果您想要處理這個檔案，您將需要建立自訂映射，並撰寫程式碼來處理 CustomData。

### <a name="linux"></a>Linux  
在 Linux OS 上，會透過 ovf-env 將自訂資料傳遞至 VM，此檔案會在布建期間複製到 */var/lib/waagent*目錄。  較新版本的 Microsoft Azure Linux 代理程式也會將 base64 編碼的資料複製到*存放于/var/lib/waagent/customdata* ，以方便您參考。

Azure 目前支援兩個布建代理程式：
* Linux 代理程式-代理程式預設不會處理自訂資料，您必須建立已啟用它的自訂映射。 相關的設定，根據[檔](https://github.com/Azure/WALinuxAgent#configuration)：
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

當您啟用自訂資料並執行腳本時，它會延遲已準備好的 VM 報告，或布建已成功，直到腳本完成為止。 如果腳本超過總 VM 布建時間額度40分鐘，VM 建立將會失敗。 請注意，如果腳本無法執行，或在執行期間發生錯誤，則不會被視為嚴重的布建失敗，您將需要建立通知路徑，以警示腳本的完成狀態。

若要針對自訂資料執行進行疑難排解，請參閱 */var/log/waagent.log*

* 雲端初始設定預設會處理自訂資料，而雲端 init 則接受[多種格式](https://cloudinit.readthedocs.io/en/latest/topics/format.html)的自訂資料，例如雲端 init 設定、腳本等。當雲端 init 處理自訂資料時，類似于 Linux 代理程式。 如果在設定處理或腳本執行期間發生錯誤，則不會被視為嚴重的布建失敗，而且您將需要建立通知路徑，以警示腳本的完成狀態。 不過，與 Linux 代理程式不同的是，雲端 init 不會等待使用者自訂資料設定完成，然後才向平臺回報 VM 已準備就緒。 如需 azure 上的雲端 init 的詳細資訊，請參閱[檔](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。


若要針對自訂資料執行進行疑難排解，請參閱疑難排解[檔](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)。


## <a name="faq"></a>常見問題集
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>我可以在建立 VM 之後更新自訂資料嗎？
針對單一 Vm，無法更新 VM 模型中的自訂資料，但若要進行 VMSS，您可以透過 REST API （不適用於 PS 或 AZ CLI 用戶端）來更新 VMSS 自訂資料。 當您更新 VMSS 模型中的自訂資料時：
* VMSS 中的現有實例將不會取得更新的自訂資料，只會在重新安裝映射之前。
* VMSS 中已升級的現有實例將不會取得更新的自訂資料。
* 新的實例將會收到新的自訂資料。

### <a name="can-i-place-sensitive-values-in-custom-data"></a>我可以在自訂資料中放置機密值嗎？
我們建議您**不要**將敏感性資料儲存在自訂資料中。 如需詳細資訊，請參閱[Azure 安全性和加密最佳作法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)。


### <a name="is-custom-data-made-available-in-imds"></a>自訂資料是否可在 IMDS 中取得？
否，目前無法使用這項功能。
