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
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066873"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>適用於 Windows 的 Azure 磁碟加密 (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>總覽

Azure 磁碟加密會利用 BitLocker 在執行 Windows 的 Azure 虛擬機器上提供完整的磁碟加密。  此解決方案與 Azure Key Vault 整合，可讓您管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 

## <a name="prerequisites"></a>Prerequisites

有關先決條件的完整清單，請參閱 Windows [VM 的 Azure 磁片加密](../windows/disk-encryption-overview.md)，具體介紹以下部分：

- [支援的 VM 和作業系統](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路需求](../windows/disk-encryption-overview.md#networking-requirements)
- [群組原則要求](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>擴充功能結構描述

Azure 磁片加密 （ADE） 有兩個版本的擴展架構：
- v2.2 - 不使用 Azure 活動目錄 （AAD） 屬性的較新的建議架構。
- v1.1 - 需要 Azure 活動目錄 （AAD） 屬性的舊架構。 

要選擇目標架構，`typeHandlerVersion`必須將屬性設置為等於要使用的架構版本。

### <a name="schema-v22-no-aad-recommended"></a>架構 v2.2： 無 AAD（推薦）

建議為所有新 VM 提供 v2.2 架構，並且不需要 Azure 活動目錄屬性。

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


### <a name="schema-v11-with-aad"></a>架構 v1.1： 與 AAD 

1.1 架構需要`aadClientID`新`aadClientSecret`VM`AADClientCertificate`和 或 不建議使用。

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
| type | Azure 磁片加密 | 字串 |
| typeHandlerVersion | 2.2, 1.1 | 字串 |
| （1.1 架構）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| （1.1 架構）AADClient秘密 | 密碼 | 字串 |
| （1.1 架構）AADClient 證書 | thumbprint | 字串 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | 字串 | 
| （可選 - 預設 RSA-OAEP ）金鑰加密演算法 | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | 字串 |
| KeyVaultURL | url | 字串 |
| 金鑰庫資源 Id | url | 字串 |
| （可選）金鑰加密鍵URL | url | 字串 |
| （可選）凱庫沃資源Id | url | 字串 |
| （可選）序列版本 | UNIQUEIDENTIFIER | 字串 |
| VolumeType | 作業系統、資料、全部 | 字串 |

## <a name="template-deployment"></a>範本部署

有關基於架構 v2.2 的範本部署示例，請參閱 Azure 快速入門範本[201-加密運行-視窗-vm-無 aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)。

有關基於架構 v1.1 的範本部署示例，請參閱 Azure 快速入門範本[201 加密運行視窗 vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)。

>[!NOTE]
> 此外，`VolumeType`如果參數設置為 All，則資料磁片僅在正確格式化時才會加密。 

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

如需疑難排解資訊，請參閱 [Azure 磁碟加密疑難排解指南](../windows/disk-encryption-troubleshooting.md)。

### <a name="support"></a>支援

如果本文中的任何一點都需要更多説明，則可以在[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/community/)上聯繫 Azure 專家。 

或者，您可以提出 Azure 支援事件。 轉到[Azure 支援](https://azure.microsoft.com/support/options/)並選擇"獲取支援"。 有關使用 Azure 支援的資訊，請閱讀[Microsoft Azure 支援常見問題解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟

* 如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Windows 功能](features-windows.md)。
* 有關 Windows 的 Azure 磁片加密的詳細資訊，請參閱[Windows 虛擬機器](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)。
