---
title: 將虛擬機器移至另一個區域（Azure Site Recovery）
description: 瞭解如何將您的 SQL Server 虛擬機器從一個區域遷移至 Azure 中的另一個區域。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74022300"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>使用 Azure Site Recovery 服務將 SQL Server VM 移至 Azure 內的另一個區域

本文會教您如何使用 Azure Site Recovery，將您的 SQL Server 虛擬機器（VM）從一個區域遷移至 Azure 中的另一個區域。 

將 SQL Server 的 VM 移到不同的區域時，需要執行下列動作：
1. [**準備**](#prepare-to-move)：確認您的來源 SQL Server VM 和目的地區域都已充分準備好進行移動。 
1. 設定[**：移動**](#configure-azure-site-recovery-vault)您的 SQL Server VM 時，它會要求它是 Azure Site Recovery 保存庫內的複寫物件。 您需要將您的 SQL Server VM 新增至 Azure Site Recovery 保存庫。 
1. [**測試**](#test-move-process)：若要將 SQL SERVER 的 VM 遷移，必須將其從來源區域容錯移轉到已複寫的目的地區域。 若要確保移動程式成功，您必須先測試您的 SQL Server VM 是否可以成功容錯移轉至目的地區域。 這將有助於公開任何問題，並在執行實際移動時避免發生。 
1. [**移動**](#move-the-sql-server-vm)：一旦成功傳遞您的測試容錯移轉，而且您知道您可以安全地遷移 SQL Server vm，您可以執行將 vm 移至目的地區域。 
1. [**清除**](#clean-up-source-resources)：若要避免計費費用，請從保存庫移除 SQL SERVER 的 VM，以及在資源群組中留下的任何不必要的資源。 

## <a name="verify-prerequisites"></a>驗證必要條件 

- 確認[支援](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)從來源區域移至目的地區域。  
- 請參閱[案例架構和元件](../../../site-recovery/azure-to-azure-architecture.md)，以及[支援限制和需求](../../../site-recovery/azure-to-azure-support-matrix.md)。 
- 驗證帳戶權限。 如果您已建立免費的 Azure 帳戶，那您就是自己的訂用帳戶管理員。 如果您不是訂用帳戶管理員，請與系統管理員合作以指派您所需的權限。 若要使用 Azure Site Recovery 來啟用 VM 的複寫並複製資料，您必須具備： 
    - 建立 VM 的許可權。 「*虛擬機器參與者*」內建角色具有這些許可權，包括： 
        - 在選取的資源群組中建立 VM 的許可權。 
        - 在選取的虛擬網路中建立 VM 的許可權。 
        - 寫入所選取儲存體帳戶的許可權。 
      - 管理 Azure Site Recovery 作業的權限。 「 *Site Recovery 參與者*」角色具有在復原服務保存庫中管理 Site Recovery 作業所需的擁有權限。  

## <a name="prepare-to-move"></a>準備移動
準備來源 SQL Server VM 和移動的目的地區域。 

### <a name="prepare-the-source-sql-server-vm"></a>準備來源 SQL Server VM

- 請確定所有最新的根憑證都在您想要移動的 SQL Server VM 上。 如果沒有最新的根憑證，安全性條件約束將會防止資料複製到目的地區域。 
- 針對 Windows Vm，請在 VM 上安裝所有最新的 Windows 更新，讓所有受信任的根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows UPdate 和憑證更新程式。 
- 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。 
- 請確定您未使用驗證 proxy 來控制您想要移動之 Vm 的網路連線能力。 
- 如果嘗試移動的 VM 無法存取網際網路，或使用防火牆 Proxy 控制輸出存取，請檢查需求。 
- 識別來源網路配置，以及您目前使用的所有資源。 這包括但不限於負載平衡器、網路安全性群組 (NSG)、公用 IP 等。 

### <a name="prepare-the-target-region"></a>準備目標區域

- 確認您的 Azure 訂用帳戶允許您在用於災害復原的目標區域中建立 VM。 請連絡支援人員啟用所需的配額。 
- 請確定您的訂用帳戶有足夠的資源，可支援大小符合您來源 Vm 的 Vm。 如果您使用 Site Recovery 將資料複製到目標，Site Recovery 會針對目標 VM 選擇相同的大小或最接近的可能大小。 
- 務必為來源網路配置中識別的每個元件建立目標資源。 這個步驟很重要，因為這可確定在將 VM 完全移轉至目標區域後，將具備與來源相同的所有功能和特性。 
    - 當您啟用來源 VM 的複寫時，Azure Site Recovery 會自動探索和建立虛擬網路。 您也可以預先建立網路，並將它指派給使用者流程中的 VM，以啟用複寫。 您必須以手動方式在目的地區域中建立任何其他資源。
- 若要根據來源 VM 設定建立最常用且符合需求的網路資源，請參閱下列文件： 
    - [網路安全性群組](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [負載平衡器](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [公用 IP 位址](../../../virtual-network/virtual-network-public-ip-address.md)
    - 如需任何其他網路元件，請參閱[網路檔](../../../virtual-network/virtual-networks-overview.md)。
- 如果您想要先測試設定，再執行移動至目標區域的最終移動作業，請在目標區域中手動建立非生產網路 \(英文\)。 我們建議執行此步驟，因為這可以確保對生產網路產生的干擾最少。 

## <a name="configure-azure-site-recovery-vault"></a>設定 Azure Site Recovery 保存庫

下列步驟顯示如何使用 Azure Site Recovery 將資料複製到目標區域。 在來源區域以外的任何區域中建立復原服務保存庫。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 從流覽窗格的左上角，選擇 [**建立資源**]。 
1. 選取**它 & [管理工具**]，然後選取 [**備份] 和 [Site Recovery**]。 
1. 在 [**基本**] 索引標籤的 [**專案詳細資料**] 底下，在目的地區域中建立新的資源群組，或選取目的地區域中的現有資源群組。 
1. 在 [**實例詳細資料**] 底下，指定保存庫的名稱，然後從下拉式選單中選取您的目標**區域**。 
1. 選取 [**審查 + 建立**] 以建立您的復原服務保存庫。 
1. 選取流覽窗格左上角的 [**所有服務**]，然後在 [搜尋方塊] 中輸入`recovery services`。 
1. 也選取 [復原服務保存**庫**] 旁邊的星號，將其新增至您的快速導覽列。 
1. 選取 [復原**服務**保存庫]，然後選取您所建立的復原服務保存庫。 
1. 在 [**總覽**] 窗格中 **，選取 [** 複寫]。 

   ![設定複寫](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. 選取 [**來源**]，然後選取 [ **Azure** ] 做為來源。 為其他下拉式欄位選取適當的值，例如來源 Vm 的位置。 只有位於**來源位置**區域的資源群組，才會顯示在 [**來源資源群組**] 欄位中。 
1. 選取 [**虛擬機器**]，然後選擇您想要遷移的虛擬機器。 選取 **[確定]** 以儲存 VM 選取專案。 
1. 選取 [**設定**]，然後從下拉式選單選擇您的**目標位置**。 這應該是您稍早準備的資源群組。 
1. 自訂複寫之後，請選取 [**建立目標資源**]，在新的位置中建立資源。 
1. 資源建立完成後，請選取 [**啟用**複寫]，開始將 SQL Server VM 從來源複寫到目的地區域。
1. 您可以流覽至您的復原保存庫、選取 [複寫的**專案**]，然後查看 SQL Server VM 的**狀態**，以檢查複寫的狀態。 [**受保護**] 狀態表示複寫已完成。 

   ![確認複寫狀態](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>測試移動進程
下列步驟示範如何使用 Azure Site Recovery 來測試移動進程。 

1. 在[Azure 入口網站](https://portal.azure.com)中流覽至您的復原**服務保存庫**，然後選取 [複寫的**專案**]。 
1. 選取您想要移動的 SQL Server VM，確認複寫**健康**情況顯示為 [狀況**良好**]，然後選取 [**測試容錯移轉**]。 

   ![測試 VM 的容錯移轉](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. 在 [**測試容錯移轉**] 頁面上，選取要用於容錯移轉的**最新應用程式一致**復原點，因為這是唯一能保證 SQL Server 資料一致性的快照集類型。 
1. 選取 [ **Azure 虛擬網路**] 下的 [虛擬網路]，然後選取 **[確定]** 以測試容錯移轉。 
   
   >[!IMPORTANT]
   > 我們建議您針對容錯移轉測試使用個別的 Azure VM 網路。 請勿使用啟用複寫時所設定，以及 VM 最終移動目標位置所在的生產網路。 

1. 若要監視進度，請流覽至您的保存庫，選取 [**監視**] 下的 [ **Site Recovery 作業**]，然後選取進行中的**測試容錯移轉**作業。

   ![監視容錯移轉測試進度](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. 測試完成之後，流覽至入口網站中的 [**虛擬機器**]，並檢查新建立的虛擬機器。 請確定 SQL Server VM 正在執行、已適當調整大小，而且已連線到適當的網路。 
1. 刪除在測試過程中建立的 VM，因為**故障**轉移選項會呈現灰色，直到容錯移轉測試資源清除為止。 流覽回到保存庫、選取 [複寫的**專案**]、選取 [SQL SERVER] VM，然後選取 [**清除測試容錯移轉**]。 在 [**附注**] 區段中記錄並儲存與測試相關聯的任何觀察，然後選取 [測試完成] 旁的核取方塊 **。刪除測試容錯移轉虛擬機器**。 選取 [**確定]** 以在測試之後清除資源。 

   ![在容錯移轉測試之後清除專案](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>移動 SQL Server VM 
下列步驟示範如何將 SQL Server VM 從來源區域移至目的地區域。 

1. 流覽至復原**服務**保存庫、選取 [複寫的**專案**]、選取 VM，然後選取 [**容錯移轉**]。 

   ![起始容錯移轉](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. 在 [**復原點**] 下，選取**最新的應用程式一致**復原點。 
1. 選取 [**先將機器關機再開始容錯移轉**] 旁的核取方塊。 Site Recovery 會在觸發容錯移轉之前，嘗試關閉來源 VM。 即使關機失敗，仍會繼續容錯移轉。 
1. 選取 **[確定]** 以開始容錯移轉。
1. 您可以從您在上一節中監視容錯移轉測試時所看到的相同**Site Recovery 作業**] 頁面監視容錯移轉程式。 
1. 作業完成後，請檢查 SQL Server VM 是否如預期般出現在目的地區域中。 
1. 流覽回到保存庫、選取 [複寫的**專案**]、選取 [SQL Server VM]，然後選取 [**認可**]，完成將程式移至目的地區域的作業。 請等候認可作業完成。 
1. 向 SQL VM 資源提供者註冊您的 SQL Server VM，以在與資源提供者相關聯的 Azure 入口網站和功能中啟用**SQL 虛擬機器**管理能力。 如需詳細資訊，請參閱[使用 SQL vm 資源提供者註冊 SQL SERVER VM](virtual-machines-windows-sql-register-with-rp.md)。 

  > [!WARNING]
  > 只有應用程式一致的快照集，才會保證 SQL Server 的資料一致性。 **最新處理**的快照集無法用於 SQL Server 容錯移轉，因為損毀復原快照集無法保證 SQL Server 的資料一致性。 

## <a name="clean-up-source-resources"></a>清除來源資源
若要避免計費費用，請從保存庫移除 SQL Server 的 VM，並刪除任何不必要的相關聯資源。 

1. 流覽回到**Site Recovery**保存庫、選取 [複寫的**專案**]，然後選取 [SQL Server VM]。 
1. 選取 [停用複寫]****。 選取停用保護的原因，然後選取 **[確定]** 以停用複寫。 

   >[!IMPORTANT]
   > 請務必執行此步驟，以避免收取 Azure Site Recovery 複寫費用。 

1. 如果您不打算重複使用來源區域中的任何資源，請刪除所有相關的網路資源，以及對應的儲存體帳戶。 


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server Windows VM 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


