---
title: Azure 磁碟加密疑難排解指南
description: 本文為 Windows VM 提供了 Microsoft Azure 磁片加密的故障排除提示。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d4e76f4d02b0287770243bfddf995a19f90d232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749441"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁碟加密疑難排解指南

本指南適用於組織採用 Azure 磁碟加密的 IT 專業人員、資訊安全性分析師和雲端系統管理員。 本文旨在協助磁碟加密相關問題的疑難排解。

在採取以下任一步驟之前，首先確保嘗試加密的 VM 屬於[支援的 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)，並且您滿足所有先決條件：

- [網路需求](disk-encryption-overview.md#networking-requirements)
- [群組原則要求](disk-encryption-overview.md#group-policy-requirements)
- [加密金鑰存儲要求](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>針對防火牆後方的 Azure 磁碟加密進行疑難排解

當連線能力受防火牆、Proxy 需求或網路安全性群組 (NSG) 設定限制時，擴充功能執行所需工作的能力可能會受到中斷。 這個中斷會產生狀態訊息 (例如「無法在 VM 上使用擴充功能狀態」)。 在預期的情節中，無法完成加密。 以下各節有一些常見的防火牆問題，您可能需要進行調查。

### <a name="network-security-groups"></a>網路安全性群組
任何套用的網路安全性群組設定仍然必須允許端點，從而符合磁碟加密的記載網路設定[必要條件](disk-encryption-overview.md#networking-requirements)。

### <a name="azure-key-vault-behind-a-firewall"></a>防火牆後方的 Azure Key Vault

使用 [Azure AD 認證](disk-encryption-windows-aad.md#)啟用加密時，目標 VM 必須允許連線到 Azure Active Directory 端點和金鑰保存庫端點。 目前的 Azure Active Directory 驗證端點列在 [Office 365 URL 與 IP 位址範圍](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges)文件的第 56 和 59 節。 如需金鑰保存庫的指示，請參閱文件中的如何[在防火牆後存取 Azure 金鑰保存庫](../../key-vault/key-vault-access-behind-firewall.md)。

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務 
VM 必須能夠存取 [Azure 執行個體中繼資料服務](../windows/instance-metadata-service.md)端點；此端點會使用只能從 VM 內存取且無法路由的已知 IP 位址 (`169.254.169.254`)。  不支援將本地 HTTP 流量更改到此位址的代理配置（例如，添加 X 轉發-for 標頭）。

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

門戶可能會將磁片顯示為加密磁片，即使磁片在 VM 中未加密。  當使用低級命令從 VM 中直接取消加密磁片，而不是使用更高級別的 Azure 磁片加密管理命令時，可能會發生這種情況。  更高級別的命令不僅從 VM 中取消加密磁片，而且在 VM 外部也會更新與 VM 關聯的重要平臺級加密設定和擴展設置。  如果這些不對齊，平臺將無法報告加密狀態或正確預配 VM。   

要禁用使用 PowerShell 的 Azure 磁片加密，請使用[禁用 AzVM 磁片加密](/powershell/module/az.compute/disable-azvmdiskencryption)，然後[刪除-AzVM磁片加密擴展](/powershell/module/az.compute/remove-azvmdiskencryptionextension)。 在禁用加密之前運行刪除-AzVM 磁片加密擴展將失敗。

要禁用使用 CLI 的 Azure 磁片加密，請使用[az vm 加密禁用](/cli/azure/vm/encryption)。 

## <a name="next-steps"></a>後續步驟

在本文件中，您已深入了解 Azure 磁碟加密中的一些常見問題，以及如何進行疑難排解。 如需此服務和其功能的相關資訊，請參閱下列文章：

- [在 Azure 資訊安全中心套用磁碟加密](../../security-center/security-center-apply-disk-encryption.md)
- [待用 Azure 資料加密](../../security/fundamentals/encryption-atrest.md)
