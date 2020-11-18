---
title: 使用 Azure Site Recovery 進行災害復原期間將 Azure VM 容錯回復至主要區域的教學課程。
description: 了解使用 Azure Site Recovery 將 Azure VM 容錯回復至主要區域的教學課程。
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393858"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>教學課程：將 Azure VM 容錯回復至主要區域

將 Azure VM 容錯移轉到次要 Azure 區域之後，請遵循此教學課程，使用 [Azure Site Recovery](site-recovery-overview.md) 將 VM 容錯移轉到主要 Azure 區域。  在本文中，您將學會如何：

> [!div class="checklist"]
> 
> * 檢閱必要條件。
> * 在次要區域中容錯回復 VM。
> * 重新保護回復到次要區域的主要 VM。
> 
> [!NOTE]
> 本教學課程說明如何使用最少的步驟進行容錯回復。 如果您想要使用完整設定來執行容錯移轉，請了解 Azure VM [網路](azure-to-azure-about-networking.md)、[自動化](azure-to-azure-powershell.md)和[疑難排解](azure-to-azure-troubleshoot-errors.md)。



## <a name="prerequisites"></a>必要條件

在開始本教學課程之前，您應該已經：

1. 至少為一部 Azure VM [設定複寫](azure-to-azure-tutorial-enable-replication.md)，並且為其嘗試[災害復原演練](azure-to-azure-tutorial-dr-drill.md)。
2. 從主要區域[容錯移轉 VM](azure-to-azure-tutorial-failover-failback.md) 至次要區域並且重新保護，讓其從次要區域複寫到主要區域。 
3. 確認主要區域可供使用，而且您可以建立及存取其中的新資源。

## <a name="fail-back-to-the-primary-region"></a>容錯回復至主要區域

重新保護 VM 之後，您可以視需要容錯回復到主要區域。

1. 在保存庫 > [複寫的項目] 中，選取 VM。

2. 在 VM 概觀頁面上，檢查 VM 是否狀況良好，以及同步處理是否已完成，然後再執行容錯移轉。 VM 應該處於「受保護」狀態。

    ![VM 概觀頁面，顯示處於受保護狀態的 VM](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. 在概觀頁面上，選取 [容錯移轉]。 因為我們不會在這次進行測試容錯移轉，因此系統會提示進行驗證。

    [頁面顯示我們同意在沒有測試容錯移轉的情況下執行容錯移轉](./media/azure-to-azure-tutorial-failback/no-test.png)

4. 在 [容錯移轉] 中，請注意從次要區域到主要區域的方向，然後選取復原點。 目標 (主要區域) 中的 Azure VM 是使用此點的資料建立。
   - **最近處理**：使用 Site Recovery 所處理的最新復原點。 隨即顯示時間戳記。 無須花費時間處理資料，因此可以提供低復原時間目標 (RTO)。
   -  **最新**：處理傳送到 Site Recovery 的所有資料，先為每部 VM 建立復原點後再進行容錯移轉。 提供最低的復原點目標 (RPO)，因為所有資料會在觸發容錯移轉時複寫到 Site Recovery。
   - **最新應用程式一致**：此選項會將 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - [自訂]：容錯移轉至特定復原點。 只有當您容錯移轉單一 VM，而且未使用復原方案時，才可以使用自訂。

    > [!NOTE]
    > 如果您容錯移轉 VM 並且在啟用 VM 的複寫之後新增磁碟，複寫點就會顯示可用於復原的磁碟。 例如，在您新增第二個磁碟之前所建立的複寫點，會顯示為「1 個磁碟，共 2 個」。

4. 如果想在啟動容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]。 關機有助於確保不會遺失資料。 即使關機失敗，仍會繼續容錯移轉。 

    ![容錯移轉設定頁面](./media/azure-to-azure-tutorial-failback/failover.png)    

3. 若要開始進行容錯移轉，請選取 [確定]。
4. 在通知中監視容錯移轉。

    ![容錯移轉進度通知](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![容錯移轉成功通知](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>重新保護 VM

將 VM 容錯回復到主要區域之後，您必須重新保護 VM，這樣才會再次開始複寫至次要區域。

1. 在 VM 的 [概觀] 頁面中，選取 [重新保護]。

    ![從主要區域重新保護的按鈕](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. 檢閱主要區域的目標設定。 Site Recovery 會在重新保護作業期間建立標記為新的資源。
3. 選取 [確定] 以開始重新保護程序。 程序會將初始資料傳送至目標位置，然後將 VM 的差異資訊複寫至目標。

     ![顯示複寫設定的頁面](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. 在通知中監視重新保護進度。 

    ![重新保護進度通知](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [重新保護進度通知](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>清除資源

針對具有受控磁碟的 VM，在容錯回復完成且 VM 針對從主要區域到次要區域的複寫進行重新保護時，Site Recovery 會自動清除次要災害復原區域中的機器。 您不需要手動刪除次要區域中的 VM 和 NIC。 使用非受控磁碟的 VM 不會清除。

如果您在容錯回復之後完全停用複寫，Site Recovery 會清除受其保護的機器。 在此情況下，也會清除 VM (未使用受控磁碟) 的磁碟。 
 
## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 VM 從次要區域容錯回復到主要區域。 這是程序中的最後一個步驟，其中包括啟用 VM 的複寫、嘗試進行災害復原演練、從主要區域容錯移轉至次要區域，最後再進行容錯回復。

> [!div class="nextstepaction"]
> 現在，請嘗試對[內部部署 VM](vmware-azure-tutorial-prepare-on-premises.md) 的 Azure 進行災害復原

