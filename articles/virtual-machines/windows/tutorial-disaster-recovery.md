---
title: 教學課程 - 使用 Azure Site Recovery 設定適用於 Windows VM 的災害復原
description: 了解如何使用 Azure Site Recovery 服務，在不同 Azure 區域啟用 Windows VM 災害復原。
author: rayne-wiselman
ms.service: virtual-machines-windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 53cc0b820bd2ffb9fc28b37f44bb71a7b9d3cd30
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379825"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>教學課程：啟用 Windows VM 災害復原

本教學課程說明如何為執行 Windows 的 Azure VM 設定災害復原至次要 Azure 區域。 在本文中，了解如何：

> [!div class="checklist"]
> * 啟用 Windows VM 災害復原
> * 執行災害復原演練
> * 在演練之後停止複寫 VM

當您為 VM 啟用複寫時，會在 VM 上安裝 Site Recovery 行動服務擴充功能，並向 [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) 註冊該擴充功能。 在複寫期間，VM 磁碟寫入會傳送至來源區域中的快取儲存體帳戶。 資料會從該處傳送到目的地區域，並從資料產生復原點。  當您在災害復原期間將 VM 容錯移轉時，會使用復原點來還原目的地區域中的 VM。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>必要條件

1. 檢查您的 Azure 訂用帳戶允許您在目標區域中建立 VM。 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶管理員且具備所需的權限。
2. 如果您不是訂用帳戶管理員，請與管理員合作以指派您：
    - 具有內建角色的虛擬機器參與者，或下列項目的權限：
        - 在所選的虛擬網路中建立 VM。
        - 寫入至 Azure 儲存體帳戶。
        - 寫入至 Azure 受控磁碟。
    - Site Recovery 參與者內建角色可管理保存庫中的 Site Recovery 作業。 
3. 建議您使用執行 Windows Server 2012 或更新版本的 Windows VM。 基於本教學課程的目的，不應將 VM 磁碟加密。
4. 如果 VM 輸出連線使用以 URL 為基礎的 Proxy，請確定可以存取這些 URL。 不支援使用已驗證的 Proxy。

    **Name** | **公用雲端** | **政府雲端** | **詳細資料**
    --- | --- | --- | ---
    儲存體 | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| 將資料從 VM 寫入來源區域的快取儲存體帳戶中。 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| 授權和驗證 Site Recovery 服務 URL。 
    複寫 | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM 與 Site Recovery 服務通訊。 
    服務匯流排 | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM 會寫入 Site Recovery 以監視和診斷資料。 

4. 如果您使用網路安全性群組 (NSG) 限制 VM 的網路流量，請建立 NSG 規則，以允許 VM 使用這些服務標籤 (IP 位址群組) 的輸出連線 (HTTPS 443)。 先試用測試 NSG 的規則。

    **Tag** | **允許** 
    --- | --- 
    Storage tag | 允許將資料從 VM 寫入快取儲存體帳戶中。
    Azure AD 標籤 | 允許存取對應至 Azure AD 的所有 IP 位址。
    EventsHub 標籤 | 允許存取 Site Recovery 監視。
    AzureSiteRecovery 標籤 | 任何區域中的 Site Recovery 服務存取權。
    GuestAndHybridManagement | 如果要自動升級在已啟用複寫之 VM 上執行的 Site Recovery 行動代理程式，請使用。
5.  在 Windows VM 上安裝最新的 Windows 更新，以確保 VM 具有最新的根憑證。
 
## <a name="enable-disaster-recovery"></a>啟用災害復原

1. 在 Azure 入口網站中，開啟 VM 屬性頁面。
2. 在 [作業] 中，選取 [災害復原]。
3. 在 **基本** > **目標區域** 中，選取要複寫 VM 的區域。 來源和目標區域必須位於相同的 Azure Active Directory 租用戶中。
4. 按一下 [檢閱 + 開始複寫]。

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="在 [VM 屬性災害復原] 頁面上啟用複寫。":::

5. 在 **審查 + 開始複寫** 中，確認以下設定：

    - **目標設定**。 根據預設，Site Recovery 會反映來源設定以建立目標資源。
    - **儲存體設定 - 快取儲存體帳戶**。 Recovery 會使用來源區域中的儲存體帳戶。 在將來源 VM 變更複寫到目標位置之前，會先在此帳戶中快取。
    - **儲存設定 - 複本磁碟**。 根據預設，Site Recovery 會在目標區域中建立與來源 VM 受控磁碟具有相同儲存體類型 (標準或進階) 的複本受控磁碟，以鏡像表示來源 VM 受控磁碟。
    - **複寫設定**。 顯示保存庫詳細資料，並指出 Site Recovery 所建立的復原點會保留 24 小時。
    - **擴充功能設定**。 指出 Site Recovery 會管理您複寫的 VM 上安裝之 Site Recovery 行動服務擴充功能的更新。 指出的 Azure 自動化帳戶會管理更新程序。

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="顯示目標和複寫設定摘要的頁面。":::

2. 選取 [開始複寫]。 部署隨即啟動，Site Recovery 開始建立目標資源。 您可以在通知中監視複寫進度。

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="複寫進度的通知。":::

## <a name="check-vm-status"></a>檢查 VM 狀態

複寫作業完成後，您可以檢查 VM 複寫狀態。

1. 開啟 VM 屬性頁面。
2. 在 [作業] 中，選取 [災害復原]。
3. 展開 **基本資訊** 區段，檢閱保存庫、複寫原則和目標設定的預設值。
4. 在 **健康情況和狀態** 中，取得 VM 複寫狀態的相關資訊、代理程式版本、容錯移轉就緒，以及最新的復原點。 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="VM 災害復原的基本資訊。":::

5. 在 **基礎結構檢視** 中，取得來源和目標 VM、受控磁碟和快取儲存體帳戶的視覺化概觀。

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="VM 災害復原的基礎結構視覺化對應。":::


## <a name="run-a-drill"></a>執行演練

執行演練以確保災害復原可如預期般運作。 執行測試容錯移轉時，系統會建立一份 VM 複本，而不會影響進行中的複寫或生產環境。 

1. 在 VM 災害復原頁面中，選取 [測試容錯移轉]。
2. 在 **測試容錯移轉** 中，保留復原點的預設 **最近處理的 (低 RPO)** 設定。

   此選項提供最低的復原點目標 (RPO)，且通常會最快速地啟動目的地 VM。 此選項會先處理已傳送到 Site Recovery 服務的所有資料，先為每部 VM 建立復原點後再進行容錯移轉。 在觸發容錯移轉時，此復原點會將所有資料複寫到 Site Recovery。

3. 選取在容錯移轉後 VM 所在的虛擬網路。 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="設定測試容錯移轉選項的頁面。":::

4. 測試容錯移轉程序隨即開始。 您可以在通知窗格中監視進度。

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="測試容錯移轉通知。"::: 
    
   測試容錯移轉完成後，VM 會在 **基本資訊** 頁面上顯示為「清除測試容錯移轉擱置中」狀態。 



## <a name="clean-up-resources"></a>清除資源

Site Recovery 會在演練之後自動清除 VM。

1. 若要開始自動清除，請選取 [清除測試容錯移轉]。

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="[ 在基本資訊] 頁面上開始清除。"::: 

2. 在 [測試容錯移轉清除] 中，輸入任何要記錄的容錯移轉備註，然後選取 [測試完成 **。** 刪除測試容錯移轉虛擬機器]。 然後選取 [確定]  。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="記錄備註和刪除測試 VM 的頁面。"::: 

7. 刪除程序隨即開始。 您可以在通知中監視進度。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="監視刪除測試 VM 的通知。"::: 

### <a name="stop-replicating-the-vm"></a>停止複寫 VM

完成災害復原演練後，建議您繼續嘗試完整的容錯移轉。 如果不想執行完整的容錯移轉，您可以停用複寫。 這會執行以下動作：

- 從已複寫電腦的 Site Recovery 清單中移除 VM。
- 停止 VM 的 Site Recovery 計費。
- 自動清除來源複寫設定。

停止複寫，如下所示︰

1. 在 VM災害復原頁面中，選取 [停用複寫]。
2. 在 **停用複寫** 中，選取停用複寫的原因。 然後選取 [確定]  。

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="停用複寫並提供原因的頁面。"::: 


系統不會自動移除在複寫期間安裝在 VM 上的 Site Recovery 擴充功能。 如果停用 VM 複寫，而且稍後也不會複寫，您可以手動移除 Site Recovery 擴充功能，如下所示： 

1. 移至 VM > **設定** > **擴充功能**。
2. 在 **擴充功能** 頁面中，選取 Linux 的每個 Microsoft.Azure.RecoveryServices 項目。
3. 在擴充功能的屬性頁面中，選取 [解除安裝]。

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="解除安裝 Site Recovery VM 擴充功能的頁面。":::



## <a name="next-steps"></a>後續步驟

您已在本教學課程中設定 Azure VM 的災害復原，並執行了災害復原演練。 現在，您可以執行 VM 的完整容錯移轉。

> [!div class="nextstepaction"]
> [將 VM 容錯移轉到另一個區域](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
