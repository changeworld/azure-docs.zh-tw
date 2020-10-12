---
title: Azure 磁碟加密疑難排解指南
description: 本文提供 Windows VM 所適用 Microsoft Azure 磁碟加密的疑難排解祕訣。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e48c6cfd1160406d55ffdc3c9bafe733a6e5e4a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400072"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁碟加密疑難排解指南

本指南適用於組織採用 Azure 磁碟加密的 IT 專業人員、資訊安全性分析師和雲端系統管理員。 本文旨在協助磁碟加密相關問題的疑難排解。

在採取以下任何步驟之前，請先確定想要加密的 VM 是[支援的 VM 大小與作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)，且已符合所有先決條件：

- [網路需求](disk-encryption-overview.md#networking-requirements)
- [群組原則需求](disk-encryption-overview.md#group-policy-requirements)
- [加密金鑰儲存體需求](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>針對防火牆後方的 Azure 磁碟加密進行疑難排解

當連線能力受防火牆、Proxy 需求或網路安全性群組 (NSG) 設定限制時，擴充功能執行所需工作的能力可能會受到中斷。 這個中斷會產生狀態訊息 (例如「無法在 VM 上使用擴充功能狀態」)。 在預期的情節中，無法完成加密。 以下各節有一些常見的防火牆問題，您可能需要進行調查。

### <a name="network-security-groups"></a>網路安全性群組
任何套用的網路安全性群組設定仍然必須允許端點，從而符合磁碟加密的記載網路設定[必要條件](disk-encryption-overview.md#networking-requirements)。

### <a name="azure-key-vault-behind-a-firewall"></a>防火牆後方的 Azure Key Vault

使用 [Azure AD 認證](disk-encryption-windows-aad.md#)啟用加密時，目標 VM 必須允許連線到 Azure Active Directory 端點和金鑰保存庫端點。 目前的 Azure Active Directory 驗證端點是在 [Microsoft 365 url 和 IP 位址範圍](/microsoft-365/enterprise/urls-and-ip-address-ranges) 檔的56和59章節中進行維護。 如需金鑰保存庫的指示，請參閱文件中的如何[在防火牆後存取 Azure 金鑰保存庫](../../key-vault/general/access-behind-firewall.md)。

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務 
VM 必須能夠存取 [Azure 執行個體中繼資料服務](../windows/instance-metadata-service.md)端點；此端點會使用只能從 VM 內存取且無法路由的已知 IP 位址 (`169.254.169.254`)。  不支援將本機 HTTP 流量更改為此位址的 Proxy 設定 (例如，新增 X-Forwarded-For 標頭)。

## <a name="troubleshooting-windows-server-2016-server-core"></a>針對 Windows Server 2016 Server Core 進行疑難排解

在 Windows Server 2016 Server Core 上，預設無法使用 bdehdcfg 元件。 Azure 磁碟加密需要此元件。 此元件用來從作業系統磁碟區中分割系統磁碟區，在 VM 的存留時間內僅會進行一次。 在之後的加密作業期間，不需要這些二進位檔。

若要解決這個問題，請將下列 4 個檔案從 Windows Server 2016 資料中心 VM 複製到 Server Core 的同一個位置：

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. 輸入下列命令：

   ```
   bdehdcfg.exe -target default
   ```

1. 此命令會建立一個 550MB 系統磁碟分割。 重新啟動系統。

1. 使用 DiskPart 來檢查磁碟區，然後再繼續作業。  

例如：

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>針對加密狀態進行疑難排解 

即使已在 VM 內解密磁碟，入口網站也可能會將其顯示為加密。  當使用低層級命令直接從 VM 內解密磁碟，而不是使用較高層級的 Azure 磁碟加密管理命令時，就會發生這種情況。  較高層級的命令不只會從 VM 內解密磁碟，也會在 VM 外部更新與 VM 建立關聯的重要平台層級加密設定和延伸模組設定。  如果內外未保持一致，則平台將無法回報加密狀態或適當地佈建 VM。

若要停用 PowerShell 的 Azure 磁碟加密，請於 [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) 後接著使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)。 若在停用加密之前執行 Remove-AzVMDiskEncryptionExtension 將會導致失敗。

若要停用 CLI 的 Azure 磁碟加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption)。 

## 



## <a name="next-steps"></a>後續步驟

在本文件中，您已深入了解 Azure 磁碟加密中的一些常見問題，以及如何進行疑難排解。 如需此服務和其功能的相關資訊，請參閱下列文章：

- [在 Azure 資訊安全中心套用磁碟加密](../../security-center/security-center-virtual-machine-protection.md)
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)
