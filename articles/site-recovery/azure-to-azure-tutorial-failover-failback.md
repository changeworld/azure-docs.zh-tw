---
title: 使用 Azure Site Recovery 服務將複寫的 Azure VM 容錯移轉到次要 Azure 區域並重新保護，以進行災害復原。
description: 了解如何使用 Azure Site Recovery 服務將複寫的 Azure VM 容錯移轉到次要 Azure 區域並重新保護，以進行災害復原。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8d38aa513b0829c2626fcd4a92c40faabff1f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502387"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>在區域之間容錯移轉及重新保護 Azure VM

本教學課程說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 Azure 虛擬機器 (VM) 容錯移轉到次要 Azure 區域。 在容錯移轉之後，您會重新保護 VM。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 容錯移轉 Azure VM
> * 重新保護次要 Azure VM，使其能複寫到主要區域。

> [!NOTE]
> 本教學課程包含採用預設設定和最低自訂的最簡單路徑。 如需更複雜的案例，請使用「操作說明」之下關於 Azure VM 的文章。


## <a name="prerequisites"></a>Prerequisites

- 開始之前，請檢閱關於容錯移轉的[相關常見問題](site-recovery-faq.md#failover)。
- 確定您已完成[災害復原演練](azure-to-azure-tutorial-dr-drill.md)，檢查一切是否如預期般運作。
- 執行測試容錯移轉之前，請確認 VM 屬性。 VM 必須符合 [Azure 需求](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)。

## <a name="run-a-failover-to-the-secondary-region"></a>執行容錯移轉至次要區域

1. 在 [複寫的項目]  中，選取您要容錯移轉的 VM > [容錯移轉]  。

   ![顯示 VM 容錯移轉選項的螢幕擷取畫面。](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. 在 [容錯移轉]  中，選取要容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：

   * **最新** (預設值)：在 Site Recovery 服務中處理所有資料，並提供最低的復原點目標 (RPO)。
   * **最近處理**：將虛擬機器還原到 Site Recovery 服務已處理的最新復原點。
   * [自訂]  ：容錯移轉至特定復原點。 此選項適合用於執行測試容錯移轉。

3. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]  。 關機有助於確保不會遺失資料。 即使關機失敗，仍會繼續容錯移轉。 Site Recovery 並不會在容錯移轉後清除來源。

4. 在 [作業]  頁面上追蹤容錯移轉進度。

5. 容錯移轉之後，登入虛擬機器進行驗證。 如果您想要前往虛擬機器的另一個復原點，您可以使用 [變更復原點]  選項。

6. 一旦您滿意容錯移轉的虛擬機器，您可以 [認可]  容錯移轉。
   認可會刪除服務可用的所有復原點。 您現在無法變更復原點。

> [!NOTE]
> 您容錯移轉 VM，並且在啟用 VM 的複寫之後新增磁碟時，複寫點就會顯示可用於復原的磁碟。 例如，如果 VM 有單一磁碟，而且您新增磁碟，則新增磁碟之前建立的複寫點就會顯示複寫點包含「1 個磁碟，共 2 個」。

![顯示已新增磁碟容錯移轉的螢幕擷取畫面。](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>重新保護次要 VM

在 VM 容錯移轉之後，您需要重新保護它，使其能複寫回到主要區域。

1. 確定 VM 處於 [已認可容錯移轉]  狀態，並檢查主要區域是否使用，而且您可以建立及存取其中的新資源。
2. 在 [保存庫]   > [已複寫的項目]  中，以滑鼠右鍵按一下已容錯移轉的 VM，然後選取 [重新保護]  。

   ![VM 的重新保護選項螢幕擷取畫面。](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. 確認已經選取保護方向 (次要到主要區域)。
3. 檢閱**資源群組、網路、儲存體和可用性設定組**資訊。 在重新保護作業期間會建立任何標記為新的資源。
4. 按一下 [確定]  以觸發重新保護作業。 這項作業會在目標網站植入最新資料。 然後，它會將差異複寫到主要區域。 VM 目前處於受保護的狀態。

## <a name="next-steps"></a>後續步驟
- 在重新保護之後，[了解如何](azure-to-azure-tutorial-failback.md)容錯回復到可用的主要區域。
- [深入了解](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)重新保護流程。
