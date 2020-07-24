---
title: 刪除 Azure Site Recovery 保存庫
description: 了解如何刪除針對 Azure Site Recovery 所設定的復原服務保存庫
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 8237888d46e1f1ad48e678234cf1edce3558876a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089578"
---
# <a name="delete-a-site-recovery-services-vault"></a>刪除 Site Recovery 復原服務保存庫

本文說明如何刪除 Site Recovery 的復原服務保存庫。 若要刪除 Azure 備份使用的保存庫，請參閱[刪除 Azure 中的備份保存庫](../backup/backup-azure-delete-vault.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>開始之前

在您可以刪除保存庫之前，您必須先移除已註冊的伺服器和保存庫中的專案。 您需要移除的內容取決於您已部署的複寫案例。 


## <a name="delete-a-vault-azure-vm-to-azure"></a>刪除保存庫-Azure VM 至 Azure

1. 請遵循[這些指示](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)來刪除所有受保護的 vm。
2. 然後，刪除保存庫。

## <a name="delete-a-vault-vmware-vm-to-azure"></a>刪除保存庫-VMware VM 至 Azure

1. 請遵循[這些指示](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)來刪除所有受保護的 vm。
2. 請遵循[下列步驟](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)來刪除所有複寫原則。
3. 使用[這些步驟](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)刪除 vCenter 的參考。
4. 請遵循[這些指示](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)來解除委任設定伺服器。
5. 然後，刪除保存庫。


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>刪除保存庫-Hyper-v VM （含 VMM）至 Azure

1. 請遵循[下列步驟](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)來刪除 SYSTEM Center VMM 所管理的 hyper-v vm。
2. 解除關聯並刪除所有複寫原則。 在您的保存庫中**Site Recovery Infrastructure**執行此動作  >  **，> System Center VMM**  >  **複寫原則**的 Site Recovery 基礎結構。
3. 請遵循[下列步驟](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)來取消註冊已連線的 VMM 伺服器。
4. 然後，刪除保存庫。

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>刪除保存庫-Hyper-v VM 至 Azure

1. 請遵循[下列步驟](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)來刪除所有受保護的 vm。
2. 解除關聯並刪除所有複寫原則。 在您的保存庫中**Site Recovery Infrastructure**執行此動作  >  **，> hyper-v 網站**  >  **複寫原則**的 Site Recovery 基礎結構。
3. 請遵循[這些指示](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)來取消註冊 hyper-v 主機。
4. 刪除 Hyper-V 站台。
5. 然後，刪除保存庫。


## <a name="use-powershell-to-force-delete-the-vault"></a>使用 PowerShell 強制刪除保存庫 

> [!Important]
> 如果您要測試產品，且不在乎資料遺失問題，可使用強制刪除方法快速移除保存庫及其所有相依性。
> PowerShell 命令會刪除保存庫的所有內容，而且**無法還原**。

即使有受保護的項目也要刪除 Site Recovery 保存庫，請使用這些命令：

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

深入瞭解[new-azrecoveryservicesvault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)和[Remove-new-azrecoveryservicesvault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)。
