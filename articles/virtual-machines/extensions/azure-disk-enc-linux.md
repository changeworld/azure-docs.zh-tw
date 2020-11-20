---
title: 適用于 Linux 的 Azure 磁碟加密
description: 使用虛擬機器擴充功能將 Azure 磁碟加密部署至 Linux 的虛擬機器。
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 61f8fd3d671bbd66d2dc5656e9a467b06798d280
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968412"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>適用於 Linux 的 Azure 磁碟加密 (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>概觀

Azure 磁碟加密會使用 Linux 中的 dm-crypt 子系統在[選取的 Azure Linux 發行版本](../linux/disk-encryption-overview.md)上提供完整的磁碟加密。  此解決方案會與 Azure Key Vault 整合，以管理磁碟加密金鑰和祕密。

## <a name="prerequisites"></a>Prerequisites

如需必要條件的完整清單，請參閱 [適用于 Linux vm 的 Azure 磁碟加密](../linux/disk-encryption-overview.md)，特別是下列各節：

- [支援的 VM 與作業系統](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 需求](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [網路需求](../linux/disk-encryption-overview.md#networking-requirements)
- [加密金鑰儲存體需求](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>擴充功能結構描述

Azure 磁碟加密 (ADE) 有兩種版本的延伸模組架構：
- v1.1-較新的建議架構，不使用 Azure Active Directory (AAD) 屬性。
- v 0.1-需要 Azure Active Directory (AAD) 屬性的較舊架構。 

若要選取目標架構， `typeHandlerVersion` 屬性必須設定為與您想要使用的架構版本相等。

### <a name="schema-v11-no-aad-recommended"></a>架構 v1.1：不建議使用 AAD () 

建議您使用 v1.1 架構，而不需要 Azure Active Directory (AAD) 屬性。

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


### <a name="schema-v01-with-aad"></a>架構 v 0.1：使用 AAD 

0.1 架構需要 `AADClientID` 和 `AADClientSecret` 或 `AADClientCertificate` 。

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
| typeHandlerVersion | 1.1、0。1 | int |
|  (0.1 架構) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
|  (0.1 架構) AADClientSecret | 密碼 | 字串 |
|  (0.1 架構) AADClientCertificate | thumbprint | 字串 |
|  (選擇性)  (0.1 架構) 複雜密碼 | 密碼 | 字串 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | 字串 | 
|  (選用-預設的 RSA-OAEP ) KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | 字串 |
| KeyVaultURL | url | 字串 |
| KeyVaultResourceId | url | 字串 |
|  (選擇性) KeyEncryptionKeyURL | url | 字串 |
|  (選擇性) KekVaultResourceId | url | 字串 |
|  (選擇性) SequenceVersion | UNIQUEIDENTIFIER | 字串 |
| VolumeType | 作業系統、資料、全部 | 字串 |

## <a name="template-deployment"></a>範本部署

如需以架構 v1.1 為基礎的範本部署範例，請參閱 Azure 快速入門範本 [201-加密-執行-linux-vm-不含 aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)。

如需以架構 v 0.1 為基礎的範本部署範例，請參閱 Azure 快速入門範本 [201-加密-執行-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)。

>[!WARNING]
> - 如果您先前曾使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。
> - 在加密 Linux OS 磁碟區時，請將 VM 視為無法使用。 強烈建議您在加密進行當下避免進行 SSH 登入，以免發生問題而封鎖加密過程中需要存取的任何已開啟檔案。 若要檢查進度，請使用 [>get-azvmdiskencryptionstatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell Cmdlet 或 [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI 命令。 對於 30GB OS 磁碟區，此過程可能需要幾個小時，再加上額外的時間來進行加密資料磁碟區。 除非使用加密格式所有選項，否則資料磁碟區加密時間將與資料磁碟區的大小和數量成正比。 
> - 只有資料磁碟區支援在 Linux VM 上停用加密。 如果 OS 磁碟區已加密，則不支援在資料或 OS 磁碟區上停用加密。 

>[!NOTE]
> 此外，如果將 `VolumeType` 參數設定為 All，則只有在資料磁片已正確掛接時，才會將資料磁片加密。

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

如需疑難排解資訊，請參閱 [Azure 磁碟加密疑難排解指南](../linux/disk-encryption-troubleshooting.md)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 

或者，您可以提出 Azure 支援事件。 移至 [Azure 支援](https://azure.microsoft.com/support/options/) ，然後選取 [取得支援]。 如需使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟

* 如需 VM 擴充功能的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](features-linux.md)。
* 如需 Linux Azure 磁碟加密的詳細資訊，請參閱 [linux 虛擬機器](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)。