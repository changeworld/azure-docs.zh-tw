---
title: 使用 Azure Site Recovery 將 VM 移至具有可用性區域的 Azure 區域
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 1d771d1e13d1ffd92a18658d08bb948d97e55999
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209004"
---
# <a name="move-azure-vms-into-availability-zones"></a>將 Azure VM 移至可用性區域中
Azure 中的可用性區域有助於讓應用程式和資料不受資料中心故障的影響。 每個可用性區域由一或多個資料中心組成，配備了電力、冷卻系統及網路系統。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 某個地區內可用性區域的實體區隔可保護應用程式和資料不受資料中心故障影響。 搭配可用性區域後，Azure 提供虛擬機器 (VM) 運作時間達 99.99% 的服務等級協定 (SLA)。 可用性區域可在選定區域中受到支援，如[支援可用性區域的地區](https://docs.microsoft.com/azure/availability-zones/az-region)中所述。

如果您將 VM 以「單一執行個體」  的形式部署到特定區域中，並且想藉由將這些 VM 移至可用性區域來改善可用性，您可以使用 Azure Site Recovery 來達到此目的。 此動作可進一步細分為：

- 將單一執行個體 VM 移至目標區域中的可用性區域
- 將可用性設定組中的 VM 移至目標區域中的可用性區域

> [!IMPORTANT]
> 目前 Azure Site Recovery 支援在不同的區域間移動 VM，但不支援在一個區域內移動。

## <a name="check-prerequisites"></a>檢查必要條件

- 檢查目標區域是否[支援可用性區域](https://docs.microsoft.com/azure/availability-zones/az-region)。 確認您選擇的[來源/目標區域組合可受到支援](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。 在目標區域上做出明智的決策。
- 請確定您了解[情節架構和元件](azure-to-azure-architecture.md)。
- 檢閱[支援限制和需求](azure-to-azure-support-matrix.md)。
- 檢查帳戶權限。 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶管理員。 如果您不是訂用帳戶管理員，請與管理員合作以指派您所需的權限。 若要為 VM 啟用複寫，繼而使用 Azure Site Recovery 將資料複製到目標，您必須具備：

    1. 在 Azure 資源中建立 VM 的權限。 「虛擬機器參與者」  內建角色具有這些權限，包含：
        - 在所選取資源群組中建立 VM 的權限
        - 在所選取虛擬網路中建立 VM 的權限
        - 寫入所選取儲存體帳戶的權限

    2. 管理 Azure Site Recovery 工作的權限。 「Site Recovery 參與者」  角色具有在復原服務保存庫中管理 Site Recovery 動作所需的所有權限。

## <a name="prepare-the-source-vms"></a>準備來源 VM

1. 如果您想要使用 Site Recovery 將 VM 移至可用性區域，這些 VM 應使用受控磁碟。 您可以將使用非受控磁碟的現有 Windows VM 轉換成使用受控磁碟。 請遵循[將 Windows 虛擬機器從非受控磁碟轉換成受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)上的步驟。 請確定可用性設定組已設定為「受控」  。
2. 檢查您要移動的 Azure VM 上具有所有最新的根憑證。 如果沒有最新的根憑證，則會因為安全性條件約束而無法啟用將資料複製到目標區域的功能。

3. 若為 Windows VM，請安裝所有最新的 Windows 更新，讓所有的受信任根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows Update 和憑證更新程序。

4. 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。
5. 請確定您未使用驗證 Proxy 來控制所要移動 VM 的網路連線能力。

6. 如果嘗試移動的 VM 無法存取網際網路，並且使用防火牆 Proxy 控制輸出存取，請在[設定輸出網路連線能力](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)上確認需求。

7. 識別來源網路配置，以及您目前用於驗證的資源，包括負載平衡器、NSG 和公用 IP 等。

## <a name="prepare-the-target-region"></a>準備目標區域

1. 確認您的 Azure 訂用帳戶可讓您在用於災害復原的目標區域中建立 VM。 如有需要，請連絡支援人員來啟用所需的配額。

2. 確定您的訂用帳戶具有足夠的資源，可支援大小與來源 VM 相符的 VM。 如果您使用 Site Recovery 將資料複製到目標，該服務將會為目標 VM 挑選相同的大小或最接近的大小。

3. 為來源網路配置中識別的每個元件建立目標資源。 此動作可確保在完全移轉至目標區域後，您的 VM 具備您在來源中擁有的所有功能和特性。

    > [!NOTE]
    > 當您啟用來源 VM 的複寫時，Azure Site Recovery 會自動探索和建立虛擬網路及儲存體帳戶。 您也可以預先建立這些資源並指派給 VM，將其當作啟用複寫步驟的一部分。 但任何其他資源 (如下面內容所述) 都必須在目標區域中手動建立。

     下列文件會說明如何根據來源 VM 組態，建立您最常用的相關網路資源。

    - [網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [負載平衡器](https://docs.microsoft.com/azure/load-balancer)
    - [公用 IP](../virtual-network/virtual-network-public-ip-address.md)
    
   如需其他網路元件的資訊，請參閱網路[文件](https://docs.microsoft.com/azure/?pivot=products&panel=network)。

    > [!IMPORTANT]
    > 請務必在目標中建立區域備援負載平衡器。 您可以在 [Standard Load Balancer 和可用性區域](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)中參閱更多資訊。

4. 如果您想要先測試組態再完全移轉到目標區域，請在目標區域中手動[建立非生產網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。 我們建議使用此方法，因為這樣對生產網路產生的干擾最少。

## <a name="enable-replication"></a>啟用複寫
下列步驟將引導您使用 Azure Site Recovery 將資料複寫到目標區域，繼而將其移至可用性區域中。

> [!NOTE]
> 這些步驟適用於單一 VM。 您可以將同樣的方法延伸到多部 VM。 移至 [復原服務保存庫]，選取 [+ 複寫]  ，然後同時選取相關的 VM。

1. 在 Azure 入口網站中選取 [虛擬機器]  ，然後選取要移至可用性區域中的 VM。
2. 在 [作業]  中，選取 [災害復原]  。
3. 在 [設定災害復原]   > [目標區域]  中，選取要作為複寫目的地的目標區域。 請確定此區域[支援](https://docs.microsoft.com/azure/availability-zones/az-region)可用性區域。

    ![選取目標區域](media/azure-vms-to-zones/enable-rep-1.PNG)

4. 完成時，選取 下一步:**進階設定**。
5. 為目標訂用帳戶、目標 VM 資源群組和虛擬網路選擇適當的值。
6. 在 [可用性]  區段中，選擇您要在其中移入 VM 的可用性區域。 
   > [!NOTE]
   > 若未看到可用性設定組或可用性區域的選項，請確定您符合[必要條件](#prepare-the-source-vms)，並且已完成來源 VM 的[準備](#prepare-the-source-vms)。
  
    ![選擇可用性區域的選取項目](media/azure-vms-to-zones/enable-rep-2.PNG)

7. 選取 [啟用複寫]  。 此動作會開始一項作業來啟用 VM 的複寫。

## <a name="check-settings"></a>檢查設定

在複寫作業完成之後，您可以檢查複寫狀態、修改複寫設定，以及測試部署。

1. 在 VM 功能表中，選取 [災害復原]  。
2. 您可以檢查複寫健康情況、已建立的復原點，以及地圖上的來源和目標區域。

   ![複寫狀態](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>測試組態

1. 在虛擬機器功能表中，選取 [災害復原]  。
2. 選取 [測試容錯移轉]  圖示。
3. 在 [測試容錯移轉]  中，選取要用於容錯移轉的復原點：

   - **最近處理**：將 VM 容錯移轉到 Site Recovery 服務所處理的最新復原點。 隨即顯示時間戳記。 使用此選項時，無須花費時間處理資料，因此它會提供低復原時間目標 (RTO)。
   - **最新應用程式一致**：此選項會將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - [自訂]  ：選取任何復原點。

3. 選取您要移入 Azure VM 的測試目標 Azure 虛擬網路，以測試組態。 

    > [!IMPORTANT]
    > 建議您使用個別的 Azure VM 網路來測試失敗，而不要使用移入 VM 所用目標區域中的生產網路。

4. 若要開始測試移動，請選取 [確定]  。 若要追蹤進度，請選取 VM 來開啟其屬性。 或者，您也可以選取保存庫名稱中的 [測試容錯移轉]  作業 > [設定]   > [作業]   > [Site Recovery 作業]  。
5. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器]  中。 請確定 VM 正在執行中、大小適中，並已連線到適當的網路。
6. 如果您想要刪除測試移動過程中建立的 VM，請在複寫的項目上選取 [清除測試容錯移轉]  。 在 [記事]  中，記錄並儲存與測試相關聯的任何觀察。

## <a name="move-to-the-target-region-and-confirm"></a>移至目標區域，並加以確認

1.  在虛擬機器功能表中，選取 [災害復原]  。
2. 選取 [容錯移轉]  圖示。
3. 在 [容錯移轉]  中，選取 [最新]  。 
4. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)  。 Site Recovery 嘗試先關閉來源 VM，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。 
5. 作業完成後，請確認 VM 如預期出現在目標 Azure 區域中。
6. 在 [複寫的項目]  中，以滑鼠右鍵按一下 VM > [認可]  。 如此，移至目標區域的程序即告完成。 請等候認可作業完成。

## <a name="discard-the-resource-in-the-source-region"></a>捨棄來源區域中的資源

移至 VM。 選取 [停用複寫]  。 此動作會停止複製 VM 資料的程序。  

> [!IMPORTANT]
> 請執行上述步驟，以避免在移動之後產生 Site Recovery 複寫費用。 來源複寫設定會自動清除。 請注意，在複寫期間安裝的 Site Recovery 擴充功能不會移除，而必須以手動方式移除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您將 VM 移至可用性設定組或可用性區域，而提高了 Azure VM 的可用性。 您現在可以設定已移動 VM 的災害復原。

> [!div class="nextstepaction"]
> [在移轉之後設定災害復原](azure-to-azure-quickstart.md)


