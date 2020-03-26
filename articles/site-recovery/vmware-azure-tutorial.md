---
title: 使用 Azure Site Recovery 設定以 Azure 為目標的 VMware VM 災害復原
description: 了解如何使用 Azure Site Recovery 為內部部署 VMware VM 設定災害復原至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 37fdd42adf66ebcb11b357ece6ea63384630d9f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222635"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>為內部部署 VMware VM 設定災害復原至 Azure

本文說明如何針對內部部署 VMware VM 啟用複寫功能，以便使用 [Azure Site Recovery](site-recovery-overview.md) 服務進行 Azure 的災害復原。

這是一系列中的第三個教學課程，說明如何為內部部署 VMware VM 設定 Azure 的災害復原。 在上一個教學課程中，我們[準備了內部部署 VMware 環境](vmware-azure-tutorial-prepare-on-premises.md)以便進行 Azure 的災害復原。


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定來源複寫設定，以及內部部署 Site Recovery 組態伺服器。
> * 設定複寫目標設定。
> * 建立複寫原則。
> * 啟用適用於 VMware VM 的複寫。

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 可能的話，會使用預設選項，而不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱 Site Recovery 目錄的「操作說明」區段中的文章。

## <a name="before-you-start"></a>開始之前

完成先前的教學課程：
1. 確保您已[設定 Azure](tutorial-prepare-azure.md)，以便將內部部署 VMware 災害復原至 Azure。
2. 遵循[這些步驟](vmware-azure-tutorial-prepare-on-premises.md)來準備內部部署 VMware 部署，以進行 Azure 的災害復原。
3. 本教學課程將示範如何複寫單一 VM。 如果您要部署多個 VMware VM，則應該使用[部署規劃工具](https://aka.ms/asr-deployment-planner)。 [深入了解](site-recovery-deployment-planner.md) 此工具。
4. 本教學課程使用一些您可能想要以不同方式操作的選項：
    - 本教學課程會使用 OVA 範本來建立組態伺服器 VMware VM。 如果您因為某個理由而無法這麼做，請遵循[這些指示](physical-manage-configuration-server.md)，手動進行設定伺服器的設定。
    - 在本教學課程中，Site Recovery 會自動下載 MySQL 並安裝到組態伺服器。 如果您願意，您可以改為手動設定它。 [深入了解](vmware-azure-deploy-configuration-server.md#configure-settings)。




## <a name="select-a-protection-goal"></a>選取保護目標

1. 在 [復原服務保存庫]  中，選取保存庫名稱。 在此案例中，我們會使用 **ContosoVMVault**。
2. 在 [使用者入門]  中，選取 Site Recovery。 然後選取 [準備基礎結構]  。
3. 在 [保護目標]   > [您的電腦位於何處]  中，選取 [內部部署]  。
4. 在 [您要將電腦複寫到何處]  中，選取 [複製到 Azure]  。
5. 在 [您的電腦虛擬化了嗎]  中，選取 [是，使用 VMware vSphere Hyperviso]  。 然後選取 [確定]  。



## <a name="set-up-the-source-environment"></a>設定來源環境

在您的來源環境中，您需要單一、高可用性、內部部署的電腦來下列裝載 Site Recovery 元件：

- **設定伺服器**：組態伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- **處理序伺服器**：處理序伺服器可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將其傳送至 Azure 中的快取儲存體帳戶。 處理序伺服器也會在您要複寫的 VM 上安裝行動服務代理程式，並且在內部部署 VMware VM 上執行自動探索。
- **主要目標伺服器**：主要目標伺服器會在從 Azure 容錯回復期間，處理複寫資料。


這些元件會全部一起安裝在單一內部部署機器 (也稱為「組態伺服器」  ) 上。 根據預設，對於 VMware 災害復原，我們會將組態伺服器設定為高可用性 VMware VM。 若要這麼做，請下載備妥的開放式虛擬化應用程式 (OVA) 範本，然後將範本匯入 VMware 以建立 VM。 

- 在入口網站中可取得最新版的組態伺服器。 您也可以從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)直接下載。
- 如果您因為某個理由而無法使用 OVA 範本來設定 VM，請遵循[這些指示](physical-manage-configuration-server.md)，手動設定組態伺服器。
- 使用 OVF 範本所提供的授權是有效期 180 天的評估授權。 在 VM 上執行 Windows 必須使用必要的授權來啟動。 


### <a name="download-the-vm-template"></a>下載 VM 範本

1. 在保存庫中，移至 [準備基礎結構]   > [來源]  。
2. 在 [準備來源]  中，選取 [+設定伺服器]  。
3. 在 [新增伺服器]  中，檢查 [VMware 的組態伺服器]  是否出現在 [伺服器類型]  中。
4. 下載設定伺服器的 OVF 範本。



## <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本


1. 透過 VMWare vSphere 用戶端，登入 VMware vCenter 伺服器或 vSphere ESXi 主機。
2. 在 [檔案]  功能表上，選取 [部署 OVF 範本]  以啟動 [部署 OVF 範本精靈]  。 

     ![OVF 範本](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. 在 [選取來源]  上，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料]  上，選取 [下一步]  。
5. 在 [選取名稱和資料夾]  和 [選取組態]  上，接受預設設定。
6. 在 [選取儲存體]  上，於 [選取虛擬磁碟格式]  中選取 [Thick Provision Eager Zeroed]  ，以達到最佳效能。
7. 在精靈的其餘頁面上，接受所有的預設設定。
8. 在 [準備完成]  上，若要使用預設設定來設定 VM，請選取 [在部署後開啟電源]   > [完成]  。

   > [!TIP]
   > 如果您想要新增額外的 NIC，請清除 [在部署後開啟電源]   > [完成]  。 根據預設，此範本包含單一 NIC。 您可以在部署後新增其他 NIC。

## <a name="add-an-additional-adapter"></a>新增其他介面卡

如果您想要將其他 NIC 新增至設定伺服器，請在保存庫中註冊伺服器前新增。 註冊之後，便不支援新增其他介面卡。

1. 在 vSphere 用戶端詳細目錄中，以滑鼠右鍵按一下 VM 並選取 [編輯設定]  。
2. 在 [硬體]  中，選取 [新增]   > [乙太網路介面卡]  。 然後選取 [下一步]  。
3. 選取介面卡類型和網路。 
4. 若要在 VM 開啟時連線虛擬 NIC，請選取 [在電源開啟時連線]  。 選取 [下一步]   > [完成]  。 然後選取 [確定]  。


## <a name="register-the-configuration-server"></a>註冊組態伺服器 

設定好組態伺服器後，您可在保存庫中加以註冊。

1. 從 VMWare vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。
4. 您第一次登入時，Azure Site Recovery 設定工具會在數秒內啟動。
5. 輸入用來向 Site Recovery 註冊設定伺服器的名稱。 然後選取 [下一步]  。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入]  以登入您的 Azure 訂用帳戶。 認證必須能夠存取您要在其中註冊組態伺服器的保存庫。 請確定已將必要的[角色](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements)指派給這名使用者。
7. 此工具會執行一些設定工作，而後重新開機。
8. 再次登入機器。 組態伺服器管理精靈會在數秒內自動啟動。


### <a name="configure-settings-and-add-the-vmware-server"></a>進行設定與新增 VMware 伺服器

完成組態伺服器的設定和註冊。 在繼續之前，請確定您已符合所有[必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)，以便能成功設定伺服器。


1. 在設定伺服器管理精靈中，選取 [設定連線]  。 從下拉式清單中，先選取內建處理序伺服器用來在來源機器上進行行動服務探索及推入安裝的 NIC，然後選取設定伺服器用來與 Azure 連線的 NIC。 然後選取 [儲存]  。 一旦進行此設定之後便無法變更。
2. 在 [選取復原服務保存庫]  中，選取您的 Azure 訂用帳戶及相關的資源群組和保存庫。
3. 在 [安裝第三方軟體]  中，接受授權合約。 選取 [下載並安裝]  以安裝 MySQL Server。 如果您在路徑中放置 MySQL，則可略過此步驟。 深入[了解](vmware-azure-deploy-configuration-server.md#configure-settings)
4. 在 [驗證設備設定]  中，必要條件會在您繼續之前進行驗證。
5. 在 [設定 vCenter 伺服器/vSphere ESXi 伺服器]  中，輸入 vCenter 伺服器或 vSphere 主機 (您要複寫的 VM 位於其上) 的 FQDN 或 IP 位址。 輸入伺服器所接聽的連接埠。 輸入要用於保存庫中 VMware 伺服器的易記名稱。
6. 輸入可供設定伺服器用來連線至 VMware 伺服器的使用者認證。 請確定使用者名稱和密碼都正確無誤，且屬於所要保護虛擬機器的 Administrators 群組。 Site Recovery 會使用這些認證來自動探索可用於複寫的 VMware VM。 選取 [新增]  ，然後選取 [繼續]  。
7. 在 [設定虛擬機器認證]  中，輸入將用於在 VM 上自動安裝行動服務的使用者名稱和密碼 (已啟用複寫時)。
    - 若為 Windows 電腦，此帳戶需具備您要複寫之機器的本機系統管理員權限。
    - 若為 Linux，請提供根帳戶的詳細資料。
8. 選取 [完成設定]  以完成註冊。
9. 註冊完成之後，請開啟 Azure 入口網站，然後確認 [復原服務保存庫]   > [管理]   > [Site Recovery 基礎結構]   > [設定伺服器]  上已列出設定伺服器和 VMware 伺服器。


註冊組態伺服器之後，Site Recovery 會使用指定的設定連線至 VMware 伺服器並探索 VM。

> [!NOTE]
> 可能需要 15 分鐘以上，帳戶名稱才會出現在入口網站。 若要立即更新，請選取 [設定伺服器]   > 伺服器名稱 > [重新整理伺服器]  。

## <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 選取 [準備基礎結構]   > [目標]  。 選取您要使用的 Azure 訂用帳戶。 我們會使用 Resource Manager 模型。
2. Site Recovery 會檢查您具有一或多個虛擬網路。 當您在本教學課程系列的[第一個教學課程](tutorial-prepare-azure.md)中設定 Azure 元件時，您應擁有這些。

   ![目標索引標籤](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>建立複寫原則

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [復原服務保存庫]  。
2. 選取復原服務保存庫 (本教學課程中的 **ContosoVMVault**)。
3. 若要建立複寫原則，請選取 [Site Recovery 基礎結構]   > [複寫原則]   > [+複寫原則]  。
4. 在 [建立複寫原則]  中，輸入原則名稱。 我們會使用 **VMwareRepPolicy**。
5. 在 [RPO 閾值]  中，使用預設值 60 分鐘。 這個值可定義復原點的建立頻率。 連續複寫超過此限制時會產生警示。
6. 在 [復原點保留]  中，指定每個復原點的保留時間長度。 在本教學課程中，我們使用會 72 小時。 複寫的 VM 可以還原至保留期內的任何時間點。
7. 在 [應用程式一致快照集頻率]  中，指定建立應用程式一致快照集的頻率。 我們會使用預設值 60 分鐘。 選取 [確定]  以建立原則。

   ![建立複寫原則](./media/vmware-azure-tutorial/replication-policy.png)

- 此原則會自動與設定伺服器產生關聯。
- 依預設會自動建立容錯回復的比對原則。 例如，如果複寫原則是 **rep-policy**，容錯回復原則就會是 **rep-policy-failback**。 從 Azure 起始容錯回復時才會使用此原則。

## <a name="enable-replication"></a>啟用複寫

啟用 VM 複寫，如下所示︰

1. 選取 [複寫應用程式]   > [來源]  。
2. 在 [來源]  中，選取 [內部部署]  ，然後選取 [來源位置]  中的組態伺服器。
3. 在 [機器類型]  中，選取 [虛擬機器] 
4. 在 [vCenter/vSphere Hypervisor]  中，選取 vSphere 主機，或可管理該主機的 vCenter 伺服器。
5. 選取處理序伺服器 (依預設安裝在組態伺服器 VM 上)。 然後選取 [確定]  。 每個處理序伺服器的健康狀態都會根據建議限制和其他參數來指示。 選擇狀況良好的處理序伺服器。 無法選擇狀態為[嚴重](vmware-physical-azure-monitor-process-server.md#process-server-alerts)的處理序伺服器。 您可以對錯誤進行[疑難排解及解決問題](vmware-physical-azure-troubleshoot-process-server.md)，**或是**設定[相應放大的處理序伺服器](vmware-azure-set-up-process-server-scale.md)。
6. 在 [目標]  中，選取您想要在其中建立容錯移轉 VM 的訂用帳戶和資源群組。 我們會使用 Resource Manager 部署模型。 
7. 選取 Azure VM 在容錯移轉後所要連線的 Azure 網路和子網路。
8. 選取 [立即設定選取的機器]  ，將網路設定套用到您啟用複寫的所有 VM 上。 選取 [稍後設定]  以選取每部機器的 Azure 網路。
9. 在 [虛擬機器]   > [選取虛擬機器]  中，選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後選取 [確定]  。 如果您無法檢視/選取任何特定的虛擬機器，請[深入了解](https://aka.ms/doc-plugin-VM-not-showing)如何解決此問題。
10. 在 [屬性]   > [設定屬性]  中，選取處理序伺服器要用來在電腦上自動安裝行動服務的帳戶。
11. 在 [複寫設定]   >  [進行複寫設定]  中，確認已選取正確的複寫原則。
12. 選取 [啟用複寫]  。 Site Recovery 會在 VM 已啟用複寫時安裝行動服務。
13. 您可以在 [設定]   >  [作業]   >  [Site Recovery 作業]  中，追蹤 [啟用保護]  作業的進度。 在**完成保護**作業開始執行且復原點產生完成之後，機器便已準備好而可進行容錯移轉。
14. 可能需要 15 分鐘或更久的時間，變更才會生效並顯示在入口網站中。
15. 若要監視您新增的 VM，請在 [設定伺服器]   >  [上次連絡時間]  中查看上次探索 VM 的時間。 若要新增 VM 而不等候已排定的探索，請醒目提示設定伺服器 (但不要選取)，然後選取 [重新整理]  。

## <a name="next-steps"></a>後續步驟
啟用複寫之後，執行演練，確保一切會如預期般運作。
> [!div class="nextstepaction"]
> [執行災害復原演練](site-recovery-test-failover-to-azure.md)
