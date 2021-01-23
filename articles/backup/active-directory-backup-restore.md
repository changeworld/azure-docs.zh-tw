---
title: 備份與還原 Active Directory
description: 瞭解如何備份和還原 Active Directory 網域控制站。
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733548"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>備份和還原 Active Directory 網域控制站

備份 Active Directory，以及確保在發生損毀、洩漏或嚴重損壞的情況下成功還原，是 Active Directory 維護的重要部分。

本文概述使用 Azure 備份備份和還原 Active Directory 網域控制站的適當程式，無論是 Azure 虛擬機器或內部部署伺服器。 它會討論您需要在備份時將整個網域控制站還原至其狀態的案例。 若要查看哪一個還原案例適合您，請參閱 [這篇文章](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)。  

>[!NOTE]
> 本文不會討論如何從 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)還原專案。 如需還原 Azure Active Directory 使用者的詳細資訊，請參閱 [這篇文章](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="best-practices"></a>最佳作法

- 請確定至少有一個網域控制站已備份。 如果您備份一個以上的網域控制站，請確定所有擁有 [FSMO (彈性單一主機操作](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) 的網域控制站都已備份) 角色。

- 經常備份 Active Directory。 備份絕不能超過標記存留期 (預設為60天) ，因為早于標記存留期的物件將會「被標記」，且不再被視為有效。

- 具有明確的嚴重損壞修復計畫，其中包含如何還原網域控制站的指示。 若要準備還原 Active Directory 樹系，請閱讀 [Active Directory 樹](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)系復原指南。

- 如果您需要還原網域控制站，並在網域中擁有剩餘的運作中網域控制站，您可以建立新的伺服器，而不是從備份還原。 將 **Active Directory Domain Services** 伺服器角色新增至新的伺服器，使其成為現有網域中的網域控制站。 然後 Active Directory 的資料會複寫到新的伺服器。 若要從 Active Directory 移除先前的網域控制站，請遵循本文 [中](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) 的步驟來執行元資料清理。

>[!NOTE]
>Azure 備份不包含 Active Directory 的專案層級還原。 如果您想要還原已刪除的物件，而且可以存取網域控制站，請使用 [Active Directory 資源回收筒](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)。 如果無法使用該方法，您可以使用網域控制站備份來還原已刪除的物件與 **ntdsutil.exe** 工具 [，如下所述。](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)
>
>如需執行 SYSVOL 的授權還原的詳細資訊，請參閱 [這篇文章](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)。

## <a name="backing-up-azure-vm-domain-controllers"></a>備份 Azure VM 網域控制站

如果網域控制站是 Azure VM，您可以使用 [AZURE Vm 備份](backup-azure-vms-introduction.md)來備份伺服器。

請參閱 [虛擬網域控制站的操作考慮](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) ，以確保您的 Azure VM 網域控制站 (和未來的還原) 成功備份。

## <a name="backing-up-on-premises-domain-controllers"></a>備份內部部署網域控制站

若要備份內部部署網域控制站，您需要備份伺服器的系統狀態資料。

- 如果您使用的是 MARS，請依照下列 [指示](backup-azure-system-state.md)進行。
- 如果您使用 MABS (Azure 備份伺服器) ，請遵循下列 [指示](backup-mabs-system-state-and-bmr.md)。

>[!NOTE]
> 不支援將內部部署網域控制站 (從系統狀態或從 Vm) 到 Azure 雲端的還原。 如果您想要從內部部署 Active Directory 環境容錯移轉至 Azure 的選項，請考慮使用 [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)。

## <a name="restoring-active-directory"></a>還原 Active Directory

您可以透過下列兩種模式之一來還原 Active Directory 資料： **權威** 或非 **授權**。 在授權還原中，還原的 Active Directory 資料將會覆寫在樹系中其他網域控制站上找到的資料。

不過，在此案例中，我們要重建現有網域中的網域控制站，因此應該執行非系統 **授權** 還原。

在還原期間，伺服器將會以目錄服務還原模式啟動 (DSRM) 。 您必須提供「目錄服務」還原模式的系統管理員密碼。

>[!NOTE]
>如果忘記 DSRM 密碼，您可以使用 [這些指示](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))來重設它。

### <a name="restoring-azure-vm-domain-controllers"></a>還原 Azure VM 網域控制站

若要還原 Azure VM 網域控制站，請參閱 [還原網域控制站 vm](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)。

如果您要在單一網域中還原單一網域控制站 VM 或多個網域控制站 Vm，請像任何其他 VM 一樣還原它們。 我們也提供了目錄服務還原模式 (DSRM)，因此，您可以進行所有的 Active Directory 復原案例。

如果您需要在多個網域設定中還原單一網域控制站 VM，請 [使用 PowerShell](backup-azure-vms-automation.md#restore-the-disks)來復原磁碟並建立 VM。

如果您要還原網域中最後一個剩餘的網域控制站，或還原一個樹系中的多個網域，則建議使用 [樹](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)系復原。

>[!NOTE]
> 從 Windows 2012 的虛擬網域控制站，則會使用以 [虛擬化為基礎的保護](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards)。 藉由這些保護，Active directory 可理解 VM 是否已還原為網域控制站，並執行還原 Active Directory 資料所需的步驟。

### <a name="restoring-on-premises-domain-controllers"></a>還原內部部署網域控制站

若要還原內部部署網域控制站，請遵循中的指示，將系統狀態還原到 Windows Server，使用 [網域控制站上系統狀態復原的特殊考慮](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)指引。

## <a name="next-steps"></a>後續步驟

- [Azure 備份的支援矩陣](backup-support-matrix.md)