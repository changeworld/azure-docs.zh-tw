---
title: 使用 Azure Site Recovery 設定 Hyper-V (含 VMM) 災害復原
description: 了解如何使用 Site Recovery 將 System Center VMM 雲端中內部部署 Hyper-V VM 的災害復原設定至 Azure。
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067583"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>將 VMM 雲端中 Hyper-V VM 的災害復原設定至 Azure

本教學課程說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 來為 System Center Virtual Machine Manager (VMM) 所管理的內部部署 Hyper-V 虛擬機器 (VM) 啟用目的地為 Azure 的複寫。 如果您不是使用 VMM，請[遵循此教學課程](hyper-v-azure-tutorial.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 選取複寫來源和目標。
> * 設定來源複寫環境，包括內部部署 Site Recovery 元件和目標複寫環境。
> * 設定 VMM VM 網路與 Azure 虛擬網路之間的網路對應。
> * 建立複寫原則。
> * 啟用 VM 複寫。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 可能的話，會使用預設選項，而不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱 [Site Recovery 文件](/azure/site-recovery/)的**操作指南**一節中的文章。

## <a name="prerequisites"></a>Prerequisites

本教學課程假設您已完成下列教學課程：

1. [準備 Azure](tutorial-prepare-azure.md)
1. [準備內部部署 Hyper-V 伺服器](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>選取複寫目標

1. 在 Azure 入口網站中，移至 [復原服務保存庫]  ，然後選取[準備 Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault) 教學課程中所建立的 **ContosoVMVault** 保存庫。
1. 從 [使用者入門]  選取 [Site Recovery]   > [準備基礎結構]  ，然後進行下列設定：
    1. [保護目標]   > [您的電腦位於何處?]  ，選取 [內部部署]  。
    1. [您要將電腦複寫到何處?]  ，選取 [複製到 Azure]  。
    1. [您的電腦虛擬化了嗎?]  ，選取 [是，使用 Hyper-V]  。
    1. [您是否使用 System Center VMM 來管理 Hyper-V 主機?]  ，選取 [是]  。
1. 選取 [確定]  。

   ![複寫目標](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>確認部署規劃

1. 在 [部署規劃]  中，如果您正在規劃大型部署，請從頁面上的連結下載適用於 Hyper-V 的部署規劃工具。 [深入了解](hyper-v-deployment-planner-overview.md) Hyper-V 部署規劃。
1. 在本教學課程中，我們不需要部署規劃工具。 在 [完成部署規劃了嗎?]  中，選取 [稍後再進行]  ，然後選取 [確定]  。

## <a name="set-up-the-source-environment"></a>設定來源環境

當您設定來源環境時，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider，並在保存庫中註冊伺服器。 您會在每個 Hyper-V 主機上安裝 Azure 復原服務代理程式。

1. **準備基礎結構**。 選取 [來源]  。
1. **準備來源**。 選取 [+ VMM]  以新增 VMM 伺服器。 在 [新增伺服器]  中，檢查 [System Center VMM 伺服器]  是否出現在 [伺服器類型]  中。
1. 下載 Microsoft Azure Site Recovery 提供者的安裝程式。
1. 下載保存庫註冊金鑰。 執行 Provider 安裝程式時會需要此金鑰。 該金鑰在產生後會維持 5 天有效。
1. 下載 Microsoft Azure 復原服務代理程式的安裝程式。

   ![下載 Provider、註冊金鑰和代理程式](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>在 VMM 伺服器上將提供者解除安裝

1. 在 Azure Site Recovery Provider 安裝精靈 **Microsoft Update** 中。 選擇使用 Microsoft Update 來檢查 Provider 更新。
1. **安裝**。 接受 Provider 的預設安裝位置，然後選取 [安裝]  。
1. 安裝之後，在 [Microsoft Azure Site Recovery 註冊精靈] 中，選取 [保存庫設定]  、[瀏覽]  ，然後在 [金鑰檔案]  中選取您下載的保存庫金鑰檔案。
1. 指定 Azure Site Recovery 訂用帳戶和保存庫名稱 (**ContosoVMVault**)。 為 VMM 伺服器指定易記名稱，以便在保存庫中加以識別。
1. **Proxy 設定**。 選取 [不使用 Proxy 直接連線到 Azure Site Recovery]  。
1. 接受用來加密資料的憑證預設位置。 當您容錯移轉時，即會將加密的資料解密。
1. **同步處理雲端中繼資料**。 選取 [將雲端中繼資料同步到 Site Recovery 入口網站]  。 這個動作只需要在每個伺服器上進行一次。 接著，選取 [註冊]  。
1. 在保存庫中註冊伺服器之後，請選取 [完成]  。

註冊完成之後，Azure Site Recovery 會取出來自伺服器的中繼資料，而該 VMM 伺服器會顯示在 [Site Recovery 基礎結構]  中。

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>在 Hyper-V 主機上安裝復原服務代理程式

在每部包含您想要複寫之 VM 的 Hyper-V 主機上安裝代理程式。

在「Microsoft Azure 復原服務代理程式安裝精靈」中進行下列設定：

1. **必要條件檢查**。 選取 [下一步]  。 系統會自動安裝任何缺少的必要條件。
1. **安裝設定**。 接受安裝位置和快取位置。 快取磁碟機至少需要 5 GB 的儲存體。 我們建議磁碟機要有 600 GB 或更多的可用空間。 然後，選取 [安裝]  。
1. **安裝**。 在安裝完成後，選取 [關閉]  以完成精靈。

   ![安裝代理程式](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>設定目標環境

1. 選取 [準備基礎結構]   > [目標]  。
1. 選取容錯移轉之後，將在其中建立 Azure VM 的訂用帳戶和資源群組 (**ContosoRG**)。
1. 選取 [Resource Manager]  部署模型。

Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

## <a name="configure-network-mapping"></a>設定網路對應

1. [Site Recovery 基礎結構]   > [網路對應]   > [網路對應]  。 選取 [+網路對應]  圖示。
1. **新增網路對應**。 選取 [來源 System Center VMM]  伺服器。 在 [目標]  中選取 [Azure]。
1. 在容錯移轉後確認訂用帳戶和部署模型。
1. **來源網路**。 選取來源內部部署 VM 網路。
1. **目標網路**。 選取 Azure 網路來放置容錯移轉之後建立的複本 Azure VM。 然後選取 [確定]  。

   ![網路對應](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 選取 [準備基礎結構]   > [複寫設定]   > [+建立並產生關聯]  。
1. 在 [建立及關聯原則]  中指定原則名稱。 我們使用的是 **ContosoReplicationPolicy**。
1. 接受預設設定，然後選取 [確定]  ：
   - [複製頻率]  指出差異資料會在初始複寫之後每隔五分鐘複寫一次。
   - [復原點保留]  指出每個復原點會保留兩小時。
   - [應用程式一致快照頻率]  指出將會每小時建立一次包含應用程式一致快照集的復原點。
   - [初始複寫開始時間]  指出初始複寫將會立即開始。
   - [將儲存在 Azure 上的資料加密]  會設定為預設值 (**關閉**)，並指出不會加密 Azure 中的待用資料。
1. 原則建立後，請選取 [確定]  。 當您建立新的原則時，該原則會自動與 VMM 雲端產生關聯。

## <a name="enable-replication"></a>啟用複寫

1. **複寫應用程式**。 選取 [來源]  。
1. **來源**。 選取 [VMM 雲端]。 然後選取 [確定]  。
1. **目標**。 確認目標 (Azure)、保存庫訂用帳戶，然後選取 [Resource Manager]  模型。
1. 選取 **contosovmsacct1910171607** 儲存體帳戶和 **ContosoASRnet** Azure 網路。
1. [虛擬機器]   > [選取]  。 選取您要複寫的 VM。 然後選取 [確定]  。

   您可以在 [作業]   > [Site Recovery 作業]  中，追蹤 [啟用保護]  動作的進度。 在**完成保護**作業完成後，即完成初始複寫，且 VM 已可進行容錯移轉。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [執行災害復原演練](tutorial-dr-drill-azure.md)
