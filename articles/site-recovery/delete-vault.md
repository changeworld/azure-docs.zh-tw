---
title: 刪除 Azure 網站恢復保存庫
description: 了解如何刪除針對 Azure Site Recovery 所設定的復原服務保存庫
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894968"
---
# <a name="delete-a-site-recovery-services-vault"></a>刪除 Site Recovery 復原服務保存庫

本文介紹如何刪除用於網站恢復的恢復服務保存庫。 若要刪除 Azure 備份使用的保存庫，請參閱[刪除 Azure 中的備份保存庫](../backup/backup-azure-delete-vault.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>開始之前

必須先刪除已註冊的伺服器和保存庫中的專案，然後才能刪除保存庫。 需要刪除的內容取決於已部署的複製方案。 


## <a name="delete-a-vault-azure-vm-to-azure"></a>將保存庫 Azure VM 刪除到 Azure

1. 按照[這些說明](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)刪除所有受保護的 VM。
2. 然後，刪除保存庫。

## <a name="delete-a-vault-vmware-vm-to-azure"></a>將保存庫 VMware VM 刪除到 Azure

1. 按照[這些說明](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)刪除所有受保護的 VM。
2. 按照[以下步驟](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)刪除所有複寫原則。
3. 使用這些步驟刪除對 vCenter[的](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)引用。
4. 按照[這些說明](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)停用佈建服務器。
5. 然後，刪除保存庫。


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>將保存庫-Hyper-V VM（使用 VMM）刪除到 Azure

1. 按照[以下步驟](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)刪除由系統中心 VMM 管理的超 VM。
2. 取消關聯並刪除所有複寫原則。 在保存庫中執行此操作>**系統中心 VMM** > **複寫原則****的網站恢復基礎結構** > 。
3. 按照[以下步驟](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)取消註冊已連接的 VMM 伺服器。
4. 然後，刪除保存庫。

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>將保存庫-Hyper-V VM 刪除到 Azure

1. 按照[以下步驟](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)刪除所有受保護的 VM。
2. 取消關聯並刪除所有複寫原則。 在**超級 V 網站** > **複寫原則**的保存庫>**網站恢復基礎結構** > 中執行此操作。
3. 按照[這些說明](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)取消註冊 Hyper-V 主機。
4. 刪除 Hyper-V 站台。
5. 然後，刪除保存庫。


## <a name="use-powershell-to-force-delete-the-vault"></a>使用 PowerShell 強制刪除保存庫 

> [!Important]
> 如果您要測試產品，且不在乎資料遺失問題，可使用強制刪除方法快速移除保存庫及其所有相依性。
> PowerShell 命令刪除保存庫的所有內容，**並且不可逆**。

即使有受保護的項目也要刪除 Site Recovery 保存庫，請使用這些命令：

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

詳細瞭解[獲取-Az 恢復服務Vault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)和[刪除-Az 恢復服務Vault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)。
