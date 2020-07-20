---
title: Azure 入口網站-使用 SSE 受控磁片啟用客戶管理的金鑰
description: 透過 Azure 入口網站，在您的受控磁片上啟用客戶管理的金鑰。
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235878"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>使用伺服器端加密管理的磁片啟用客戶管理的金鑰-入口網站

Azure 磁碟儲存體可讓您在使用伺服器端加密時管理您自己的金鑰， (適用于受控磁片的 SSE) （如果您選擇的話）。 如需搭配客戶管理的金鑰和其他受控磁片加密類型之 SSE 的概念資訊，請參閱磁片加密一文中的[客戶管理的金鑰](disk-encryption.md#customer-managed-keys)一節。

## <a name="restrictions"></a>限制

目前，客戶管理的金鑰具有下列限制：

- 如果您的磁碟已啟用此功能，則無法停用。
    如果您需要解決此問題，必須[將所有資料複製到](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)完全不同且未使用客戶管理的金鑰的受控磁碟。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 客戶管理的金鑰須依賴 Azure 資源的受控識別 (Azure Active Directory (Azure AD) 的一項功能)。 當您設定客戶管理的金鑰時，受控識別會在幕後自動指派給您的資源。 如果您之後將訂用帳戶、資源群組或受控磁片從一個 Azure AD 目錄移到另一個，則與受控磁片相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱[在 Azure AD 目錄之間移轉訂用帳戶](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="next-steps"></a>後續步驟

- [探索 Azure Resource Manager 範本，以使用客戶管理的金鑰來建立加密的磁片](https://github.com/ramankumarlive/manageddiskscmkpreview)) 
- [使用已啟用客戶管理的金鑰的磁碟來複寫機器](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 設定 VMWare VM 至 Azure 的災害復原](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure Resource Manager，針對 Hyper-V VM 設定 Azure 的災害復原](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
