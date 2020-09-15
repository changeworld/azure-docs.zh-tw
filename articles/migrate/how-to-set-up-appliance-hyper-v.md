---
title: 設定 Hyper-v 的 Azure Migrate 設備
description: 瞭解如何設定 Azure Migrate 設備來評定和遷移 Hyper-v Vm。
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: c53f82268bd1a5d94659a8b749a14fd026f91ce1
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087145"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>為 Hyper-v Vm 設定設備

遵循這篇文章來設定 Azure Migrate 設備，以使用 [Azure Migrate：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具來評估 hyper-v vm。

[Azure Migrate 設備](migrate-appliance.md)是 Azure Migrate 所使用的輕量設備：伺服器評量/遷移以探索內部部署 hyper-v vm，並將 VM 中繼資料/效能資料傳送至 Azure。

您可以使用幾種方法來部署設備：

- 使用下載的 VHD 在 Hyper-v VM 上進行設定。 這是本文中所述的方法。
- 使用 PowerShell 安裝程式指令碼在 Hyper-V VM 或實體機器上進行設定。 如果您無法使用 VHD 設定 VM，或在 Azure Government 中，則應使用[此方法](deploy-appliance-script.md)。

建立設備之後，您會檢查其是否可以連線到 Azure Migrate：伺服器評量、進行第一次設，以及向 Azure Migrate 專案註冊設備。

## <a name="appliance-deployment-vhd"></a>設備部署 (VHD) 

使用 VHD 範本設定設備：

- 提供設備名稱，並在入口網站中產生 Azure Migrate 專案金鑰。
- 從 Azure 入口網站下載壓縮的 Hyper-V VHD。
- 建立設備，並確認其可以連線至 Azure Migrate 伺服器評估。
- 第一次設定設備，並使用 Azure Migrate 專案金鑰向 Azure Migrate 專案註冊。

### <a name="generate-the-azure-migrate-project-key"></a>產生 Azure Migrate 專案金鑰

1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評估]** 中，選取 [探索]。
2. 在 [探索機器]**** > [機器是否已虛擬化?]**** 中，選取 [是，使用 Hyper-V]****。
3. 在 **1：產生 Azure Migrate 專案金鑰**中，為您要為探索之 Hyper-V VM 設定的 Azure Migrate 設備命名。名稱應使用英數位元，且長度不超過 14 個字元。
1. 按一下 [產生金鑰] ，開始建立必要的 Azure 資源。 在建立資源期間，請勿關閉探索的電腦頁面。
1. 成功建立 Azure 資源之後，系統會產生 **Azure Migrate 專案金鑰**。
1. 複製金鑰，您在設定期間需要此金鑰才能完成設備的註冊。

### <a name="download-the-vhd"></a>下載 VHD

在 **2：下載 Azure Migrate 設備**中，選取 .VHD 檔案，然後按一下 [下載]。 

   ![探索電腦的選取項目](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![產生金鑰的選取項目](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 VHD 的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```





## <a name="create-the-appliance-vm"></a>建立設備 VM

匯入所下載的檔案，並建立 VM。

1. 在會裝載設備 VM 的 Hyper-V 主機上，將壓縮的 VHD 檔案解壓縮至其中的資料夾。 會解壓縮出三個資料夾。
2. 開啟 Hyper-V 管理員。 在 [動作] 中，按一下 [匯入虛擬機器]。

    ![部署 VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. 在 [匯入虛擬機器精靈] > [開始之前]，按 [下一步]。
3. 在 [尋找資料夾]**** 中，指定已解壓縮的 VHD 所在的資料夾。 然後按一下 [下一步]  。
1. 在 [選取虛擬機器] 中，按 [下一步]。
2. 在 [選擇匯入類型] 中，按一下 [複製虛擬機器 (建立新的唯一識別碼)]。 然後按一下 [下一步]  。
3. 在 [選擇目的地] 中，保留預設設定。 按 [下一步] 。
4. 在 [儲存資料夾] 中，保留預設設定。 按 [下一步] 。
5. 在 [選擇網路] 中，指定 VM 會使用的虛擬交換器。 此交換器必須能夠連線到網際網路，以將資料傳送至 Azure。
6. 在 [摘要] 中檢閱設定。 然後按一下 [ **完成**]。
7. 在 [Hyper-V 管理員] > [虛擬機器] 中，啟動 VM。


### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確定設備 VM 可以連線至[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure URL。

### <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

> [!NOTE]
> 如果您使用 [PowerShell 指令碼](deploy-appliance-script.md)來設定設備 (而非使用下載的 VHD)，則此程序中的前兩個步驟將與之無關。

1. 在 [Hyper-V 管理員] > [虛擬機器] 中，以滑鼠右鍵按一下 [VM] > [連線]。
2. 提供設備的語言、時區和密碼。
3. 在任何可連線至 VM 的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址:44368**。

   或者，您也可以按一下應用程式捷徑，從設備桌面開啟應用程式。
1. 接受**授權條款**，並閱讀第三方資訊。
1. 在 [Web 應用程式] > [設定必要條件] 中，執行下列動作：
    - **連線能力**：應用程式會確認 VM 是否能夠存取網際網路。 如果 VM 使用 Proxy：
      - 按一下 [設定 Proxy] 以指定 Proxy 位址 (格式為 http://ProxyIPAddress 或 http://ProxyFQDN) ) 和接聽連接埠。
      - 如果 Proxy 需要驗證，請指定認證。
      - 僅支援 HTTP Proxy。
      - 如果您已新增 Proxy 詳細資料，或已停用 Proxy 和/或驗證，請按一下 [儲存] 以再次觸發連線檢查。
    - **時間同步**：系統會確認時間。 設備上的時間應該與網際網路時間同步，VM 探索才能正常運作。
    - **安裝更新**：Azure Migrate Server 評估會檢查設備是否已安裝最新的更新。檢查完成之後，您可以按一下 [檢視設備服務] 以查看設備上執行之元件的狀態和版本。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 貼上從入口網站複製的 **Azure Migrate 專案金鑰**。 如果沒有金鑰，請移至**伺服器評估 > 探索 > 管理現有的設備**，選取在金鑰產生時提供的設備名稱，並複製對應的金鑰。
1. 按一下 [登入]。 系統會在新的瀏覽器索引標籤中開啟 Azure 登入提示。如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
1. 在新的索引標籤上，使用您的 Azure 使用者名稱和密碼登入。
   
   不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。 
4. 如果用於記錄的 Azure 使用者針對在金鑰產生期間建立的 Azure 資源帳戶具有正確的[權限](tutorial-prepare-hyper-v.md#prepare-azure)，就會起始設備註冊。
1. 成功註冊設備之後，您可以按一下 [檢視詳細資料]查看註冊詳細資料。



### <a name="delegate-credentials-for-smb-vhds"></a>委派 SMB VHD 的認證

如果您要在 SMB 上執行 VHD，就必須將認證從設備委派到 Hyper-V 主機。 若要從設備進行此操作：

1. 在設備 VM 上，執行此命令。 HyperVHost1/HyperVHost2 是主機名稱範例。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. 或者，也可以在設備上的本機群組原則編輯器中執行此動作：
    - 在 [本機電腦原則] > [電腦設定] 中，按一下 [系統管理範本] > [系統] > [認證委派]。
    - 按兩下 [允許委派全新認證]，然後選取 [已啟用]。
    - 在 [選項] 中，按一下 [顯示]，然後以 **wsman/** 作為前置詞將您想要探索的每一部 Hyper-V 主機新增至清單中。
    - 在 [認證委派]**** 中，按兩下 [允許委派僅具有 NTLM 伺服器驗證的全新認證]****。 再次以 **wsman/** 作為前置詞將您想要探索的每一部 Hyper-V 主機新增至清單中。

## <a name="start-continuous-discovery"></a>開始連續探索

從設備連線至 Hyper-V 主機或叢集，然後開始探索 VM。

1. 在 [步驟 1：選取復原點]**提供 Hyper-V 主機認證**中，按一下 [新增認證] 以指定認證的自訂名稱，並新增 Hyper-V 主機/叢集的**使用者名稱**和**密碼**，設備將使用此資訊探索  VM。 按一下 [ **儲存**]。
1. 如果想要一次新增多個認證，請按一下 [新增更多] 以儲存並新增更多認證。 Hyper-V VM 探索支援使用多個認證。
1. 在**步驟 2：提供 Hyper-V 主機/叢集詳細資料**中，按一下 [新增探索來源] 來指定 Hyper-V 主機/叢集 **IP 位址/FQDN** 和認證的自訂名稱，以連線到主機/叢集。
1. 您可以一次**新增單一項目**，或**新增多個項目**。 另外也可透過**匯入 CSV** 提供 Hyper-V 主機/叢集詳細資料。

    ![新增探索來源的選取項目](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - 如果您選擇**新增單一項目**，需要指定認證的自訂名稱和 Hyper-V 主機/叢集的 **IP 位址/FQDN**，然後按一下 [儲存]。
    - 如果您選擇**新增多個項目**  _(預設選項)_ ，可以在文字方塊中指定Hyper-V 主機/叢集的 **IP 位址/FQDN** 和認證的自訂名稱，一次新增多筆記錄。**確認**新增的記錄，然後按一下 [儲存]。
    - 如果您選擇**匯入 CSV**，可以下載 CSV 範本檔案，並在檔案中填入 Hyper-V 主機/叢集的 **IP 位址/FQDN** 和憑證的自訂名稱。 然後將檔案匯入設備，**確認**檔案中的記錄，然後按一下 [儲存]。

1. 按一下儲存時，設備會嘗試驗證已新增的 Hyper-V 主機/叢集連線，並在資料表中顯示每部主機/叢集的**驗證狀態**。
    - 對於成功驗證的主機/叢集，您可以按一下其 IP 位址/FQDN 以檢視更多詳細資料。
    - 如果主機驗證失敗，請按一下資料表狀態欄中的 [驗證失敗] 以檢閱錯誤。 修正問題，然後再次驗證。
    - 若要移除主機或叢集，請按一下 [刪除]。
    - 您無法從叢集中移除特定主機。 您只能移除整個叢集。
    - 即使叢集中的特定主機有問題，您還是可以新增叢集。
1. 您可以在啟動探索之前，隨時**重新驗證**主機/叢集的連線功能是否正常。
1. 按一下 [開始探索]，以開始探索成功驗證的 VM 主機/叢集。 成功起始探索之後，您可以在資料表中檢查每部主機/叢集的探索狀態。

這會開始探索。 每部主機大約會需要 2 分鐘，才能讓探索到之伺服器的中繼資料出現在 Azure 入口網站中。

## <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索完成後，便可以確認 VM 是否出現在入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器 >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器 計數。


## <a name="next-steps"></a>後續步驟

試用 Azure Migrate Server 評量的 [hyper-v 評](tutorial-assess-hyper-v.md) 量。
