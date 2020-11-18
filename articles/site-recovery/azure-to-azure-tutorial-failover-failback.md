---
title: 使用 Azure Site Recovery 將 Azure VM 容錯移轉至次要區域以進行災害復原的教學課程。
description: 了解如何使用 Azure Site Recovery 服務將複寫的 Azure VM 容錯移轉至次要 Azure 區域並重新保護，以進行災害復原的教學課程。
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392712"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>教學課程：將 Azure VM 容錯移轉至次要區域

了解如何使用 [Azure Site Recovery](site-recovery-overview.md)，將已啟用災害復原的 Azure VM 容錯移轉至次要 Azure 區域。 進行容錯移轉後，您可以重新保護目標區域中的 VM，使其可複寫回主要區域。 在本文中，您將學會如何：

> [!div class="checklist"]
> * 檢查必要條件
> * 確認 VM 設定
> * 執行容錯移轉至次要區域
> * 開始將 VM 複寫回主要區域。


> [!NOTE]
> 本教學課程說明如何使用最少的步驟進行 VM 的容錯移轉。 如果您想要使用完整設定來執行容錯移轉，請了解 Azure VM [網路](azure-to-azure-about-networking.md)、[自動化](azure-to-azure-powershell.md)和[疑難排解](azure-to-azure-troubleshoot-errors.md)。



## <a name="prerequisites"></a>必要條件

在開始本教學課程之前，您應該已經：

1. 設定一或多個 Azure VM 的複寫。 若尚未這麼做，請[完成本系列中的第一個教學課程](azure-to-azure-tutorial-enable-replication.md)以執行此動作。
2. 建議您對複寫的 VM [執行災害復原演練](azure-to-azure-tutorial-dr-drill.md)。 在執行完整容錯移轉之前進行演練，有助於確保一切都能如預期運作，而不會影響您的實際執行環境。 


## <a name="verify-the-vm-settings"></a>確認 VM 設定

1. 在保存庫 > [複寫的項目] 中，選取 VM。

    ![在 [概觀] 頁面上開啟 VM 屬性的選項](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. 在執行容錯移轉之前，在 VM [概觀] 頁面上確認 VM 已受到保護且狀況良好。
    ![確認 VM 屬性和狀態的頁面](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. 進行容錯移轉之前，請先確認：
    - VM 執行支援的 [Windows](azure-to-azure-support-matrix.md#windows) 或 [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) 作業系統。
    - VM 符合[計算](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)、[儲存體](azure-to-azure-support-matrix.md#replicated-machines---storage)和[網路](azure-to-azure-support-matrix.md#replicated-machines---networking)需求。

## <a name="run-a-failover"></a>執行容錯移轉


1. 在 VM [概觀] 頁面上，選取 [容錯移轉]。

    ![複寫項目的容錯移轉按鈕](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. 在 [容錯移轉] 中，選擇復原點。 目標區域中的 Azure VM 會使用此復原點的資料來建立。
  
   - **最近處理**：使用 Site Recovery 所處理的最新復原點。 隨即顯示時間戳記。 無須花費時間處理資料，因此可以提供低復原時間目標 (RTO)。
   -  **最新**：處理傳送到 Site Recovery 的所有資料，先為每個 VM 建立復原點後再進行容錯移轉。 提供最低的復原點目標 (RPO)，因為所有資料會在觸發容錯移轉時複寫到 Site Recovery。
   - **最新應用程式一致**：此選項會將 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - [自訂]：容錯移轉至特定復原點。 只有當您容錯移轉單一 VM，而且未使用復原方案時，才可以使用自訂。

    > [!NOTE]
    > 如果您在啟用複寫之後將磁碟新增至 VM，複寫點將會顯示可供復原的磁碟。 例如，在您新增第二個磁碟之前所建立的複寫點，會顯示為「1 個磁碟，共 2 個」。

4. 如果想在啟動容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]。 關機有助於確保不會遺失資料。 即使關機失敗，仍會繼續容錯移轉。 

    ![容錯移轉設定頁面](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. 若要開始進行容錯移轉，請選取 [確定]。
4. 在通知中監視容錯移轉。

    ![進度通知](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![成功通知](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. 容錯移轉之後，在目標區域中建立的 Azure VM 會出現在 [虛擬機器] 中。 請確定 VM 正在執行，且大小適當。 如果您想要對 VM 使用不同的復原點，請在 [基本資訊] 頁面上選取 [變更復原點]。
6. 如果您對容錯移轉後的 VM 感到滿意，請在 [概觀] 頁面上選取 [認可] 以完成容錯移轉。

    ![認可按鈕](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. 在 [認可]中，選取 [確定] 加以確認。 「認可」會刪除 Site Recovery 中的 VM 所有可用的復原點，且您將無法變更復原點。

8. 在通知中監視認可進度。

    ![認可進度通知](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![認可成功通知](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery 並不會在容錯移轉後清除來源 VM。 您必須手動執行此動作。


## <a name="reprotect-the-vm"></a>重新保護 VM

進行容錯移轉後，您可以重新保護次要區域中的 VM，使其可複寫回主要區域。 

1. 開始之前，請確定 VM 的 [狀態] 為 [容錯移轉已認可]。
2. 確認您可以存取主要區域，且您有權在其中建立 VM。
3. 在 VM [概觀] 頁面上，選取 [重新保護]。

   ![為 VM 啟用重新保護的按鈕。](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. 在 [重新保護] 中確認複寫方向 (次要到主要區域)，並檢閱主要區域的目標設定。 Site Recovery 會在重新保護作業期間建立標記為新的資源。

     ![重新保護設定頁面](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. 選取 [確定] 以開始重新保護程序。 程序會將初始資料傳送至目標位置，然後將 VM 的差異資訊複寫至目標。
7. 在通知中監視重新保護進度。 

    ![重新保護進度通知](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![重新保護成功通知](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已從主要區域容錯移轉至次要區域，並開始將 VM 複寫回主要區域。 現在，您可以從次要區域容錯回復至主要區域。

> [!div class="nextstepaction"]
> [容錯回復至主要區域](azure-to-azure-tutorial-failback.md)
