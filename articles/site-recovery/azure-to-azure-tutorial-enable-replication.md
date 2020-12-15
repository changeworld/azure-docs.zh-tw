---
title: 使用 Azure Site Recovery 設定 Azure VM 災害復原的教學課程
description: 在本教學課程中，使用 Site Recovery 服務設定從 Azure VM 到另一個 Azure 區域的災害復原。
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 6d07082b4a9c18461d5cc74de8844be803da7168
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922487"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>教學課程：設定適用於 Azure VM 的災害復原

本教學課程說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 為 Azure VM 設定災害復原。 在本文中，您將學會如何：

> [!div class="checklist"]
> * 確認 Azure 設定和權限
> * 準備您要複寫的 VM
> * 建立復原服務保存庫
> * 啟用 VM 複寫

當您為 VM 啟用複寫已設定災害復原時，將會在 VM 上安裝 Site Recovery 行動服務擴充功能，並向 Azure Site Recovery 註冊該擴充功能。 在複寫期間，VM 磁碟寫入會傳送至來源區域中的快取儲存體帳戶。 資料會從該處傳送到目的地區域，並從資料產生復原點。 當您在災害復原期間將 VM 容錯移轉時，會使用復原點來還原目標區域中的 VM。

> [!NOTE]
> 教學課程會就最簡單的預設設定提供指示。 如果您想要使用自訂設定來設定 Azure VM 災害復原，請參閱[這篇文章](azure-to-azure-how-to-enable-replication.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前，請先：

- [檢閱支援的區域](azure-to-azure-support-matrix.md#region-support)。 您可以為相同地理位置中任何兩個區域之間的 Azure VM 設定災害復原。
- 您需要一或多個 Azure VM。 確認支援 [Windows](azure-to-azure-support-matrix.md#windows) 或 [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) VM。
- 檢閱 VM [計算](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)、[儲存體](azure-to-azure-support-matrix.md#replicated-machines---storage)和[網路](azure-to-azure-support-matrix.md#replicated-machines---networking)需求。
- 本教學課程假設 VM 不會加密。 如果您想要為加密的 VM 設定災害復原，請[遵循這篇文章](azure-to-azure-how-to-enable-replication-ade-vms.md)。

## <a name="check-azure-settings"></a>檢查 Azure 設定

檢查權限以及目標區域中的設定。

### <a name="check-permissions"></a>請檢查權限

您的 Azure 帳戶必須具有建立復原服務保存庫的權限，以及在目標區域中建立 VM 的權限。

- 如果您僅建立了免費 Azure 訂用帳戶，且您就是帳戶管理員，則無須採取進一步的動作。
- 如果您不是管理員，請與管理員合作以取得您所需的權限。
    - **建立保存庫**：訂用帳戶的管理員或擁有者權限。 
    - **管理保存庫中的 Site Recovery 作業**：*Site Recovery 參與者* 內建 Azure 角色。
    - **在目標區域中建立 Azure VM**：內建 *虛擬機器參與者* 角色，或下列項目的權限：
        - 在所選的虛擬網路中建立 VM。
        - 寫入至 Azure 儲存體帳戶。
        - 寫入至 Azure 受控磁碟。

### <a name="verify-target-settings"></a>確認目標設定

在災害復原期間，當您從來源區域進行容錯移轉時，將會在目標區域中建立 VM。 

請確認您的訂用帳戶在目標區域中有足夠的資源。 您必須能夠建立大小與來源區域中的 VM 相符的 VM。 當您設定災害復原時，Site Recovery 會為目標 VM 挑選相同的大小 (或最接近的大小)。


## <a name="prepare-vms"></a>準備 VM

請確定 VM 具有輸出連線能力，以及最新的根憑證。 


### <a name="set-up-vm-connectivity"></a>設定 VM 連線能力

您要複寫的 VM 需要輸出網路連線能力。

> [!NOTE]
> Site Recovery 不支援使用驗證 Proxy 來控制網路連線能力。

#### <a name="outbound-connectivity-for-urls"></a>URL 的輸出連線能力

如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 URL：

| **名稱**                  | **商業**                               | **政府**                                 | **說明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 儲存體                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | 允許將資料從 VM 寫入來源區域的快取儲存體帳戶中。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | 提供 Site Recovery 服務 URL 的授權和驗證。 |
| 複寫               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | 允許 VM 與 Site Recovery 服務進行通訊。 |
| 服務匯流排               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | 允許 VM 寫入 Site Recovery 監視和診斷資料。 |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 位址範圍的輸出連線能力

如果您使用網路安全性群組 (NSG) 來控制連線，請建立以服務標籤為基礎的 NSG 規則，以允許這些[服務標籤](../virtual-network/service-tags-overview.md#available-service-tags) (IP 位址群組) 透過 HTTPS 輸出至連接埠 443：

**Tag** | **允許** 
--- | ---
Storage tag  |允許將資料從 VM 寫入快取儲存體帳戶中。   
Azure AD 標籤 | 允許存取對應至 Azure AD 的所有 IP 位址。   
EventsHub 標籤 | 允許存取 Site Recovery 監視。  
AzureSiteRecovery 標籤 | 任何區域中的 Site Recovery 服務存取權。   
GuestAndHybridManagement 標籤 | 如果要自動升級在已啟用複寫之 VM 上執行的 Site Recovery 行動代理程式，請使用此標籤。

[深入了解](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)必要的標籤和標記範例。

### <a name="verify-vm-certificates"></a>驗證 VM 憑證

確認 VM 具有最新的根憑證。 如果沒有的話，VM 會因安全性限制而無法註冊至 Site Recovery。

- **Windows VM**：在 VM 上安裝所有最新的 Windows 更新，讓機器上具有所有受信任的根憑證。 在中斷連線的環境中，請依照 Windows Update 和憑證更新的標準程序操作。
- **Linux VM**：請依照 Linux 散發者提供的指引，取得最新受信任的根憑證及憑證撤銷清單 (CRL)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

在任何區域 (您要從中複寫 VM 的來源區域除外) 中建立復原服務保存庫。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在搜尋方塊中，輸入 *recovery*。 在 [服務] 底下，選取 [復原服務保存庫]。

    ![搜尋復原服務保存庫](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. 在 [復原服務保存庫] 中，選取 [新增]。
4. 在 [建立復原服務保存庫] > [基本資料] 中，選取要在其中建立保存庫的訂用帳戶。
5. 在 [資源群組] 中，為保存庫選取現有的資源群組，或建立新的資源群組。
6. 在 [保存庫名稱] 中，指定保存庫的易記名稱。
7. 在 [區域] 中，選取要在其中放置保存庫的 Azure 區域。 [檢查支援的區域](https://azure.microsoft.com/pricing/details/site-recovery/)。
8. 選取 [檢閱 + 建立]。

   ![用來建立新保存庫之頁面上的保存庫設定](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. 在 [檢閱 + 建立] 中，選取 [建立]。

10. 保存庫部署隨即開始。 在通知中追蹤進度。
11. 部署保存庫之後，請選取 [釘選到儀表板] 加以儲存，以便參考。 選取 [前往資源] 以開啟新的保存庫。 
    
    ![在部署後開啟保存庫，以及釘選到儀表板的按鈕](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>啟用 Site Recovery

在保存庫設定中，選取 [啟用 Site Recovery]。

![在保存庫中啟用 Site Recovery 的選取項目](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>啟用複寫

選取來源設定，並啟用 VM 複寫。 

### <a name="select-source-settings"></a>選取來源設定

1. 在 [保存庫 > Site Recovery] 頁面的 [Azure 虛擬機器] 底下，選取 [啟用複寫]。

    ![為 Azure VM 啟用複寫的選取項目](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. 在 [來源]> [來源位置] 中，選取 VM 目前執行所在的來源 Azure 區域。
3. 在 [Azure 虛擬機器部署模型] 中，保留預設的 [Resource Manager] 設定。
4. 在 [來源訂用帳戶] 中，選取 VM 執行所在的訂用帳戶。 您可以選取與保存庫位於相同 Azure Active Directory (AD) 租用戶中的任何訂用帳戶。
5. 在 [來源資源群組] 中，選取包含 VM 的資源群組。
6. 在 [可用性區域之間的災害復原] 中，保留預設的 [無] 設定。

     ![設定來源](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. 選取 [下一步]。

### <a name="select-the-vms"></a>選取 VM

Site Recovery 會擷取與選取的訂用帳戶/資源群組相關聯的 VM。

1. 在 [虛擬機器] 中，選取您要為災害復原啟用的 VM。

     ![用來選取要複寫之 VM 的頁面](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. 選取 [下一步]。

### <a name="review-replication-settings"></a>檢閱複寫設定

1. 在 [複寫設定] 中檢查設定。 Site Recovery 會建立目標區域的預設設定/原則。 基於本教學課程的目的，我們會使用預設設定。
2. 選取 [啟用複寫]  。

    ![自訂設定和啟用複寫的頁面](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. 在通知中追蹤複寫進度。

     ![在通知中追蹤進度](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![追蹤成功複寫通知](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. 您啟用的 VM 會出現在 [保存庫 > 複寫的項目] 頁面上。

    ![[複寫的項目] 頁面上的 VM](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已為 Azure VM 啟用災害復原。 接下來，請執行演練以檢查容錯移轉是否如預期運作。

> [!div class="nextstepaction"]
> [執行災害復原演練](azure-to-azure-tutorial-dr-drill.md)
