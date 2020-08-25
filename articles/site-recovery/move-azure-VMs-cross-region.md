---
title: 使用 Azure Site Recovery 將 Azure VM 移至另一個區域
description: 使用 Azure Site Recovery，將 Azure IaaS VM 從一個 Azure 區域移至另一個區域。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 463bd290c304d9436316c9e08778f37ce8bd0b55
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "86135431"
---
# <a name="move-azure-vms-to-another-azure-region"></a>將 Azure VM 移至另一個 Azure 區域

您可以將 Azure 基礎結構即服務 (IaaS) 虛擬機器從一個區域移至另一個區域，以改善可靠性、可用性、管理或控管。 本教學課程說明如何使用 Azure Site Recovery 將 VM 移至其區域。 您將學習如何：

> [!div class="checklist"]
> * 驗證必要條件
> * 準備來源 VM
> * 準備目標區域
> * 將資料複製到目標區域
> * 測試組態
> * 執行移動
> * 捨棄來源區域中的資源


> [!IMPORTANT]
> 本文說明何將 Azure VM「原封不動」  移到另一個區域。 如果您的目標是藉由將 VM 移至可用性區域來提高您的基礎結構可用性，請參閱[將 Azure VM 移至可用性區域](move-azure-vms-avset-azone.md)。

## <a name="prerequisites"></a>Prerequisites

- 確定您要「移出」  VM 的來源 Azure 區域中有 Azure VM。
- 確認您選擇的[來源區域與目標區域組合受到支援](./azure-to-azure-support-matrix.md#region-support)，然後小心地選擇目標區域。
- 請確定您了解[情節架構和元件](azure-to-azure-architecture.md)。
- 檢閱[支援限制和需求](azure-to-azure-support-matrix.md)。
- 驗證帳戶權限。 如果您剛建立免費的 Azure 帳戶，「您」  就是您的訂用帳戶管理員。 如果您不是管理員，請與管理員合作來取得您所需的權限：
  -  若要為 VM 啟用複寫，繼而使用 Site Recovery 將資料複製到目標，您必須具備在 Azure 資源中建立 VM 的權限。 具有這些權限的「虛擬機器參與者」內建角色。 具備權限，您就可以：
        - 在所選的資源群組中建立 VM。
        - 在所選的虛擬網路中建立 VM。
        - 寫入所選取的儲存體帳戶。

  - 您也需要管理 Site Recovery 作業的權限。 「Site Recovery 參與者」角色具有在 Azure 復原服務保存庫中管理 Site Recovery 作業所需的所有權限。

## <a name="prepare-the-source-vms"></a>準備來源 VM

1. 請檢查您打算移動的 Azure VM 是否有最新的根憑證。 如果沒有，您會因為安全性限制而無法將資料複製到目標區域。

    - 若為 Windows VM，請安裝最新的 Windows 更新，讓所有的受信任根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows Update 和憑證更新程序。
    - 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證和憑證撤銷清單。
2. 請確定您未使用驗證 Proxy 來控制打算移動 VM 的網路連線能力。
3. 如果您要移動的 VM 無法存取網際網路並使用防火牆 Proxy 控制輸出存取，請檢查[需求](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)。
4. 記載來源網路配置以及您目前使用的所有資源，包括 (但不限於) 要驗證的負載平衡器、網路安全性群組和公用 IP 位址。

## <a name="prepare-the-target-region"></a>準備目標區域

1. 在您的 Azure 訂用帳戶中，確認您可以在用於災害復原的目標區域中建立 VM。 如有需要，請連絡支援人員來啟用所需的配額。

2. 確定您的訂用帳戶具有足夠的資源可支援您的來源 VM。 如果您使用 Site Recovery 將資料複製到目標，它會為目標 VM 挑選相同的大小或最接近的可用大小。

3. 務必為來源網路配置中識別的每個元件，建立目標資源。 這可確保在將 VM 完全移轉至目標區域後，將具備與來源相同的所有功能和特性。

   當您啟用來源 VM 的複寫時，Azure Site Recovery 會自動探索和建立虛擬網路及儲存體帳戶。 您也可以在啟用複寫步驟中，預先建立這些資源並將其指派給 VM。 但您必須在目標區域中手動建立任何其他資源。 請參閱下列文件，以根據您的來源 VM 組態建立最常用的網路資源：

   - [網路安全性群組](../virtual-network/manage-network-security-group.md)
   - [負載平衡器](../load-balancer/index.yml)
   - [公用 IP](../virtual-network/virtual-network-public-ip-address.md)
    
   如需任何其他網路元件，請參閱 [Azure 網路文件](../index.yml?pivot=products&panel=network)。 

4. 若要在執行移動前測試組態，請在目標區域中手動[建立非生產網路](../virtual-network/quick-create-portal.md)。 測試安裝程式對生產環境的干擾最少，建議使用此方法。
    
## <a name="copy-data-to-the-target-region"></a>將資料複製到目標區域
下列步驟使用 Azure Site Recovery 將資料複製到目標區域。

### <a name="create-the-vault-in-any-region-except-the-source"></a>在來源以外的任何區域中建立保存庫

1. 登入 [Azure 入口網站](https://portal.azure.com) > [復原服務]  。
2. 選取 [建立資源]   > [管理工具]   > [備份和 Site Recovery]  。
3. 針對 [名稱]  ，指定易記名稱 [ContosoVMVault]  。 如果您有多個訂用帳戶，請選取適當的一個。
4. 建立資源群組 **ContosoRG**。
5. 指定 Azure 區域。 若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 針對復原服務保存庫，選取 [概觀]   > [ConsotoVMVault]   > [+複寫]  。
7. 針對 [來源]  ，選取 [Azure]  。
8. 針對 [來源位置]  ，選取 VM 目前執行所在的來源 Azure 區域。
9. 選取 Azure Resource Manager 部署模型。 然後，選取 [來源訂用帳戶]  和 [來源資源群組]  。
10. 選取 [確定]  來儲存設定。

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>為 Azure VM 啟用複寫並開始複製資料

Site Recovery 會擷取與訂用帳戶和資源群組建立關聯的 VM 清單。

1. 選取想要移動的 VM，然後選取 [確定]  。
2. 針對 [設定]  ，選取 [災害復原]  。
3. 針對 [設定災害復原]   >  [目標區域]  ，選取您要複寫至的目標區域。
4. 選擇使用預設目標資源，或使用您預先建立的資源。
5. 選取 [啟用複寫]  來啟動作業。

   ![啟用複寫](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>測試組態


1. 請移至保存庫。 在 [設定]   >  [複寫的項目]  中，選取要移至目標區域的虛擬機器。 然後，選取 [測試容錯移轉]  。
2. 在 [測試容錯移轉]  中，選取要用於容錯移轉的復原點：

   - **最近處理**：將 VM 容錯移轉到 Site Recovery 服務所處理的最新復原點。 隨即顯示時間戳記。 無須花費時間處理資料，所以這個選項會提供低復原時間目標 (RTO)。
   - **最新應用程式一致**：將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - [自訂]  ：選取任何復原點。

3. 選取您要移入 Azure VM 的目標 Azure 虛擬網路，以測試組態。

   > [!IMPORTANT]
   > 建議您使用個別的 Azure VM 網路來測試容錯移轉，而不要使用目標區域中的生產網路。

4. 若要開始測試移動，請選取 [確定]  。 若要追蹤進度，請選取 VM 來開啟其 [屬性]  。 或者，在保存庫中選取 [測試容錯移轉]  作業。 然後，選取 [設定]   > [作業]   >  [Site Recovery 作業]  。
5. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器]  中。 請確定 VM 正在執行中、大小適中，並已連線到適當的網路。
6. 若要刪除您為了測試所建立的 VM，請在複寫的項目上選取 [清除測試容錯移轉]  。 在 [記事]  中，記錄並儲存測試的任何相關觀察。

## <a name="perform-the-move-and-confirm"></a>執行移動並確認

1. 移至保存庫，在 [設定]   > [複寫的項目]  中選取虛擬機器，然後選取 [容錯移轉]  。
1. 針對 [容錯移轉]  ，選取 [最新]  。 
2. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)  。 Site Recovery 會嘗試先關閉來源 VM，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。
3. 作業完成時，請確認 VM 如預期出現在目標 Azure 區域中。
4. 在 [複寫的項目]  中，以滑鼠右鍵按一下 VM 並選取 [認可]  。 這會完成移動。 請等候認可作業完成。

## <a name="discard-the-resources-from-the-source-region"></a>捨棄來源區域中的資源

- 移至 VM 並選取 [停用複寫]  。 這會停止複製 VM 資料的程序。

  > [!IMPORTANT]
  > 請完成此步驟，以避免在移動之後產生 Site Recovery 複寫費用。

如果不打算重複使用任何來源資源，請依照這些步驟進行：

1. 刪除您在[準備來源 VM](#prepare-the-source-vms) 的步驟 4 所列來源區域中的所有相關網路資源。
2. 刪除來源區域中對應的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將 Azure VM 移到不同的 Azure 區域。 您現在可以設定這些 VM 的災害復原。

> [!div class="nextstepaction"]
> [在移轉之後設定災害復原](azure-to-azure-quickstart.md)
