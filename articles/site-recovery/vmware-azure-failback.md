---
title: 使用 Azure Site Recovery 從 Azure 容錯回復 VMware Vm/實體伺服器
description: 了解如何在從 VMware VM 和實體伺服器至 Azure 的災害復原期間，在容錯移轉至 Azure 後容錯回復至內部部署網站。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75495319"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>將 VMware Vm 容錯回復至內部部署網站

本文說明如何使用[Azure Site Recovery](site-recovery-overview.md)將內部部署 vm[容錯移轉](site-recovery-failover.md)至 Azure 後，將 azure vm 容錯回復至內部部署網站。 容錯回復至內部部署之後，您會啟用複寫，讓內部部署 Vm 開始複寫至 Azure。

## <a name="before-you-start"></a>開始之前

1. 瞭解[VMware 容錯回復](failover-failback-overview.md#vmwarephysical-reprotectionfailback)。 
2. 請確定您已審查並完成[準備容錯回復](vmware-azure-prepare-failback.md)的步驟，並已部署所有必要的元件。 元件包括 Azure 中的進程伺服器、內部部署主要目標伺服器，以及用於容錯回復的 VPN 站對站連線（或 ExpressRoute 私人對等互連）。
3. 請確定您已完成重新保護和容錯回復的[需求](vmware-azure-reprotect.md#before-you-begin)，而且您已啟用 azure vm 的[重新保護](vmware-azure-reprotect.md#enable-reprotection)，以便從 azure 複寫到內部部署網站。 Vm 必須處於已複寫狀態，才能進行容錯回復。




## <a name="run-a-failover-to-fail-back"></a>執行容錯移轉以容錯回復

1. 請確定 Azure Vm 已重新保護並複寫至內部部署網站。 
    - VM 必須至少有一個復原點，才能進行容錯回復。
    - 如果您容錯回復復原方案，則方案中的所有機器都應該至少有一個復原點。
2. 在 [保存庫] > [複寫的**專案**] 中，選取 VM。 以滑鼠右鍵按一下 VM > 非**計畫的容錯移轉**。
3. 在 [確認容錯移轉]**** 中，確認容錯移轉方向 (以 Azure 為來源)。
4. 選取您要用於容錯移轉的復原點。
    - 我們建議您使用**最新**的復原點。 應用程式一致的點會在最近的時間點後方，並導致部分資料遺失。
    - **最新**的是損毀一致復原點。
    - 透過**最新**的，VM 會故障切換到其最新的可用時間點。 如果您的復原方案內有多部 VM 一致性的複寫群組，則群組中的每個 VM 都會故障轉換到其獨立的最新時間點。
    - 如果您使用應用程式一致的復原點，每個 VM 會容錯回復到其最新的可用點。 如果復原方案有複寫群組，則每個群組都會復原到其一般可用的復原點。
5. 開始容錯移轉。 Site Recovery 會關閉 Azure Vm。
6. 在容錯移轉完成之後，請檢查一切是否如預期般運作。 檢查 Azure Vm 是否已關閉。 
7. 當所有專案都經過驗證後，以滑鼠右鍵按一下 VM >**認可**]，完成容錯移轉程式。 Commit 會移除已故障的 Azure VM。 

> [!NOTE]
> 對於 Windows Vm，Site Recovery 會在容錯移轉期間停用 VMware 工具。 在 Windows VM 容錯回復期間，VMware 工具會重新啟用。 




## <a name="reprotect-from-on-premises-to-azure"></a>從內部部署移至 Azure 來重新保護

認可容錯回復之後，會刪除 Azure Vm。 VM 已回到內部部署網站，但未受到保護。 若要再次開始將 Vm 複寫至 Azure，如下所示：

1. 在保存庫中 > 已複寫的**專案**] 中，選取 [容錯回復 vm]，然後選取 [**重新保護**]。
2. 指定用來將資料傳送回 Azure 的進程伺服器。
3. 選取 [確定]**** 以開始重新保護作業。

> [!NOTE]
> 當內部部署 VM 啟動之後，代理程式需要15分鐘的時間才能向後註冊到設定伺服器。 在這段時間，重新保護會失敗，並傳回錯誤訊息以指出未安裝代理程式。 如果發生這種情況，請等候幾分鐘，再重新保護。

## <a name="next-steps"></a>後續步驟

重新保護作業完成之後，內部部署 VM 會複寫到 Azure。 如有需要，您可以[執行另一個容錯移轉](site-recovery-failover.md)至 Azure。

