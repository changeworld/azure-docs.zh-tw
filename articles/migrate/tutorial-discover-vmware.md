---
title: 使用 Azure Migrate 伺服器評量來探索 VMware VM
description: 了解如何使用 Azure Migrate 伺服器評量工具探索內部部署 VMware VM
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e11c3277ffa07fe0a8d5fc7495e2c09152ce585f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704279"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>教學課程：使用伺服器評量來探索 VMware VM

在遷移至 Azure 的過程中，您會探索內部部署清查和工作負載。 

本教學課程說明如何透過「Azure Migrate：伺服器評量工具」探索內部部署 VMware 虛擬機器 (VM)：(使用輕量型 Azure Migrate 設備)。 您會將設備部署為 VMware VM，以持續探索 VM 和效能中繼資料、在 VM 上執行的應用程式，以及 VM 相依性。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定 Azure 帳戶。
> * 準備 VMWare 環境以進行探索。
> * 建立 Azure Migrate 專案。
> * 設定 Azure Migrate 設備。
> * 開始連續探索。

> [!NOTE]
> 教學課程顯示嘗試案例的最快路徑，並且在可行時使用預設選項。  

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>必要條件

在開始本教學課程之前，請先確認您符合下列必要條件。


**需求** | **詳細資料**
--- | ---
**vCenter Server/ESXi 主機** | 您需要執行 5.5、6.0、6.5 或 6.7 版的 vCenter Server。<br/><br/> 必須在執行 5.5 版或更新版本的 ESXi 主機上裝載 VM。<br/><br/> 在 vCenter Server 上，允許 TCP 連接埠 443 上的輸入連線，讓設備可以收集評量資料。<br/><br/> 根據預設，設備會連線到連接埠 443 上的 vCenter。 如果 vCenter 伺服器在不同的連接埠上接聽，您可以在從設備連線至伺服器以進行探索時修改連接埠。<br/><br/> 在裝載 VM 的 EXSi 伺服器上，請確定 TCP 連接埠 443 允許輸入存取，以進行應用程式探索。
**設備** | vCenter Server 需要用來為 Azure Migrate 設備配置 VM 的資源：<br/><br/> - Windows Server 2016<br/><br/> - 32 GB 的 RAM、八個 vCPU，以及大約 80 GB 的磁碟儲存體。<br/><br/> - 外部虛擬交換器，以及 VM 的網際網路存取 (直接或透過 Proxy)。
**VM** | 若要使用本教學課程，Windows VM 必須執行 Windows Server 2016、2012 R2、2012 或 2008 R2。<br/><br/> Linux VM 必須執行 Red Hat Enterprise Linux 7/6/5、Ubuntu Linux 14.04/16.04、Debian 7/8、Oracle Linux 6/7 或 CentOS 5/6/7。<br/><br/> VM 需要已安裝並執行中的 VMware 工具 (高於 10.2.0 的版本)。<br/><br/> 在 Windows VM 上，應安裝 Windows PowerShell 2.0 或更新版本。


## <a name="prepare-an-azure-user-account"></a>準備 Azure 使用者帳戶

若要建立 Azure Migrate 專案並註冊 Azure Migrate 設備，您需要具有下列權限的帳戶：
- Azure 訂用帳戶的參與者或擁有者權限。
- 註冊 Azure Active Directory 應用程式的權限。

如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。 如果您不是訂用帳戶擁有者，請與擁有者合作以指派權限，如下所示：

1. 在 Azure 入口網站中搜尋「訂用帳戶」，然後在 [服務] 底下選取 [訂用帳戶]。

    ![搜尋 Azure 訂用帳戶的搜尋方塊](./media/tutorial-discover-vmware/search-subscription.png)

2. 在 [訂用帳戶] 頁面中，選取您要在其中建立 Azure Migrate 專案的訂用帳戶。 
3. 在訂用帳戶中，選取 [存取控制 (IAM)] > [檢查存取權]。
4. 在 [檢查存取權] 中，搜尋相關的使用者帳戶。
5. 在 [新增角色指派] 中，按一下 [新增]。

    ![搜尋使用者帳戶以檢查存取權並指派角色](./media/tutorial-discover-vmware/azure-account-access.png)

6. 在 [新增角色指派] 中，選取 [參與者] 或 [擁有者] 角色，然後選取帳戶 (在我們的範例中為 azmigrateuser)。 然後按一下 [儲存]  。

    ![開啟 [新增角色指派] 頁面，將角色指派給帳戶](./media/tutorial-discover-vmware/assign-role.png)

7. 在入口網站中搜尋使用者，然後在 [服務] 底下選取 [使用者]。
8. 在 [使用者設定] 中，確認 Azure AD 使用者可以註冊應用程式 (預設為 [是])。

    ![在 [使用者設定] 中確認使用者可以註冊 Active Directory 應用程式](./media/tutorial-discover-vmware/register-apps.png)

9. 或者，租用戶/全域管理員可為帳戶指派 **應用程式開發人員** 角色，以允許註冊 AAD 應用程式。 [深入了解](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>準備 VMware

在 vCenter Server 上，建立可供設備用來存取 vCenter Server 的帳戶，並確認所需的連接埠已開啟。 您也需要可供設備用來存取 VM 的帳戶。 

### <a name="create-an-account-to-access-vcenter"></a>建立帳戶以存取 vCenter

在 vSphere Web 用戶端中設定帳戶，如下所示：

1. 使用具有管理員權限的帳戶，在 vSphere Web 用戶端中選取 [系統管理]。
2. 在 [存取] 中，選取 [SSO 使用者和群組]。
3. 在 [使用者] 中，新增新的使用者。
4. 在 [新增使用者] 中，輸入帳戶詳細資料。 然後按一下 [確定] 。
5. 在 [全域權限] 中選取使用者帳戶，然後將 [唯讀] 角色指派給該帳戶。 然後按一下 [確定] 。
6. 在 [角色] 中選取 [唯讀] 角色，然後在 [權限] 中選取 [來賓作業]。 需要這些權限，才能探索在 VM 上執行的應用程式，以及分析 VM 相依性。
 
    ![允許對唯讀角色進行來賓作業的核取方塊](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>建立帳戶以存取 VM

設備會存取 VM 以探索應用程式，並分析 VM 相依性。 設備不會在 VM 上安裝任何代理程式。

1. 建立可供設備用來探索 Windows VM 上的應用程式和相依性的本機管理員帳戶。
2. 針對 Linux 機器，請建立具有根權限的使用者帳戶，或建立對 /bin/netstat 和 /bin/ls 檔案具有下列權限的使用者帳戶：CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。

> [!NOTE]
> Azure Migrate 支援對所有 Windows 伺服器上的應用程式探索使用單一認證，以及對所有 Linux 機器上的應用程式探索使用單一認證。


## <a name="set-up-a-project"></a>設定專案

設定新的 Azure Migrate 專案。

1. 在 Azure 入口網站 > [所有服務] 中，搜尋 **Azure Migrate**。
2. 在 [服務] 下，選取 [Azure Migrate]。
3. 在 [概觀] 中，選取 [建立專案]。
5. 在 [建立專案] 中，選取您的 Azure 訂用帳戶和資源群組。 如果您還沒有資源群組，請加以建立。
6. 在 [專案詳細資料] 中，指定專案名稱以及要在其中建立專案的地理位置。 請檢閱[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府雲端](migrate-support-matrix.md#supported-geographies-azure-government)支援的地理位置。

   ![專案名稱和區域的方塊](./media/tutorial-discover-vmware/new-project.png)

7. 選取 [建立]。
8. 等候幾分鐘讓 Azure Migrate 專案完成部署。

**Azure Migrate：伺服器評量** 工具依預設會新增至新專案中。

![顯示依預設新增伺服器評量工具的頁面](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>設定設備

若要使用 OVA 範本來設定設備，您可以：
- 提供設備名稱，並在入口網站中產生 Azure Migrate 專案金鑰
- 下載 OVA 範本檔案，並將其匯入 vCenter Server。
- 建立設備，並確認其可以連線至 Azure Migrate 伺服器評估。
- 進行設備的第一次設定，並使用 Azure Migrate 專案金鑰將其註冊至 Azure Migrate 專案。

> [!NOTE]
> 如果您因故無法使用範本設定設備，可以使用 PowerShell 指令碼進行設定。 [深入了解](deploy-appliance-script.md#set-up-the-appliance-for-vmware)。


### <a name="deploy-with-ova"></a>使用 OVA 進行部署

若要使用 OVA 範本來設定設備，您可以：
- 提供設備名稱，並在入口網站中產生 Azure Migrate 專案金鑰
- 下載 OVA 範本檔案，並將其匯入 vCenter Server。
- 建立設備，並確認其可以連線至 Azure Migrate 伺服器評估。
- 進行設備的第一次設定，並使用 Azure Migrate 專案金鑰將其註冊至 Azure Migrate 專案。

### <a name="generate-the-azure-migrate-project-key"></a>產生 Azure Migrate 專案金鑰

1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評估]** 中，選取 [探索]。
2. 在 [探索機器] > [機器是否已虛擬化?] 中，選取 [是，使用 VMware vSphere Hypervisor]。
3. 在 **1：產生 Azure Migrate 專案金鑰** 中，為您要為探索之 VMware VM 設定的 Azure Migrate 設備命名。名稱應使用英數位元，且長度不超過 14 個字元。
1. 按一下 [產生金鑰] ，開始建立必要的 Azure 資源。 在建立資源期間，請勿關閉探索的電腦頁面。
1. 成功建立 Azure 資源之後，系統會產生 **Azure Migrate 專案金鑰**。
1. 複製金鑰，您在設定期間需要此金鑰才能完成設備的註冊。

### <a name="download-the-ova-template"></a>下載 OVA 範本

在 **2：下載 Azure Migrate 設備** 中，選取 .OVA 檔案，然後按一下 [下載]。 


### <a name="verify-security"></a>確認安全性

請先確認 OVA 檔案是安全的，再進行部署：

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 檔案的雜湊：
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   使用方式範例：```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. 確認最新的設備版本與雜湊值：

    - 對於 Azure 公用雲端：
    
        **演算法** | **下載** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - 對於 Azure Government：
    
        **演算法** | **下載** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca




### <a name="create-the-appliance-vm"></a>建立設備 VM

匯入所下載的檔案，並建立 VM。

1. 在 vSphere 用戶端主控台中，按一下 [檔案] > [部署 OVF 範本]。
2. 在 [部署 OVF 範本精靈] > [來源] 中，指定 OVA 檔案的位置。
3. 在 [名稱]  和 [位置] 中，指定 VM 的易記名稱。 選取將裝載 VM 的庫存物件。
5. 在 [主機/叢集] 中，指定 VM 的執行所在主機或叢集。
6. 在 **儲存體** 中，指定 VM 的儲存目的地。
7. 在 [磁碟格式] 中，指定磁碟類型和大小。
8. 在 [網路對應] 中，指定 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure Migrate 伺服器評量。
9. 檢閱並確認設定，然後按一下 [完成]。


### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確定設備 VM 可以連線至[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure URL。


### <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

> [!NOTE]
> 如果您使用 [PowerShell 指令碼](deploy-appliance-script.md)來設定設備 (而非使用下載的 OVA)，則此程序中的前兩個步驟將與之無關。

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 VM，然後選取 [開啟主控台]。
2. 提供設備的語言、時區和密碼。
3. 在任何可連線至 VM 的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址:44368**。

   或者，您也可以選取應用程式捷徑，從設備桌面開啟應用程式。
1. 接受 **授權條款**，並閱讀第三方資訊。
1. 在 [Web 應用程式] > [設定必要條件] 中，執行下列動作：
   - **連線能力**：應用程式會確認 VM 是否能夠存取網際網路。 如果 VM 使用 Proxy：
     - 按一下 [設定 Proxy] 以指定 Proxy 位址 (格式為 http://ProxyIPAddress 或 http://ProxyFQDN) ) 和接聽連接埠。
     - 如果 Proxy 需要驗證，請指定認證。
     - 僅支援 HTTP Proxy。
     - 如果您已新增 Proxy 詳細資料，或已停用 Proxy 和/或驗證，請按一下 [儲存] 以再次觸發連線檢查。
   - **時間同步**：設備上的時間應該與網際網路時間同步，探索才能正常運作。
   - **安裝更新**：設備會確保已安裝最新的更新。 檢查完成之後，您可以按一下 [檢視設備服務] 查看設備上執行之元件的狀態和版本。
   - **安裝 VDDK**：設備會檢查是否已安裝 VMware vSphere 虛擬磁碟開發套件 (VDDK)。 如果未安裝，則會從 VMware 下載 VDDK 6.7，並將下載的 zip 內容解壓縮到設備上的指定位置、如 **安裝指示** 中提供的內容所述。

     在遷移至 Azure 期間，Azure Migrate 伺服器移轉會使用 VDDK 來複寫機器。 
1. 如有需要，可以在設備設定期間隨時 **重新執行必要條件**，以檢查設備是否符合所有必要條件。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 貼上從入口網站複製的 **Azure Migrate 專案金鑰**。 如果沒有金鑰，請移至 **伺服器評估 > 探索 > 管理現有的設備**，選取在金鑰產生時提供的設備名稱，並複製對應的金鑰。
1. 您需要裝置程式碼才能向 Azure 驗證。 按一下 [登入] 會以如下所示的裝置程式碼開啟強制回應。

    ![顯示裝置程式碼的強制回應](./media/tutorial-discover-vmware/device-code.png)

1. 按一下 [複製程式碼和登入] 以複製裝置程式碼，然後在新的瀏覽器索引標籤中開啟 Azure 登入提示。如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
1. 在新的索引標籤上，使用您的 Azure 使用者名稱和密碼登入並貼上裝置程式碼。
   
   不支援使用 PIN 登入。
3. 如果不小心在尚未完成登入流程時關閉了登入索引標籤，則需要重新整理設備組態管理員的瀏覽器索引標籤，才能再次啟用登入按鈕。
1. 成功登入之後，請回到設備組態管理員的上一個索引標籤。
1. 如果用於記錄的 Azure 使用者針對在金鑰產生期間建立的 Azure 資源帳戶具有正確的權限，就會起始設備註冊。
1. 成功註冊設備之後，您可以按一下 [檢視詳細資料]查看註冊詳細資料。



## <a name="start-continuous-discovery"></a>開始連續探索

設備必須連線到 vCenter Server，才能探索 VM 的設定與效能資料。

1. 在 [步驟 1：選取復原點]**提供 vCenter Server 認證** 中，按一下 [新增認證] 以指定認證的自訂名稱，並新增 vCenter Server 帳戶的 **使用者名稱** 和 **密碼**，設備將使用此 vCenter Server 帳戶探索 vCenter Server 執行個體上的 VM。
    - 您應該已在上一個教學課程中設定具有必要權限的帳戶。
    - 如果您想要將探索範圍限定成特定的 VMware 物件 (vCenter Server 資料中心、叢集、叢集的資料夾、主機、主機的資料夾或個別的 VM)，請參閱本文的[指示](set-discovery-scope.md)來限制 Azure Migrate 所使用的帳戶。
1. 在 **步驟 2：提供 vCenter Server 詳細資料** 中，按一下 [新增探索來源]，從下拉式選單選取自訂的認證名稱，指定 vCenter Server 執行個體的 **IP 位址/FQDN**。 您可以將 **連接埠** 保留為預設 (443)，或指定 vCenter Server 接聽的自訂連接埠並按一下 [儲存]。
1. 按一下 [儲存] 時，設備會嘗試使用提供的認證驗證與 vCenter Server 的連線，並根據 vCenter Server 的 IP 位址/FQDN，在資料表中顯示 **驗證狀態** 。
1. 您可以在啟動探索之前，隨時 **重新驗證** vCenter Server 的連線功能是否正常。
1. 在 **步驟 3：提供 VM 認證以探索已安裝的應用程式，並執行無代理程式相依性對應** 中，按一下 [新增認證]，並指定要提供認證的作業系統、認證的自訂名稱以及 **使用者名稱** 和 **密碼**。 然後按一下 [儲存]。

    - 如果您已建立可用於[應用程式探索功能](how-to-discover-applications.md)，或[無代理程式相依性分析功能](how-to-create-group-machine-dependencies-agentless.md)的認證，您可以選擇性地在此新增認證。
    - 如果不想使用這些功能，可以按一下滑桿來略過步驟。 您之後可以隨時改變心意。
    - 請檢閱[應用程式探索](migrate-support-matrix-vmware.md#application-discovery-requirements)或[無代理程式相依性分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)所需的認證。

5. 按一下 [開始探索]，即可啟動 VM 探索。 成功起始探索之後，您可以在資料表中檢查 vCenter Server IP 位址/FQDN 的探索狀態。

探索的運作方式如下：
- 所探索到的 VM 中繼資料會在大約 15 分鐘後出現在入口網站中。
- 探索已安裝的應用程式、角色和功能需要花一點時間。 時間長短取決於所探索的 VM 數目。 針對 500 部 VM，應用程式清查大約需要一小時才會出現在 Azure Migrate 入口網站中。


## <a name="next-steps"></a>後續步驟

- [評估 VMware VM](./tutorial-assess-vmware-azure-vm.md) 以移轉至 Azure VM。
- [檢閱設備在探索期間收集的資料](migrate-appliance.md#collected-data---vmware)。