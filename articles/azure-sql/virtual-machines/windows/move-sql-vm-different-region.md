---
title: '將虛擬機器移至另一個區域 (Azure Site Recovery) '
description: 了解如何將 SQL Server 虛擬機器從某個區域移轉至 Azure 內的另一個區域。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: ae89091eb57eade39f8b7581fc5df7ad449e8590
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553551"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>使用 Azure Site Recovery 將 SQL Server VM 移至 Azure 中的另一個區域
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文會教導如何使用 Azure Site Recovery，將 SQL Server 虛擬機器 (VM) 從某個區域移轉至 Azure 內的另一個區域。 

將 SQL Server VM 移至不同區域時，需要執行下列動作：
1. [準備](#prepare-to-move)：確認來源 SQL Server VM 和目標區域都已充分準備好進行移動。 
1. [設定](#configure-azure-site-recovery-vault)：移動 SQL Server VM 時，其必須是 Azure Site Recovery 保存庫內的複寫物件。 您需要將 SQL Server VM 新增至 Azure Site Recovery 保存庫。 
1. [測試](#test-move-process)：移轉 SQL Server VM 需要將其從來源區域容錯移轉至複寫的目標區域。 若要確定移動程式會成功，您必須先測試 SQL Server VM 是否可以成功容錯移轉至目的地區域。 這有助於公開任何問題，並在執行實際移動時避免發生這些問題。 
1. [移動](#move-the-sql-server-vm)：一旦通過容錯移轉測試，且知道可安全地移轉 SQL Server VM，即可將 VM 移至目標區域。 
1. [清除](#clean-up-source-resources)：若要避免計費，請從保存庫中移除 SQL Server VM，以及移除在資源群組中保留的任何不必要資源。 

## <a name="verify-prerequisites"></a>驗證必要條件 

- 確認[支援](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)從來源區域移至目標區域。  
- 檢閱[案例架構和元件](../../../site-recovery/azure-to-azure-architecture.md)，以及[支援限制和需求](../../../site-recovery/azure-to-azure-support-matrix.md)。 
- 驗證帳戶權限。 如果您已建立免費的 Azure 帳戶，那您就是自己的訂用帳戶管理員。 如果您不是訂用帳戶管理員，請與系統管理員合作以指派您所需的權限。 若要啟用 VM 複寫且使用 Azure Site Recovery 複製資料，則必須具備： 
    - 建立 VM 的權限。 「虛擬機器參與者」內建角色具有這些權限，包含： 
        - 在所選取資源群組中建立 VM 的權限。 
        - 在所選取虛擬網路中建立 VM 的權限。 
        - 寫入所選取儲存體帳戶的權限。 
      - 管理 Azure Site Recovery 作業的權限。 「Site Recovery 參與者」角色具有在復原服務保存庫中管理 Site Recovery 作業所需的所有權限。  

## <a name="prepare-to-move"></a>準備移動
準備來源 SQL Server VM 和移動的目標區域。 

### <a name="prepare-the-source-sql-server-vm"></a>準備來源 SQL Server VM

- 確定想要移動的 SQL Server VM 中有所有最新根憑證。 如果其中沒有最新的根憑證，則會因為安全性條件約束而無法將資料複製到目標區域。 
- 針對 Windows VM，請安裝所有最新的 Windows 更新，讓所有受信任根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows Update 和憑證更新程式。 
- 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。 
- 請確定未使用驗證 Proxy 來控制所要移動 VM 的網路連線能力。 
- 如果嘗試移動的 VM 無法存取網際網路，或其使用防火牆 Proxy 控制輸出存取，請檢查需求。 
- 識別來源網路配置，以及您目前使用的所有資源。 這包括但不限於負載平衡器、網路安全性群組 (NSG)、公用 IP 等。 

### <a name="prepare-the-target-region"></a>準備目標區域

- 確認您的 Azure 訂用帳戶允許您在用於災害復原的目標區域中建立 VM。 請連絡支援人員啟用所需的配額。 
- 確定訂用帳戶有足夠的資源，可支援大小與來源 VM 相符的 VM。 如果使用 Site Recovery 將資料複製到目標，則 Site Recovery 將會為目標 VM 選擇相同或盡可能接近的大小。 
- 務必為來源網路配置中識別的每個元件建立目標資源。 這個步驟很重要，因為這可確定在將 VM 完全移轉至目標區域後，將具備與來源相同的所有功能和特性。 
    - 當您啟用來源 VM 的複寫時，Azure Site Recovery 會自動探索和建立虛擬網路。 您也可在啟用複寫的使用者流程中，預先建立網路並將其指派給 VM。 您必須在目標區域中手動建立任何其他資源。
- 若要根據來源 VM 設定建立最常用且符合需求的網路資源，請參閱下列文件： 
    - [網路安全性群組](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [負載平衡器](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [公用 IP 位址](../../../virtual-network/virtual-network-public-ip-address.md)
    - 如需任何其他網路元件，請參閱[網路文件](../../../virtual-network/virtual-networks-overview.md)。
- 如果想要先測試設定，再執行移至目標區域的最終移動作業，請在目標區域中手動建立非生產網路。 我們建議執行此步驟，因為這可以確保對生產網路產生的干擾最少。 

## <a name="configure-azure-site-recovery-vault"></a>設定 Azure Site Recovery 保存庫

下列步驟顯示如何使用 Azure Site Recovery 將資料複製到目標區域。 在來源區域以外的任何區域中建立復原服務保存庫。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 從瀏覽窗格的左上角選擇 [建立資源]。 
1. 選取 [IT 和管理工具]，然後選取 [備份和 Site Recovery]。 
1. 在 [基本] 索引標籤的 [專案詳細資料] 下方，於目標區域中建立新的資源群組，或選取目標區域中的現有資源群組。 
1. 在 [執行個體詳細資料] 中，指定保存庫的名稱，然後從下拉式清單中選取目標 **區域** 。 
1. 按一下 [檢閱 + 建立] 來建立復原服務保存庫。 
1. 從瀏覽窗格左上角選取 [所有服務]，然後在搜尋方塊中鍵入 `recovery services`。 
1. (選擇性) 選取 [復原服務保存庫] 旁的星號，將其新增至快速瀏覽列。 
1. 選取 [復原服務保存庫]，然後選取所建立的復原服務保存庫。 
1. 在 [概觀] 窗格上，選取 [複寫]。 

   ![設定複寫](./media/move-sql-vm-different-region/configure-replication.png)

1. 選取 [來源]，然後選取 [Azure] 作為來源。 為其他下拉式欄位選取適當的值，例如來源 VM 的位置。 只有位於 [來源位置] 區域的資源群組才會顯示在 [來源資源群組] 欄位中。 
1. 選取 [虛擬機器]，然後選擇想要移轉的虛擬機器。 選取 [確定] 以儲存 VM 選取項目。 
1. 選取 [設定]，然後從下拉式清單中選擇 **目標位置** 。 這應該是先前準備的資源群組。 
1. 在自訂複寫之後，請選取 [建立目標資源]，以在新的位置中建立資源。 
1. 資源建立完成後，請選取 [啟用複寫]，開始將 SQL Server VM 從來源複寫到目標區域。
1. 若要檢查複寫的狀態，請巡覽至復原保存庫，選取 [已複寫的項目] 並檢視 SQL Server VM 其 **狀態** 。 [受保護] 的狀態表示複寫已完成。 

   ![確認複寫狀態](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>測試移動處理序
下列步驟顯示如何使用 Azure Site Recovery 來測試移動處理序。 

1. 巡覽至在 [Azure 入口網站](https://portal.azure.com)中的 **復原服務保存庫** ，然後選取 [已複寫的項目]。 
1. 選取想要移動的 SQL Server VM，確認 [複寫健康狀態] 顯示為 [狀況良好]，然後選取 [測試容錯移轉]。 

   ![測試 VM 的容錯移轉](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. 在 [測試容錯移轉] 頁面上，選取要用於容錯移轉的 **最新應用程式一致** 復原點，因為這是唯一能保證 SQL Server 資料一致性的快照集類型。 
1. 選取 [Azure 虛擬網路] 下的虛擬網路，然後選取 [確定] 以測試容錯移轉。 
   
   >[!IMPORTANT]
   > 我們建議使用個別 Azure VM 網路進行容錯移轉測試。 請勿使用啟用複寫時所設定，以及 VM 最終移動目標位置所在的生產網路。 

1. 若要監視進度，請巡覽至保存庫，選取 [監視] 下的 [Site Recovery 作業]，然後選取進行中的 **測試容錯移轉** 作業。

   ![監視容錯移轉測試進度](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. 測試完成之後，請巡覽至入口網站中的 **虛擬機器** ，並檢閱新建立的虛擬機器。 請確定 SQL Server VM 正在執行中、其大小適中且已連線到適當的網路。 
1. 刪除在測試過程中建立的 VM，因為在清除容錯移轉測試資源之前，[容錯移轉] 選項會呈現灰色。 巡覽回保存庫，選取 [已複寫的項目]，選取 [SQL Server VM]，然後選取 [清除測試容錯移轉]。 在 [附註] 區段中記錄並儲存與測試建立關聯的任何觀察，然後選取 [Testing is complete. Delete test failover virtual machine] \(測試完成。請刪除測試容錯移轉虛擬機器\) 旁的核取方塊。 選取 [確定] 以在測試之後清除資源。 

   ![在容錯移轉測試之後清除項目](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>移動 SQL Server VM 
下列步驟示範如何將 SQL Server VM 從來源區域移至目標區域。 

1. 巡覽至 **復原服務** 保存庫，選取 [已複寫的項目]，選取 VM，然後選取 [容錯移轉]。 

   ![起始容錯移轉](./media/move-sql-vm-different-region/initiate-failover.png)

1. 選取 [復原點] 下的 **最新應用程式一致** 復原點。 
1. 選取 [ **先將機器關機再開始容錯移轉** ] 旁的核取方塊。 Site Recovery 會先嘗試關閉來源 VM，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 
1. 選取 [確定] 開始進行容錯移轉。
1. 從在上一節中監視容錯移轉測試時所檢視的相同 [Site Recovery 作業] 頁面中，您可監視容錯移轉處理序。 
1. 作業完成後，請確認 SQL Server VM 如預期般出現在目標區域中。 
1. 巡覽回保存庫，選取 [已複寫的項目]，選取 [SQL Server VM]，然後選取 [認可]，vio 完成移至目標區域的移動處理序。 請等候認可作業完成。 
1. 使用 SQL IaaS 代理程式延伸模組註冊 SQL Server VM，以在與擴充功能相關聯的 Azure 入口網站和功能中啟用 **SQL 虛擬機器** 管理能力。 如需詳細資訊，請參閱 [使用 SQL IaaS 代理程式擴充功能註冊 SQL SERVER VM](sql-agent-extension-manually-register-single-vm.md)。 

  > [!WARNING]
  > 只有應用程式一致的快照集才會保證 SQL Server 資料一致性。 當損毀復原快照集無法保證 SQL Server 資料一致性時， **最新處理** 快照集即無法用於 SQL Server 容錯移轉。 

## <a name="clean-up-source-resources"></a>清除來源資源
若要避免計費，請從保存庫移除 SQL Server VM，並刪除任何不必要的相關聯資源。 

1. 巡覽回 **Site Recovery** 保存庫，選取 [已複寫的項目]，然後選取 SQL Server VM。 
1. 選取 [停用複寫]。 選取停用保護的原因，然後選取 [確定] 停用複寫。 

   >[!IMPORTANT]
   > 請務必執行此步驟，以避免產生 Azure Site Recovery 複寫費用。 

1. 如果您不打算重複使用來源區域中的任何資源，請刪除所有相關的網路資源，以及對應的儲存體帳戶。 


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定價指引](pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](doc-changes-updates-release-notes.md)