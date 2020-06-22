---
title: 自訂資料和 Azure 虛擬機器
description: 有關在 Azure 虛擬機器上使用自訂資料和 cloud-init 的詳細資訊
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: c0dd5c8cd61d1c7abf11d97e858fdc30d774e456
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259111"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>在 Azure 虛擬機器上的自訂資料和 cloud-init

## <a name="what-is-custom-data"></a>什麼是自訂資料？

客戶通常會詢問如何在佈建時間將指令碼或其他中繼資料插入 Microsoft Azure 虛擬機器中。  在其他雲端中，此概念通常稱為使用者資料。  在 Microsoft Azure 中，我們有一個類似的功能，稱為「自訂資料」。 

VM 只有在第一次開機/初始設定期間才可使用自訂資料，我們稱之為「佈建」。 佈建是指將「VM 建立參數」(例如主機名稱、使用者名稱、密碼、憑證、自訂資料、金鑰等) 提供給 VM 使用，並由佈建代理程式 (例如 [Linux 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)和 [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)) 來處理這些參數的流程。 


## <a name="passing-custom-data-to-the-vm"></a>將自訂資料傳遞至 VM
若要使用自訂資料，除非您使用的 CLI 工具會為您執行轉換，例如 AZ CLI，否則您必須先對內容進行 base64 編碼，再將它傳遞給 API。 檔案大小不能超過 64 KB。

在 CLI 中，您可以將自訂資料當做檔案傳遞，並將其轉換為 base64。
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

在 Azure Resource Manager (ARM) 中，有一個 [base64 函數](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)。

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
安裝在 VM 上的佈建代理程式會處理與平台的互動，並將其置於檔案系統上。 

### <a name="windows"></a>Windows
自訂資料會以二進位檔案的形式放在 *%SYSTEMDRIVE%\AzureData\CustomData.bin* 中，但不會進行處理。 如果要處理這個檔案，您需要建立自訂映像，並撰寫程式碼來處理 CustomData。

### <a name="linux"></a>Linux  
在 Linux OS 上，自訂資料會透過 ovf-env.xml 檔案傳送至 VM，而此檔案會在佈建期間複製到 */var/lib/waagent* 目錄。  新版 Microsoft Azure Linux 代理程式還會將 base64 編碼的資料複製到 */var/lib/waagent/CustomData*，以方便您參考。

Azure 目前支援兩個佈建代理程式：
* Linux 代理程式 - 此代理程式依預設不會處理自訂資料，您必須建立已啟用它的自訂映像。 根據[文件](https://github.com/Azure/WALinuxAgent#configuration)，相關的設定如下：
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

如果已啟用自訂資料並執行指令碼，則在指令碼完成前，會延遲 VM 已準備好或佈建成功的報告。 如果指令碼超過 40分鐘的總 VM 佈建時間額度，則 VM 建立會失敗。 請注意，如果指令碼無法執行，或在執行期間發生錯誤，則不會視為嚴重的佈建失敗，您需要建立通知路徑，以提醒您指令碼的完成狀態。

若要針對自訂資料執行進行疑難排解，請參閱 */var/log/waagent.log*

* cloud-init - 依預設會處理自訂資料，因此可接受[多種格式](https://cloudinit.readthedocs.io/en/latest/topics/format.html)的自訂資料，例如 cloud-init 組態、指令碼等。當 cloud-init 處理自訂資料時，類似於 Linux 代理程式。 如果在設定處理或指令碼執行期間發生錯誤，則不會被視為嚴重的佈建失敗，您需要建立通知路徑，以提醒您指令碼的完成狀態。 不過，與 Linux 代理程式不同的是，cloud-init 不會等待使用者自訂資料設定完成，然後才向平台回報 VM 已準備就緒。 如需有關 Azure 上 cloud-init 的詳細資訊，請參閱[文件](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。


如需對自訂資料執行進行疑難排解，請參閱疑難排解[文件](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)。


## <a name="faq"></a>常見問題集
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>我可以在建立 VM 後更新自訂資料嗎？
針對單一 VM，無法更新 VM 模型中的自訂資料，但針對 VMSS，您可以透過 [REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/update) 來更新 VMSS 自訂資料 (不適用於 PS 或 AZ CLI 用戶端)。 當您更新 VMSS 模型中的自訂資料時：
* VMSS 中的現有執行個體在重新安裝映像之前無法取得更新的自訂資料。
* VMSS 中已升級的現有執行個體將無法取得更新的自訂資料。
* 新的執行個體將會收到新的自訂資料。

### <a name="can-i-place-sensitive-values-in-custom-data"></a>我可以將機密值放置在自訂資料中嗎？
建議您**不要**將敏感性資料儲存在自訂資料中。 如需詳細資訊，請參閱 [Azure 安全性和加密最佳作法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)。


### <a name="is-custom-data-made-available-in-imds"></a>在 IMDS 中是否提供自訂資料功能？
否，目前不提供這項功能。
