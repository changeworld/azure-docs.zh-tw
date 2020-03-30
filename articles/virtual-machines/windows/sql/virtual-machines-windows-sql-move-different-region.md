---
title: 將虛擬機器移動到其他區域（Azure 網站恢復）
description: 瞭解如何將 SQL Server 虛擬機器從 Azure 中的一個區域遷移到另一個區域。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022300"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>使用 Azure 網站恢復服務將 SQL Server VM 移動到 Azure 中的另一個區域

本文介紹如何使用 Azure 網站恢復將 SQL Server 虛擬機器 （VM） 從 Azure 中的一個區域遷移到另一個區域。 

將 SQL Server VM 移動到其他區域需要執行以下操作：
1. [**準備**](#prepare-to-move)： 確認源 SQL Server VM 和目的地區域都為移動做好了充分的準備。 
1. [**配置**](#configure-azure-site-recovery-vault)：移動 SQL Server VM 需要它是 Azure 網站恢復保存庫中的複製物件。 您需要將 SQL Server VM 添加到 Azure 網站恢復保存庫。 
1. [**測試**](#test-move-process)：遷移 SQL Server VM 需要將其從源區域轉移到複製的目的地區域。 為了確保移動過程成功，您需要首先測試 SQL Server VM 能否成功容錯移轉到目的地區域。 這將有助於暴露任何問題，並在執行實際移動時避免這些問題。 
1. [**移動**](#move-the-sql-server-vm)：測試容錯移轉通過後，您知道可以安全地遷移 SQL Server VM，則可以執行 VM 移動到目的地區域。 
1. [**清理**](#clean-up-source-resources)：為了避免計費費用，請從保存庫中刪除 SQL Server VM 以及資源組中遺留下來的任何不必要的資源。 

## <a name="verify-prerequisites"></a>驗證必要條件 

- 確認[支援](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)從源區域移動到目的地區域。  
- 查看[方案體系結構和元件](../../../site-recovery/azure-to-azure-architecture.md)以及[支援限制和要求](../../../site-recovery/azure-to-azure-support-matrix.md)。 
- 驗證帳戶權限。 如果您已建立免費的 Azure 帳戶，那您就是自己的訂用帳戶管理員。 如果您不是訂用帳戶管理員，請與系統管理員合作以指派您所需的權限。 要啟用 VM 的複製並使用 Azure 網站恢復複製資料，您必須具有： 
    - 創建 VM 的許可權。 *虛擬機器參與者*內置角色具有以下許可權，其中包括： 
        - 在所選資源組中創建 VM 的許可權。 
        - 在選定的虛擬網路中創建 VM 的許可權。 
        - 寫入所選存儲帳戶的許可權。 
      - 管理 Azure Site Recovery 作業的權限。 *網站恢復參與者角色*具有在恢復服務保存庫中管理網站恢復操作所需的擁有權限。  

## <a name="prepare-to-move"></a>準備移動
準備源 SQL Server VM 和移動的目的地區域。 

### <a name="prepare-the-source-sql-server-vm"></a>準備源 SQL 伺服器 VM

- 確保所有最新的根憑證都在要移動的 SQL Server VM 上。 如果不存在最新的根憑證，則安全約束將阻止將資料複製到目的地區域。 
- 對於 Windows VM，請在 VM 上安裝所有最新的 Windows 更新，以便所有受信任的根憑證都在電腦上。 在斷開連接的環境中，請遵循組織的標準 Windows UPdate 和證書更新過程。 
- 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。 
- 確保沒有使用身份驗證代理來控制要移動的 VM 的網路連接。 
- 如果嘗試移動的 VM 無法存取網際網路，或使用防火牆 Proxy 控制輸出存取，請檢查需求。 
- 識別來源網路配置，以及您目前使用的所有資源。 這包括但不限於負載平衡器、網路安全性群組 (NSG)、公用 IP 等。 

### <a name="prepare-the-target-region"></a>準備目標區域

- 確認您的 Azure 訂用帳戶允許您在用於災害復原的目標區域中建立 VM。 請連絡支援人員啟用所需的配額。 
- 確保訂閱有足夠的資源來支援大小與您的源 VM 相匹配的 VM。 如果使用網站恢復將資料複製到目標，網站恢復將選擇相同大小或目標 VM 的最近可能的大小。 
- 務必為來源網路配置中識別的每個元件建立目標資源。 這個步驟很重要，因為這可確定在將 VM 完全移轉至目標區域後，將具備與來源相同的所有功能和特性。 
    - 當您啟用來源 VM 的複寫時，Azure Site Recovery 會自動探索和建立虛擬網路。 您還可以預先創建網路並將其分配給使用者流中的 VM 以啟用複製。 您需要手動創建目的地區域中的任何其他資源。
- 若要根據來源 VM 設定建立最常用且符合需求的網路資源，請參閱下列文件： 
    - [網路安全性群組](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [負載等化器](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [公共 IP 位址](../../../virtual-network/virtual-network-public-ip-address.md)
    - 有關任何其他網路元件，請參閱[網路文檔](../../../virtual-network/virtual-networks-overview.md)。
- 如果您想要先測試設定，再執行移動至目標區域的最終移動作業，請在目標區域中手動建立非生產網路 \(英文\)。 我們建議執行此步驟，因為這可以確保對生產網路產生的干擾最少。 

## <a name="configure-azure-site-recovery-vault"></a>配置 Azure 網站恢復保存庫

下列步驟顯示如何使用 Azure Site Recovery 將資料複製到目標區域。 在源區域以外的任何區域中創建恢復服務保存庫。 

1. 登錄到[Azure 門戶](https://portal.azure.com)。 
1. 選擇從功能窗格的左上角**創建資源**。 
1. 選擇**IT &管理工具**，然後選擇**備份和網站恢復**。 
1. 在 **"基本"** 選項卡下，在 **"專案詳細資訊**"下，在目的地區域中創建新資源組，或在目的地區域中選擇現有資源組。 
1. 在 **"實例詳細資訊**"下，指定保存庫的名稱，然後從下拉清單中選擇目標**區域**。 
1. 選擇 **"查看 + 創建**"以創建恢復服務保存庫。 
1. 從功能窗格的左上角和搜索框類型`recovery services`中選擇**所有服務**。 
1. （可選）選擇**恢復服務保存庫**旁邊的星形以將其添加到快速巡覽列。 
1. 選擇**恢復服務保存庫**，然後選擇您創建的恢復服務保存庫。 
1. 在 **"概述"** 窗格中，選擇 **"複製**"。 

   ![設定複寫](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. 選擇 **"源**"，然後選擇**Azure**作為源。 為其他下拉欄位選擇適當的值，例如源 VM 的位置。 "**源"位置**區域中僅顯示的資源**組在"源資源組"** 欄位中可見。 
1. 選擇**虛擬機器**，然後選擇要遷移的虛擬機器。 選擇 **"確定"** 以保存 VM 選擇。 
1. 選擇 **"設置**"，然後從下拉清單中選擇 **"目標"位置**。 這應該是您之前準備的資源組。 
1. 自訂複製後，選擇 **"創建目標資源**"以在新位置創建資源。 
1. 資源創建完成後，選擇**啟用複製**以開始將 SQL Server VM 從源區域複製到目的地區域。
1. 您可以通過導航到恢復保存庫、選擇 **"複製專案"** 並查看 SQL Server VM**的狀態**來檢查複製的狀態。 **"受保護"** 狀態表示複製已完成。 

   ![確認複寫狀態](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>測試移動過程
以下步驟演示如何使用 Azure 網站恢復來測試移動過程。 

1. 導航到[Azure 門戶](https://portal.azure.com)中的**恢復服務保存庫**，然後選擇 **"複製項**"。 
1. 選擇要移動的 SQL Server VM，驗證**複製運行狀況**是否顯示**為"正常"，** 然後選擇 **"測試容錯移轉**"。 

   ![測試 VM 容錯移轉](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. 在 **"測試容錯移轉"** 頁上，選擇用於容錯移轉**的最新應用一致性**復原點，因為這是保證 SQL Server 資料一致性的唯一快照類型。 
1. 選擇**Azure 虛擬網路**下的虛擬網路，然後選擇 **"確定"** 以測試容錯移轉。 
   
   >[!IMPORTANT]
   > 我們建議您使用單獨的 Azure VM 網路進行容錯移轉測試。 請勿使用啟用複寫時所設定，以及 VM 最終移動目標位置所在的生產網路。 

1. 要監視進度，請導航到保存庫，選擇 **"監視****"下的網站恢復作業**，然後選擇正在進行的 **"測試容錯移轉**作業"。

   ![監視容錯移轉測試進度](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. 測試完成後，導航到門戶中的**虛擬機器**並查看新創建的虛擬機器。 確保 SQL Server VM 正在運行，大小適當，並連接到相應的網路。 
1. 刪除作為測試的一部分創建的 VM，因為**容錯移轉**選項將灰顯，直到容錯移轉測試資源被清理。 導航回保存庫，選擇**複製的專案**，選擇 SQL Server VM，然後選擇**清理測試容錯移轉**。 在 **"備註"** 部分記錄並保存與測試關聯的任何觀察值，然後選擇 **"測試已完成"旁邊的核取方塊。刪除測試容錯移轉虛擬機器**。 選擇 **"確定"** 以在測試後清理資源。 

   ![容錯移轉測試後清理專案](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>移動 SQL 伺服器 VM 
以下步驟演示如何將 SQL Server VM 從源區域移動到目的地區域。 

1. 導航到**恢復服務**保存庫，選擇 **"複製專案**"，選擇 VM，然後選擇**容錯移轉**。 

   ![啟動容錯移轉](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. 在**復原點**下選擇**最新的應用一致的**復原點。 
1. **在開始容錯移轉之前，選擇關閉電腦**旁邊的核取方塊。 網站恢復將嘗試在觸發容錯移轉之前關閉源 VM。 即使關閉失敗，容錯移轉也會繼續。 
1. 選擇 **"確定"** 以啟動容錯移轉。
1. 您可以在上一節中監視容錯移轉測試時查看的同一**網站恢復作業**頁監視容錯移轉過程。 
1. 作業完成後，檢查 SQL Server VM 在目的地區域中顯示的按預期情況。 
1. 導航回保存庫，選擇 **"複製專案**"，選擇 SQL Server VM，然後選擇 **"提交"** 以完成移動到目的地區域的過程。 請等候認可作業完成。 
1. 將 SQL Server VM 註冊到 SQL VM 資來源提供者，以便在 Azure 門戶和與資來源提供者關聯的功能中啟用**SQL 虛擬機器**可管理性。 有關詳細資訊，請參閱將[SQL Server VM 註冊為 SQL VM 資來源提供者](virtual-machines-windows-sql-register-with-rp.md)。 

  > [!WARNING]
  > SQL Server 資料一致性僅通過應用一致的快照得到保證。 **最新的處理**快照不能用於 SQL Server 容錯移轉，因為崩潰恢復快照不能保證 SQL Server 資料的一致性。 

## <a name="clean-up-source-resources"></a>清理源資源
為避免計費費用，請從保存庫中刪除 SQL Server VM，並刪除任何不必要的關聯資源。 

1. 導航回**網站恢復**保存庫，選擇**複製項**，然後選擇 SQL Server VM。 
1. 選取 [停用複寫]****。 選擇禁用保護的原因，然後選擇 **"確定**"以禁用複製。 

   >[!IMPORTANT]
   > 執行此步驟非常重要，以避免為 Azure 網站恢復複製付費。 

1. 如果沒有計劃重用源區域中的任何資源，請刪除所有相關網路資源和相應的存儲帳戶。 


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 定價指南上的 SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


