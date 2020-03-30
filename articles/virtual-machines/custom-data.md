---
title: 自訂資料和 Azure 虛擬機器
description: 有關在 Azure 虛擬機器上使用自訂資料和雲 Init 的詳細資訊
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109624"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure 虛擬機器上的自訂資料和雲 Init

## <a name="what-is-custom-data"></a>什麼是自訂資料？

客戶經常詢問如何在預配時將腳本或其他中繼資料注入 Microsoft Azure 虛擬機器。  在其他雲中，這個概念通常稱為使用者資料。  在 Microsoft Azure 中，我們有一個類似的功能稱為自訂資料。 

自訂資料僅在初次開機/初始設置期間提供給 VM，我們稱之為"預配"。 預配是 VM 提供 VM 的 VM 創建參數（例如，主機名稱、使用者名、密碼、證書、自訂資料、金鑰等）的過程，預配代理處理它們，如[Linux 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)和[雲 init。](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) 


## <a name="passing-custom-data-to-the-vm"></a>將自訂資料傳遞到 VM
要使用自訂資料，必須先對內容進行基礎編碼，然後再將其傳遞給 API，除非您使用的是為您進行轉換的 CLI 工具，例如 AZ CLI。 大小不能超過 64 KB。

在 CLI 中，您可以將自訂資料作為檔傳遞，並將它轉換為 base64。
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

在 Azure 資源管理器 （ARM） 中，有一個[base64 函數](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)。

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
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>處理自訂資料
安裝在 VM 上的預配代理處理與平臺的介面並將其放置在檔案系統上。 

### <a name="windows"></a>Windows
自訂資料作為二進位檔案放置在 *%SYSTEMDRIVE%\AzureData_CustomData.bin*中，但不會處理。 如果要處理此檔，則需要構建自訂映射，並編寫代碼來處理 CustomData.bin。

### <a name="linux"></a>Linux  
在 Linux 作業系統上，自訂資料通過 ovf-env.xml 檔傳遞到 VM，該檔在預配期間複製到 */var/lib/waagent*目錄。  較新版本的 Microsoft Azure Linux 代理也將將基本編碼的資料複製到 */var/lib/waagent/自訂資料*，以方便。

Azure 當前支援兩個預配代理：
* Linux 代理 - 預設情況下，代理不會處理自訂資料，您需要在啟用自訂映射後構建自訂映射。 根據[文檔](https://github.com/Azure/WALinuxAgent#configuration)，相關設置包括：
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

啟用自訂資料並執行腳本時，它將延遲 VM 報告，該報告已準備就緒，或者預配成功，直到腳本完成。 如果腳本超過 40 分鐘的總 VM 預配時間限制，VM Create 將失敗。 請注意，如果腳本在執行失敗或執行過程中出現錯誤，則不被視為致命預配失敗，則需要創建通知路徑來提醒您腳本的完成狀態。

要排除自訂資料執行的故障，請查看 */var/log/waagent.log*

* 雲 - 預設情況下，雲 init 預設處理自訂資料，雲 init 接受[多種格式](https://cloudinit.readthedocs.io/en/latest/topics/format.html)的自訂資料，如雲 init 配置、腳本等。與 Linux 代理類似，當雲 init 處理自訂資料時。 如果在執行配置處理或腳本期間出現錯誤，則不被視為致命預配失敗，您需要創建通知路徑來提醒您腳本的完成狀態。 但是，與 Linux 代理不同，雲 init 不會等待使用者自訂資料配置完成，然後再向 VM 已準備好的平臺報告。 有關 azure 上的雲 init 的詳細資訊，請查看[文檔](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。


要對自訂資料執行進行故障排除，請查看故障排除[文檔](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)。


## <a name="faq"></a>常見問題集
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>創建 VM 後，是否可以更新自訂資料？
對於單個 VM，無法更新 VM 模型中的自訂資料，但對於 VMSS，您可以通過 REST API 更新 VMSS 自訂資料（不適用於 PS 或 AZ CLI 用戶端）。 在 VMSS 模型中更新自訂資料時：
* VMSS 中的現有實例只有在重新映射之前才能獲取更新的自訂資料。
* 升級的 VMSS 中的現有實例將得不到更新的自訂資料。
* 新實例將接收新的自訂資料。

### <a name="can-i-place-sensitive-values-in-custom-data"></a>是否可以在自訂資料中放置敏感值？
我們建議**不要**將敏感性資料存儲在自訂資料中。 有關詳細資訊，請參閱[Azure 安全和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)。


### <a name="is-custom-data-made-available-in-imds"></a>自訂資料是否在 IMDS 中提供？
否，此功能當前不可用。
