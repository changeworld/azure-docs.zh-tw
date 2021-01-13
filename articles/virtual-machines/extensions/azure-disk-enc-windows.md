---
title: Windows 適用的 Azure 磁碟加密
description: 使用虛擬機器擴充功能將 Azure 磁碟加密部署至 Windows 虛擬機器。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 361751107212d556aaee6886902d2798c014ff39
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180178"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>適用於 Windows 的 Azure 磁碟加密 (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概觀

Azure 磁碟加密會利用 BitLocker 在執行 Windows 的 Azure 虛擬機器上提供完整的磁碟加密。  此解決方案與 Azure Key Vault 整合，可讓您管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 

## <a name="prerequisites"></a>先決條件

如需必要條件的完整清單，請參閱 [適用于 Windows vm 的 Azure 磁碟加密](../windows/disk-encryption-overview.md)，特別是下列各節：

- [支援的 VM 與作業系統](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路需求](../windows/disk-encryption-overview.md#networking-requirements)
- [群組原則需求](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>擴充功能結構描述

Azure 磁碟加密 (ADE) 有兩種版本的延伸模組架構：
- 2.2-較新的建議架構，不使用 Azure Active Directory (AAD) 屬性。
- v1.1-需要 Azure Active Directory (AAD) 屬性的舊版架構。 

若要選取目標架構， `typeHandlerVersion` 屬性必須設定為與您想要使用的架構版本相等。

### <a name="schema-v22-no-aad-recommended"></a>架構2.2：不建議使用 AAD () 

針對所有新的 Vm，建議使用2.2 架構，而不需要 Azure Active Directory 屬性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>架構 v1.1：使用 AAD 

1.1 架構需要 `aadClientID` 和 `aadClientSecret` 或 `AADClientCertificate` ，不建議用於新的 vm。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
| type | AzureDiskEncryption | 字串 |
| typeHandlerVersion | 2.2、1。1 | 字串 |
|  (1.1 架構) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
|  (1.1 架構) AADClientSecret | 密碼 | 字串 |
|  (1.1 架構) AADClientCertificate | thumbprint | 字串 |
| EncryptionOperation | EnableEncryption | 字串 | 
|  (選用-預設的 RSA-OAEP ) KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | 字串 |
| KeyVaultURL | url | 字串 |
| KeyVaultResourceId | url | 字串 |
|  (選擇性) KeyEncryptionKeyURL | url | 字串 |
|  (選擇性) KekVaultResourceId | url | 字串 |
|  (選擇性) SequenceVersion | UNIQUEIDENTIFIER | 字串 |
| VolumeType | 作業系統、資料、全部 | 字串 |

## <a name="template-deployment"></a>範本部署

如需以架構2.2 為基礎的範本部署範例，請參閱 Azure 快速入門範本 [201-加密-執行-windows-vm-不含 aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)。

如需以架構 v1.1 為基礎的範本部署範例，請參閱 Azure 快速入門範本 [201-加密-執行-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)。

>[!NOTE]
> 此外 `VolumeType` ，如果將參數設定為 All，則只有在資料磁片格式正確時，才會將資料磁片加密。 

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

如需疑難排解資訊，請參閱 [Azure 磁碟加密疑難排解指南](../windows/disk-encryption-troubleshooting.md)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 

或者，您可以提出 Azure 支援事件。 移至 [Azure 支援](https://azure.microsoft.com/support/options/) ，然後選取 [取得支援]。 如需使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>下一步

* 如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Windows 功能](features-windows.md)。
* 如需 Windows Azure 磁碟加密的詳細資訊，請參閱 [windows 虛擬機器](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)。
