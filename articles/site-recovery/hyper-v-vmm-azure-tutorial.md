---
title: 使用 Azure Site Recovery 設定 Hyper-v （含 VMM）嚴重損壞修復
description: 瞭解如何使用 Site Recovery，將 System Center VMM 雲端中內部部署 Hyper-v Vm 的嚴重損壞修復設定到 Azure。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0c570702e4c3899ef2847883e6fc8649e603a787
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281674"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>將 VMM 雲端中 Hyper-V VM 的災害復原設定至 Azure

本文說明如何使用[Azure Site Recovery](site-recovery-overview.md)服務，針對 SYSTEM CENTER VIRTUAL MACHINE MANAGER （VMM）所管理的內部部署 hyper-v vm 啟用複寫，以進行嚴重損壞修復至 Azure 的復原。 如果您未使用 VMM，請改為[遵循此教學](hyper-v-azure-tutorial.md)課程。

這是一系列中的第三個教學課程，說明如何為內部部署 VMware VM 設定 Azure 的災害復原。 在上一個教學課程中，我們已[備妥內部部署 hyper-v 環境](hyper-v-prepare-on-premises-tutorial.md)，以進行 Azure 的嚴重損壞修復。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 選取複寫來源和目標。
> * 設定來源複寫環境，包括內部部署 Site Recovery 元件和目標複寫環境。
> * 設定網路對應，以便在 VMM VM 網路與 Azure 虛擬網路之間進行對應。
> * 建立複寫原則。
> * 啟用 VM 複寫。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 可能的話，會使用預設選項，而不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱 **Site Recovery 文件**的[操作指南](https://docs.microsoft.com/azure/site-recovery)一節中的文章。



## <a name="before-you-begin"></a>開始之前

這是本系列的第三個教學課程。 其內容假設您已完成了先前教學課程中的工作：

1. [準備 Azure](tutorial-prepare-azure.md)
2. [準備內部部署 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>選取複寫目標

1. 在 Azure 入口網站中，移至 [復原服務保存庫]，然後選取保存庫。 我們已在上一個教學課程中備妥保存庫 **ContosoVMVault**。
2. 在 [使用者入門] 中選取 [Site Recovery]，然後選取 [準備基礎結構]。
3. 在 [保護目標] > [您的電腦位於何處?] 中，選取 [內部部署]。
4. 在 [您要將電腦複寫到何處?] 中，選取 [複製到 Azure]。
5. 在 [您的電腦虛擬化了嗎?] 中，選取 [是，使用 Hyper-V]。
6. 在 [**您是否使用 System CENTER VMM 來管理 hyper-v 主機？** ] 中，選取 **[是]** 。
7.  選取 [確定]。

    ![複寫目標](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>確認部署規劃

1. 在 [部署規劃] 中，如果您正在規劃大型部署，請從頁面上的連結下載適用於 Hyper-V 的部署規劃工具。 [深入了解](hyper-v-deployment-planner-overview.md) Hyper-V 部署規劃。
2. 在本教學課程中，我們不需要部署規劃工具。 在 [完成部署規劃了嗎?] 中，選取 [稍後再進行]，然後選取 [確定]。

## <a name="set-up-the-source-environment"></a>設定來源環境

當您設定來源環境時，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者，並在保存庫中註冊伺服器。 您會在每部 Hyper-v 主機上安裝 Azure 復原服務代理程式。

1. 在 [**準備基礎結構**] 中，選取 [**來源**]。
2. 在 [**準備來源**] 中，選取 [ **+ vmm** ] 以新增 vmm 伺服器。 在 [新增伺服器] 中，檢查 [System Center VMM 伺服器] 是否出現在 [伺服器類型] 中。
3. 下載 Microsoft Azure Site Recovery 提供者的安裝程式。
4. 下載保存庫註冊金鑰。 當您執行提供者安裝程式時，您需要此金鑰。 該金鑰在產生後會維持 5 天有效。
5. 下載 Microsoft Azure 復原服務代理程式的安裝程式。

    ![下載提供者、註冊金鑰和代理程式](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>在 VMM 伺服器上將提供者解除安裝

1. 在 Azure Site Recovery Provider 安裝精靈 > **Microsoft Update** 中，選擇使用 Microsoft Update 來檢查 Provider 更新。
2. 在 [**安裝**] 中，接受提供者的預設安裝位置，然後選取 [**安裝**]。
3. 安裝完成後，在 Microsoft Azure Site Recovery 註冊嚮導 > 保存**庫設定** 中，選取**流覽**，然後在 **金鑰**檔案 中，選取您下載的保存庫金鑰檔。
4. 指定 Azure Site Recovery 訂用帳戶和保存庫名稱 (**ContosoVMVault**)。 為 VMM 伺服器指定易記名稱，以便在保存庫中加以識別。
5. 在 [Proxy 設定] 中，選取 [不使用 Proxy 而直接連線到 Azure Site Recovery]。
6. 接受用來加密資料的憑證預設位置。 當您容錯移轉時，即會將加密的資料解密。
7. 在 [同步雲端中繼資料] 中，選取 [請將雲端中繼資料同步至 Site Recovery 入口網站]。 此動作只需要在每部伺服器上發生一次。 接著，選取 [註冊]。
8. 在保存庫中註冊伺服器之後，請選取 **[完成]** 。

註冊完成之後，Azure Site Recovery 會取出來自伺服器的中繼資料，而該 VMM 伺服器會顯示在 [Site Recovery 基礎結構] 中。

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>在 Hyper-v 主機上安裝復原服務代理程式

在每部包含您想要複寫之 Vm 的 Hyper-v 主機上安裝代理程式。

1. 在 [Microsoft Azure 復原服務 Agent 安裝程式嚮導] 中 >**必要條件檢查**，選取 **[下一步]** 。 任何遺漏的必要條件將會自動安裝。
2. 在 [**安裝設定**] 中，接受安裝位置和快取位置。 快取磁碟機至少需要 5 GB 的儲存體。 我們建議磁碟機要有 600 GB 或更多的可用空間。 然後，選取 [安裝]。
3. 在 [**安裝**] 中，當安裝完成時，選取 [**關閉**] 以完成嚮導。

    ![安裝代理程式](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>設定目標環境

1. 選取 [準備基礎結構] > [目標]。
2. 選取容錯移轉之後，將在其中建立 Azure VM 的訂用帳戶和資源群組 (**ContosoRG**)。
3. 選取**Resource Manager**部署模型。

Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

## <a name="configure-network-mapping"></a>設定網路對應

1. 在 [ **Site Recovery 基礎結構** > **網路**對應] > [**網路對應**] 中，選取 [ **+ 網路對應**] 圖示。
2. 在 [新增網路對應] 中，選取來源 VMM 伺服器。 選取 [Azure] 作為目標。
3. 在容錯移轉後確認訂用帳戶和部署模型。
4. 在 [來源網路] 中，選取來源內部部署 VM 網路。
5. 在 [目標網路] 中，選取 Azure 網路來放置容錯移轉之後建立的複本 Azure VM。 然後選取 [確定]。

    ![網路對應](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 選取 [準備基礎結構] > [複寫設定] > [+建立並產生關聯]。
2. 在 [建立及關聯原則]中指定原則名稱。 我們使用的是 **ContosoReplicationPolicy**。
3. 保留預設設定，然後選取 **[確定]** 。
    - **複製頻率**表示在初始複寫之後，差異資料每五分鐘會複寫一次。
    - [**復原點保留**] 表示每個復原點會保留兩個小時。
    - [應用程式一致快照頻率] 指出將會每小時建立一次包含應用程式一致快照集的復原點。
    - [初始複寫開始時間] 指出初始複寫將會立即開始。
    - 將**儲存在 azure 上的資料加密**會設為預設值（**Off**），並指出 Azure 中的待用資料並未加密。
4. 原則建立後，請選取 [確定]。 當您建立新的原則時，它會自動與 VMM 雲端產生關聯。

## <a name="enable-replication"></a>啟用複寫

1. 在 [複寫應用程式] 中，選取 [來源]。
2. 在 [來源] 中，選取 VMM 雲端。 然後選取 [確定]。
3. 在 [**目標**] 中，確認目標（Azure）、保存庫訂用帳戶，然後選取 [ **Resource Manager** ] 模型。
4. 選取**contosovmsacct1910171607**儲存體帳戶和**ContosoASRnet** Azure 網路。
5. 在 [虛擬機器] > [選取] 中，選取您要複寫的 VM。 然後選取 [確定]。

   您可以在 [作業] **[Site Recovery 作業]**  >  中，追蹤 [啟用保護] 動作的進度。 在**完成保護**作業完成後，即完成初始複寫，且 VM 已可進行容錯移轉。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [執行災害復原演練](tutorial-dr-drill-azure.md)
