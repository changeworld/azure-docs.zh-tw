---
title: Linux 的 Azure 磁片加密
description: 使用虛擬機器擴充功能將 Azure 磁碟加密部署至 Linux 的虛擬機器。
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066923"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>適用於 Linux 的 Azure 磁碟加密 (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>總覽

Azure 磁碟加密會使用 Linux 中的 dm-crypt 子系統在[選取的 Azure Linux 發行版本](https://aka.ms/adelinux)上提供完整的磁碟加密。  此解決方案會與 Azure Key Vault 整合，以管理磁碟加密金鑰和祕密。

## <a name="prerequisites"></a>Prerequisites

有關先決條件的完整清單，請參閱 Linux [VM 的 Azure 磁片加密](../linux/disk-encryption-overview.md)，具體介紹以下各節：

- [支援的 VM 和作業系統](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 要求](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [網路需求](../linux/disk-encryption-overview.md#networking-requirements)
- [加密金鑰存儲要求](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>擴充功能結構描述

Azure 磁片加密 （ADE） 有兩個版本的擴展架構：
- v1.1 - 不使用 Azure 活動目錄 （AAD） 屬性的較新的建議架構。
- v0.1 - 需要 Azure 活動目錄 （AAD） 屬性的舊架構。 

要選擇目標架構，`typeHandlerVersion`必須將屬性設置為等於要使用的架構版本。

### <a name="schema-v11-no-aad-recommended"></a>架構 v1.1： 無 AAD（推薦）

建議使用 v1.1 架構，不需要 Azure 活動目錄 （AAD） 屬性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>架構 v0.1： 與 AAD 

0.1 架構需要`AADClientID`和`AADClientSecret``AADClientCertificate`或 。

使用 `AADClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

使用 `AADClientCertificate`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | 字串 |
| type | AzureDiskEncryptionForLinux | 字串 |
| typeHandlerVersion | 1.1, 0.1 | int |
| （0.1 架構）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| （0.1 架構）AADClient秘密 | 密碼 | 字串 |
| （0.1 架構）AADClient 證書 | thumbprint | 字串 |
| （可選）（0.1 架構）密碼 | 密碼 | 字串 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | 字串 | 
| （可選 - 預設 RSA-OAEP ）金鑰加密演算法 | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | 字串 |
| KeyVaultURL | url | 字串 |
| 金鑰庫資源 Id | url | 字串 |
| （可選）金鑰加密鍵URL | url | 字串 |
| （可選）凱庫沃資源Id | url | 字串 |
| （可選）序列版本 | UNIQUEIDENTIFIER | 字串 |
| VolumeType | 作業系統、資料、全部 | 字串 |

## <a name="template-deployment"></a>範本部署

有關基於架構 v1.1 的範本部署示例，請參閱 Azure 快速入門範本[201-加密運行-linux-vm-無 aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)。

有關基於架構 v0.1 的範本部署的示例，請參閱 Azure 快速入門範本[201 加密運行-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)。

>[!WARNING]
> - 如果以前使用 Azure 磁片加密與 Azure AD 一起加密 VM，則必須繼續使用此選項對 VM 進行加密。
> - 加密 Linux 作業系統卷時，應認為 VM 不可用。 我們強烈建議在加密過程中避免 SSH 登錄，以避免在加密過程中阻止任何需要訪問的打開檔的問題。 要檢查進度，請使用[獲取-AzVM 磁片加密狀態](/powershell/module/az.compute/get-azvmdiskencryptionstatus)PowerShell Cmdlet 或[vm 加密顯示](/cli/azure/vm/encryption#az-vm-encryption-show)CLI 命令。 對於 30GB OS 磁碟區，此過程可能需要幾個小時，再加上額外的時間來進行加密資料磁碟區。 除非使用加密格式所有選項，否則資料磁碟區加密時間將與資料磁碟區的大小和數量成正比。 
> - 只有資料磁碟區支援在 Linux VM 上停用加密。 如果 OS 磁碟區已加密，則不支援在資料或 OS 磁碟區上停用加密。 

>[!NOTE]
> 此外，`VolumeType`如果參數設置為 All，則資料磁片僅在正確裝載時才會加密。

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

如需疑難排解資訊，請參閱 [Azure 磁碟加密疑難排解指南](../linux/disk-encryption-troubleshooting.md)。

### <a name="support"></a>支援

如果本文中的任何一點都需要更多説明，則可以在[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/community/)上聯繫 Azure 專家。 

或者，您可以提出 Azure 支援事件。 轉到[Azure 支援](https://azure.microsoft.com/support/options/)並選擇"獲取支援"。 有關使用 Azure 支援的資訊，請閱讀[Microsoft Azure 支援常見問題解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟

* 如需 VM 擴充功能的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](features-linux.md)。
* 有關 Linux 的 Azure 磁片加密的詳細資訊，請參閱[Linux 虛擬機器](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)。
