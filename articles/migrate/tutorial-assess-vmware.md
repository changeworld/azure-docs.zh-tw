---
title: 使用 Azure Migrate 伺服器評估來評估 VMware VM
description: 說明如何使用 Azure Migrate 伺服器評量來評估內部部署 VMware VM 是否可移轉至 Azure。
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: abd8aafebe9cc02b8ee88cce88616389c82af83f
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514096"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>透過伺服器評量評估 VMware

本文說明如何使用 [Azure Migrate：伺服器評量](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具來評估內部部署 VMware 虛擬機器 (VM)。


本教學課程是一個系列中的第二篇，會示範如何評估 VMware VM 並將其遷移至 Azure。 在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 設定 Azure Migrate 專案。
> * 設定內部部署環境中執行的 Azure Migrate 設備來評估 VM。
> * 開始連續探索內部部署 VM。 設備會將探索到的 VM 組態和效能資料傳送至 Azure。
> * 將探索到的 VM 群組在一起，然後評估該 VM 群組。
> * 檢閱評估結果。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱操作說明文章。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>必要條件

- [完成](tutorial-prepare-vmware.md)本系列的第一個教學課程。 如果未完成，本教學課程中的指示便沒有作用。
- 您在第一個教學課程中應該已完成下列作業：
    - [準備 Azure](tutorial-prepare-vmware.md#prepare-azure) 以使用 Azure Migrate。
    - [準備VMware 以進行評量](tutorial-prepare-vmware.md#prepare-for-assessment)。 這包括檢查 VMware 設定、設定 Azure Migrate 可用來存取 vCenter Server 的帳戶。
    - [確認](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment)您需要哪些內容，才能部署 VMware 評量所需的 Azure Migrate 設備。

## <a name="set-up-an-azure-migrate-project"></a>設定 Azure Migrate 專案

設定新的 Azure Migrate 專案，如下所示：

1. 在 Azure 入口網站 > [所有服務] 中，搜尋 **Azure Migrate**。
2. 在 [服務] 下，選取 [Azure Migrate]。
3. 在 [概觀] 的 [探索、評估和遷移伺服器] 底下，選取 [評估和遷移伺服器]。

   ![用來評估和遷移伺服器的按鈕](./media/tutorial-assess-vmware/assess-migrate.png)

4. 在 [開始使用] 中，選取 [新增工具]。
5. 在 [Migrate 專案] 中選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有的話)。     
6. 在 [專案詳細資料] 中，指定專案名稱以及要在其中建立專案的地理位置。 請檢閱[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府雲端](migrate-support-matrix.md#supported-geographies-azure-government)支援的地理位置。

   ![專案名稱和區域的方塊](./media/tutorial-assess-vmware/migrate-project.png)

7. 選取 [下一步] 。
8. 在 [選取評量工具] 中，選取 **[Azure Migrate：伺服器評量]**  > [下一步]。

   ![伺服器評估工具的選取](./media/tutorial-assess-vmware/assessment-tool.png)

9. 在 [選取移轉工具] 中，選取 [暫時跳過新增移轉工具] > [下一步]。
10. 在 [檢閱 + 新增工具] 中檢閱設定，然後選取 [新增工具]。
11. 等候幾分鐘讓 Azure Migrate 專案完成部署。 您會進入專案頁面。 如果您沒有看到專案，則可以從 Azure Migrate 儀表板中的 [伺服器] 來存取。

## <a name="set-up-the-azure-migrate-appliance"></a>設定 Azure Migrate 設備

Azure Migrate：伺服器評量會使用輕量的 Azure Migrate 設備。 設備會執行 VM 探索，並將 VM 的中繼資料和效能資料傳送至 Azure Migrate。 您可以透過數種方式來設定設備。

- 使用下載的 OVA 範本，在 VMware VM 上進行設定。 **這是本教學課程使用的方法。**
- 使用 PowerShell 安裝程式指令碼在 VMware VM 或實體機器上進行設定。 如果您無法使用 OVA 範本設定 VM，或如果您是在 Azure Government 中，則應該使用[此方法](deploy-appliance-script.md)。

建立設備之後，您會檢查其是否可以連線到 Azure Migrate：伺服器評量、進行第一次設，以及向 Azure Migrate 專案註冊設備。


### <a name="generate-the-azure-migrate-project-key"></a>產生 Azure Migrate 專案金鑰

1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評估]** 中，選取 [探索]。
2. 在 [探索機器] > [機器是否已虛擬化?] 中，選取 [是，使用 VMware vSphere Hypervisor]。
3. 在 **1：產生 Azure Migrate 專案金鑰**中，為您要為探索之 VMware VM 設定的 Azure Migrate 設備命名。名稱應使用英數位元，且長度不超過 14 個字元。
1. 按一下 [產生金鑰] ，開始建立必要的 Azure 資源。 在建立資源期間，請勿關閉探索的電腦頁面。
1. 成功建立 Azure 資源之後，系統會產生 **Azure Migrate 專案金鑰**。
1. 複製金鑰，您在設定期間需要此金鑰才能完成設備的註冊。

### <a name="download-the-ova-template"></a>下載 OVA 範本
在 **2：下載 Azure Migrate 設備**中，選取 .OVA 檔案，然後按一下 [下載]。 


   ![探索電腦的選取項目](./media/tutorial-assess-vmware/servers-discover.png)


   ![產生金鑰的選取項目](./media/tutorial-assess-vmware/generate-key-vmware.png)


### <a name="verify-security"></a>確認安全性

請先確認 OVA 檔案是安全的，再進行部署：

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 檔案的雜湊：
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. 確認最新的設備版本與雜湊值：

    - 對於 Azure 公用雲端：
    
        **演算法** | **下載** | **SHA256**
        --- | --- | ---
        VMware (11.6 GB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - 對於 Azure Government：
    
        **演算法** | **下載** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140337) | 47179f47eba2842337bbe533c424dd1da56baccdcf68b1d87b71a5a4280108c2


### <a name="create-the-appliance-vm"></a>建立設備 VM

匯入所下載的檔案，並建立 VM：

1. 在 vSphere 用戶端主控台中，選取 [檔案] > [部署 OVF 範本]。

   ![用於部署 OVF 範本的功能表命令](./media/tutorial-assess-vmware/deploy-ovf.png)

2. 在 [部署 OVF 範本精靈] > [來源] 中，指定 OVA 檔案的位置。
3. 在 [名稱]  和 [位置] 中，指定 VM 的易記名稱。 選取將裝載 VM 的庫存物件。
4. 在 [主機/叢集] 中，指定 VM 的執行所在主機或叢集。
5. 在**儲存體**中，指定 VM 的儲存目的地。
6. 在 [磁碟格式] 中，指定磁碟類型和大小。
7. 在 [網路對應] 中，指定 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure Migrate 伺服器評估。
8. 檢閱並確認設定，然後選取 [完成]。

## <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確定設備 VM 可以連線至[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure URL。

### <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

> [!NOTE]
> 如果您使用 [PowerShell 指令碼](deploy-appliance-script.md)來設定設備 (而非使用下載的 OVA)，則此程序中的前兩個步驟將與之無關。

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 VM，然後選取 [開啟主控台]。
2. 提供設備的語言、時區和密碼。
3. 在任何可連線至 VM 的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址:44368**。

   或者，您也可以選取應用程式捷徑，從設備桌面開啟應用程式。
1. 接受**授權條款**，並閱讀第三方資訊。
1. 在 [Web 應用程式] > [設定必要條件] 中，執行下列動作：
   - **連線能力**：應用程式會確認 VM 是否能夠存取網際網路。 如果 VM 使用 Proxy：
     - 按一下 [設定 Proxy] 以指定 Proxy 位址 (格式為 http://ProxyIPAddress 或 http://ProxyFQDN) ) 和接聽連接埠。
     - 如果 Proxy 需要驗證，請指定認證。
     - 僅支援 HTTP Proxy。
     - 如果您已新增 Proxy 詳細資料，或已停用 Proxy 和/或驗證，請按一下 [儲存] 以再次觸發連線檢查。
   - **時間同步**：設備上的時間應該與網際網路時間同步，探索才能正常運作。
   - **安裝更新**：設備會確保已安裝最新的更新。 檢查完成之後，您可以按一下 [檢視設備服務] 查看設備上執行之元件的狀態和版本。
   - **安裝 VDDK**：設備會檢查是否已安裝 VMware vSphere 虛擬磁碟開發套件 (VDDK)。 如果未安裝，則會從 VMware 下載 VDDK 6.7，並將下載的 zip 內容解壓縮到設備上的指定位置、如**安裝指示**中提供的內容所述。

     在遷移至 Azure 期間，Azure Migrate 伺服器移轉會使用 VDDK 來複寫機器。 
1. 如有需要，可以在設備設定期間隨時**重新執行必要條件**，以檢查設備是否符合所有必要條件。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 貼上從入口網站複製的 **Azure Migrate 專案金鑰**。 如果沒有金鑰，請移至**伺服器評估 > 探索 > 管理現有的設備**，選取在金鑰產生時提供的設備名稱，並複製對應的金鑰。
1. 按一下 [登入]。 系統會在新的瀏覽器索引標籤中開啟 Azure 登入提示。如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
1. 在新的索引標籤上，使用您的 Azure 使用者名稱和密碼登入。
   
   不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。 
4. 如果用於記錄的 Azure 使用者針對在金鑰產生期間建立的 Azure 資源帳戶具有正確的[權限](tutorial-prepare-vmware.md#prepare-azure)，就會起始設備註冊。
1. 成功註冊設備之後，您可以按一下 [檢視詳細資料]查看註冊詳細資料。


## <a name="start-continuous-discovery"></a>開始連續探索

設備必須連線到 vCenter Server，才能探索 VM 的設定與效能資料。

1. 在 [步驟 1：選取復原點]**提供 vCenter Server 認證**中，按一下 [新增認證] 以指定認證的自訂名稱，並新增 vCenter Server 帳戶的**使用者名稱**和**密碼**，設備將使用此 vCenter Server 帳戶探索 vCenter Server 執行個體上的 VM。
    - 您應該已在[上一個教學課程](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)中設定具有必要權限的帳戶。
    - 如果您想要將探索範圍限定成特定的 VMware 物件 (vCenter Server 資料中心、叢集、叢集的資料夾、主機、主機的資料夾或個別的 VM)，請參閱本文的[指示](set-discovery-scope.md)來限制 Azure Migrate 所使用的帳戶。
1. 在**步驟 2：提供 vCenter Server 詳細資料**中，按一下 [新增探索來源]，從下拉式選單選取自訂的認證名稱，指定 vCenter Server 執行個體的 **IP 位址/FQDN**。 您可以將**連接埠**保留為預設 (443)，或指定 vCenter Server 接聽的自訂連接埠並按一下 [儲存]。
1. 按一下 [儲存] 時，設備會嘗試使用提供的認證驗證與 vCenter Server 的連線，並根據 vCenter Server 的 IP 位址/FQDN，在資料表中顯示**驗證狀態** 。
1. 您可以在啟動探索之前，隨時**重新驗證** vCenter Server 的連線功能是否正常。
1. 在**步驟 3：提供 VM 認證以探索已安裝的應用程式，並執行無代理程式相依性對應**中，按一下 [新增認證]，並指定要提供認證的作業系統、認證的自訂名稱以及**使用者名稱**和**密碼**。 然後按一下 [儲存]。

    - 如果您已建立可用於[應用程式探索功能](how-to-discover-applications.md)，或[無代理程式相依性分析功能](how-to-create-group-machine-dependencies-agentless.md)的認證，您可以選擇性地在此新增認證。
    - 如果不想使用這些功能，可以按一下滑桿來略過步驟。 您之後可以隨時改變心意。
    - 請檢閱[應用程式探索](migrate-support-matrix-vmware.md#application-discovery-requirements)或[無代理程式相依性分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)所需的認證。

5. 按一下 [開始探索]，即可啟動 VM 探索。 成功起始探索之後，您可以在資料表中檢查 vCenter Server IP 位址/FQDN 的探索狀態。

探索的運作方式如下：
- 所探索到的 VM 中繼資料會在大約 15 分鐘後出現在入口網站中。
- 探索已安裝的應用程式、角色和功能需要花一點時間。 時間長短取決於所探索的 VM 數目。 針對 500 部 VM，應用程式清查大約需要一小時才會出現在 Azure Migrate 入口網站中。

### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索完成後，便可以確認 VM 是否出現在 Azure 入口網站中：

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器 >  **Azure Migrate：伺服器評估** 中，選取圖示以顯示 探索到的伺服器 計數。

## <a name="set-up-an-assessment"></a>設定評估

您可以使用 Azure Migrate 伺服器評估來建立兩種評估：

**評量類型** | **詳細資料**
--- | --- 
**Azure VM** | 評估內部部署伺服器，並將其遷移至 Azure 虛擬機器。 <br/><br/> 您可以使用評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 和[實體伺服器](how-to-set-up-appliance-physical.md)以移轉至 Azure。 [深入了解](concepts-assessment-calculation.md)
**Azure VMware 解決方案 (AVS)** | 評估內部部署伺服器，並將其遷移至 [Azure VMware 解決方案 (AVS)](../azure-vmware/introduction.md)。 <br/><br/> 您可以使用此評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md) 以移轉至 Azure VMware 解決方案 (AVS)。 [深入了解](concepts-azure-vmware-solution-assessment-calculation.md)

伺服器評量提供兩個調整大小準則選項：

**調整大小準則** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 根據所收集的效能資料做出建議的評量 | **VMware VM 評量**：以 CPU 和記憶體使用量資料為基礎的 VM 大小建議。<br/><br/> 磁碟類型建議 (標準 HDD/SSD 或進階受控磁碟) 是以內部部署磁碟的 IOPS 和輸送量為基礎。<br/><br/> **Azure VMware 解決方案 (AVS) 評量**：以 CPU 和記憶體使用量資料為基礎的 AVS 節點建議。
**依內部部署** | 不使用效能資料來提出建議的評量。 | **VMware VM 評量**：VM 大小建議是根據內部部署 VM 大小而提供<br/><br> 建議的磁碟類型是根據您在評量的儲存體類型設定中所選取的內容而定。<br/><br/> **Azure VMware 解決方案 (AVS) 評量**：AVS 節點建議是根據內部部署 VM 大小而提供。

## <a name="run-an-assessment"></a>執行評估

執行 *Azure VM 評量*，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 [伺服器] 索引標籤的 **[Azure Migrate：伺服器評估]** 圖格中，選取 [評估]。

   ![[評估] 按鈕的位置](./media/tutorial-assess-vmware/assess.png)

3. 在 [評估伺服器] 中，選取「Azure VM」作為評量類型，選取探索來源，然後指定評量名稱。

    ![評量基本概念](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. 選取 [檢視全部]，然後檢閱評估屬性。

   ![評量屬性](./media/tutorial-assess-vmware/view-all.png)

5. 按一下 [下一步] 以**選取要評量的機器**。 在 [選取或建立群組] 中，選取 [新建]，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。
6. 在 [將機器新增至群組] 中，選取要新增至群組的 VM。
7. 按一下 下一步 以**檢閱+ 建立評量**來檢閱評量詳細資料。
8. 選取 [建立評估] 以建立群組，然後執行評估。

   ![評定伺服器](./media/tutorial-assess-vmware/assessment-create.png)

8. 評量建立好之後，可在 [伺服器] >  **[Azure Migrate：伺服器評量]**  > [評量] 中加以檢視。
9. 選取 [匯出評估]，將其下載為 Excel 檔案。

如果您想要執行 **Azure VMware 解決方案 (AVS) 評量**，請遵循[這裡](how-to-create-azure-vmware-solution-assessment.md)所敘述的步驟。


## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 移轉整備程度**：VM 是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行 VM 的預估每月計算和儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。

若要檢視評估：

1. 在 [移轉目標] > [伺服器] 中，選取 [Azure Migrate：伺服器評估] 中的 [評估]。
2. 在 [評估] 中，選取評估來加以開啟。

   ![評量摘要](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度] 中，確認 VM 是否已準備好移轉至 Azure。
2. 檢查 VM 狀態：
    - **可供 Azure 使用**：當 Azure Migrate 針對評估中的 VM 建議 VM 大小和成本估計值時會用到。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：當 Azure Migrate 因資料可用性問題而無法評估整備程度時，便會使用此狀態。

3. 選取 [Azure 移轉整備程度] 狀態。 您可以檢視 VM 移轉整備程度的詳細資料。 您也可以向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。

### <a name="review-cost-details"></a>檢閱成本詳細資料

此評估摘要會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。 系統會針對評估群組中的所有 VM 匯總成本。 您可以向下切入以查看特定 VM 的成本詳細資料。

> [!NOTE]
> 系統會根據機器的大小建議、其磁碟和其屬性來預估成本。 此估計適用於執行內部部署 VM 來作為 IaaS Vm。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。

所評估群組的彙總儲存體成本會分割為不同類型的儲存體磁碟。 

### <a name="review-confidence-rating"></a>檢閱信賴評等

Azure Migrate 伺服器評估會對以效能為基礎的評估指派信賴評等，分數為 1 顆星 (最低) 到 5 顆星 (最高)。

![信賴評等](./media/tutorial-assess-vmware/confidence-rating.png)

信賴評等可協助您預估評估的大小建議是否可靠。 此評等會以計算評估所需的資料點可用性作為基礎：

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[了解](best-practices-assessment.md#best-practices-for-confidence-ratings)信賴評等的最佳做法。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定 Azure Migrate 設備。 您也已經建立和檢閱評估。

若要了解如何使用 Azure Migrate 伺服器移轉將 VMware VM 遷移至 Azure，請繼續進行本系列的第三個教學課程：

> [!div class="nextstepaction"]
> [遷移 VMware VM](./tutorial-migrate-vmware.md)
