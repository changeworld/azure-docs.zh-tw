---
title: 使用 Azure Site Recovery 從 Azure 容錯回復 Azure VMware 解決方案 VM
description: 了解在災害復原期間，如何在容錯移轉至 Azure 之後，容錯回復至 Azure VMware 解決方案私人雲端。
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814195"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>將 VM 容錯回復至 Azure VMware 解決方案私人雲端

本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md)，在將 Azure VMware 解決方案 VM [容錯移轉](avs-tutorial-failover.md)至 Azure 後，將 Azure VM 容錯回復至 Azure VMware 解決方案私人雲端。 容錯回復之後，請啟用複寫，讓 Azure VMware 解決方案 VM 開始複寫至 Azure。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

1. 了解 [VMware 容錯回復](failover-failback-overview.md#vmwarephysical-reprotectionfailback)。 
2. 確定您已檢閱並完成[準備容錯回復](vmware-azure-prepare-failback.md)的步驟，且所有必要元件均已部署完成。 必要元件包括 Azure 中的處理序伺服器、主要目標伺服器，以及用於容錯回復的 VPN 站對站連線 (或 ExpressRoute 私人對等互連)。
3. 確定您已完成重新保護和容錯回復的[需求](avs-tutorial-reprotect.md#before-you-begin)，並已為 Azure VM [啟用重新保護](avs-tutorial-reprotect.md#enable-reprotection)，使其會從 Azure 複寫至 Azure VMware 解決方案私人雲端。 VM 必須處於已複寫狀態，才能進行容錯回復。




## <a name="run-a-failover-to-fail-back"></a>執行容錯移轉以進行容錯回復

1. 確定 Azure VM 已重新保護並複寫至 Azure VMware 解決方案私人雲端。
    - VM 必須至少有一個復原點，才能進行容錯回復。
    - 如果您容錯回復復原方案，則方案中的所有機器都應該至少有一個復原點。
2. 在保存庫 > [複寫的項目] 中，選取 VM。 以滑鼠右鍵按一下 VM > [非計劃性容錯移轉]。
3. 在 [確認容錯移轉]**** 中，確認容錯移轉方向 (以 Azure 為來源)。
4. 選取您要用於容錯移轉的復原點。
    - 建議您使用 [最新] 復原點。 應用程式一致復原點會在最近的復原點之後，並造成部分資料遺失。
    - **最新**復原點具有當機時保持一致的特性。
    - 使用**最新**復原點，VM 會容錯移轉到其最新的可用時間點。 如果您的復原方案內有多 VM 一致性的複寫群組，則群組中的每個 VM 都會容錯移轉到其獨立的最新時間點。
    - 如果您使用應用程式一致的復原點，則每個 VM 都會容錯回復到其最新的可用時間點。 如果復原方案有複寫群組，則每個群組都會復原到其一般可用的復原點。
5. 容錯移轉開始。 Site Recovery 將 Azure VM 關機。
6. 在容錯移轉完成之後，請檢查一切是否如預期般運作。 檢查 Azure VM 是否已關機。 
7. 全都確認過後，以滑鼠右鍵按一下 VM > [認可]，以完成容錯移轉程序。 認可會移除已容錯移轉的 Azure VM。 

> [!NOTE]
> 對於 Windows VM，Site Recovery 會在容錯移轉期間停用 VMware 工具。 在 Windows VM 容錯回復期間，VMware 工具會重新啟用。 




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>從 Azure VMware 解決方案移至 Azure 來重新保護

認可容錯回復之後，Azure VM 就會遭到刪除。 VM 已回到 Azure VMware 解決方案私人雲端，但未受到保護。 若要重新開始將 VM 複寫至 Azure，請如下所示進行操作：

1. 在保存庫 > [複寫的項目] 中，選取已容錯回復的 VM，然後選取 [重新保護]。
2. 指定用來將資料傳送回 Azure 的處理序伺服器。
3. 選取 [確定]**** 以開始重新保護作業。

> [!NOTE]
> Azure VMware 解決方案 VM 啟動之後，最多需要 15 分鐘的時間，代理程式才會註冊回到設定伺服器。 在這段時間，重新保護會失敗，並傳回錯誤訊息以指出未安裝代理程式。 如果發生這種情況，請等候幾分鐘，然後再重新保護。

## <a name="next-steps"></a>下一步

重新保護作業完成之後，Azure VMware 解決方案 VM 就會複寫到 Azure。 如有需要，您可以將 Azure 作為目的地來[執行另一個容錯移轉](avs-tutorial-failover.md)。

