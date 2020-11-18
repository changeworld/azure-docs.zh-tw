---
title: 使用 Azure Site Recovery 執行 Azure VM 災害復原演練的教學課程
description: 在本教學課程中，使用 Site Recovery 執行從 Azure VM 到另一個區域的災害復原演練。
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395573"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>教學課程：執行 Azure VM 的災害復原演練

了解如何對使用 [Azure Site Recovery](site-recovery-overview.md) 進行複寫的 Azure VM，執行移轉至另一個 Azure 區域的災害復原演練。 在本文章中，您將：

> [!div class="checklist"]
> * 確認必要條件
> * 在演練前檢查 VM 設定
> * 執行測試容錯移轉
> * 在演練後進行清除


> [!NOTE]
> 本教學課程提供執行災害復原演練的基本步驟。 如果您想要使用完整基礎結構測試來執行演練，請了解 Azure VM [網路](azure-to-azure-about-networking.md)、[自動化](azure-to-azure-powershell.md)和[疑難排解](azure-to-azure-troubleshoot-errors.md)。

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須為一或多個 Azure VM 啟用災害復原。 若要這麼做，請[完成本系列中的第一個教學課程](azure-to-azure-tutorial-enable-replication.md)。

## <a name="verify-vm-settings"></a>確認 VM 設定

1. 在保存庫 > [複寫的項目] 中，選取 VM。

    ![在 VM 屬性中開啟 [災害復原] 頁面的選項](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. 在 [概觀] 頁面上，確認 VM 已受到保護且狀況良好。
3. 當您執行測試容錯移轉時，您會選取目標區域中的 Azure 虛擬網路。 在容錯移轉後建立的 Azure VM 會放在此網路中。 

    - 在本教學課程中，我們在執行測試容錯移轉時會選取現有的網路。
    - 建議您選擇非生產網路來進行演練，讓 IP 位址和網路元件在生產網路中仍可供使用。
   - 您也可以預先設定要用於測試容錯移轉的網路設定。 您可以為每個 NIC 指派的精細設定包括子網路、私人 IP 位址、公用 IP 位址、負載平衡器和網路安全性群組。 在此我們不會使用此方法，但您可以[參閱這篇文章](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations)以深入了解。


## <a name="run-a-test-failover"></a>執行測試容錯移轉


1. 在 [概觀] 頁面上，選取 [測試容錯移轉]。

    
    ![複寫項目的測試容錯移轉按鈕](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. 在 [測試容錯移轉] 中，選擇復原點。 目標區域中的 Azure VM 會使用此復原點的資料來建立。
  
   - **最近處理**：使用 Site Recovery 所處理的最新復原點。 隨即顯示時間戳記。 無須花費時間處理資料，因此可以提供低復原時間目標 (RTO)。
   -  **最新**：處理傳送到 Site Recovery 的所有資料，先為每個 VM 建立復原點後再進行容錯移轉。 提供最低的復原點目標 (RPO)，因為所有資料會在觸發容錯移轉時複寫到 Site Recovery。
   - **最新應用程式一致**：此選項會將 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - [自訂]：容錯移轉至特定復原點。 只有當您容錯移轉單一 VM，而且未使用復原方案時，才可以使用自訂。

3. 在 [Azure 虛擬網路] 中，選取在容錯移轉後建立的 Azure VM 要放置到的目標網路。 如果可能，請選取非生產網路，而不是您在啟用複寫時所建立的網路。

    ![測試容錯移轉設定頁面](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. 若要開始進行容錯移轉，請選取 [確定]。
5. 在通知中監視測試容錯移轉。

    ![進度通知](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![成功通知](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. 容錯移轉完成後，在目標區域中建立的 Azure VM 會出現在 Azure 入口網站的 [虛擬機器] 中。 請確定 VM 正在執行、大小適中，且已連線至您選取的網路。

## <a name="clean-up-resources"></a>清除資源

1. 在 [基本資訊] 頁面上，選取 [清除測試容錯移轉]。

    ![啟動清除程序的按鈕](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. 在 [測試容錯移轉清除] > [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。 
3. 選取 [測試完成]，以刪除在測試容錯移轉期間建立的 VM。

    ![具有清除選項的頁面](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. 在通知中監視清除進度。

    ![清除進度通知](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![清除成功通知](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已執行災害復原演練，以確認容錯移轉是否如預期運作。 接下來您可以嘗試進行完整的容錯移轉。

> [!div class="nextstepaction"]
> [執行產生容錯移轉](azure-to-azure-tutorial-failover-failback.md)
