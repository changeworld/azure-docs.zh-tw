---
title: 使用 Azure Migrate 伺服器評定來評定要移轉到 Azure 的實體伺服器
description: 說明如何使用 Azure Migrate 伺服器評定來評定內部部署實體伺服器是否可移轉到 Azure。
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: c89c731712a625e5f3b7a1a7e9306f6a7480b96b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990295"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>使用 Azure Migrate 來評定實體伺服器：伺服器評量

此文章說明如何使用 Azure Migrate 來評定內部部署實體伺服器：伺服器評量工具。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。

此教學課程是一個系列中的第二篇，會示範如何評定實體伺服器並將其移轉到 Azure。 在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 設定 Azure Migrate 專案。
> * 設定內部部署環境中執行的 Azure Migrate 設備來評定實體伺服器。
> * 開始連續探索內部部署實體伺服器。 設備會將探索到的伺服器組態與效能資料傳送到 Azure。
> * 將探索到的伺服器分組，然後評定伺服器群組。
> * 檢閱評估結果。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱操作說明文章。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>Prerequisites

- [完成](tutorial-prepare-physical.md)本系列的第一個教學課程。 如果未完成，本教學課程中的指示便沒有作用。
- 您在第一個教學課程中應該已完成下列作業：
    - 針對 Azure Migrate [設定 Azure 權限](tutorial-prepare-physical.md#prepare-azure)。
    - [準備實體伺服器](tutorial-prepare-physical.md#prepare-for-physical-server-assessment)進行評定。 應該驗證設備需求。 您也應該設定用於實體伺服器探索的帳戶。 必要的連接埠應可供使用，而且您應該知道存取 Azure 所需的 URL。


## <a name="set-up-an-azure-migrate-project"></a>設定 Azure Migrate 專案

設定新的 Azure Migrate 專案，如下所示。

1. 在 Azure 入口網站 > [所有服務]  中，搜尋 **Azure Migrate**。
2. 在 [服務]  下，選取 [Azure Migrate]  。
3. 在 [概觀]  的 [探索、評估和遷移伺服器]  底下，按一下 [評估和遷移伺服器]  。

    ![探索和評估伺服器](./media/tutorial-assess-physical/assess-migrate.png)

4. 在 [開始使用]  中，按一下 [新增工具]  。
5. 在 [Migrate 專案]  中選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有的話)。     
6. 在 [專案詳細資料]  中，指定專案名稱以及要在其中建立專案的地理位置。 支援亞洲、歐洲、英國和美國。

    - 專案地理區域只會用來儲存從內部部署伺服器收集到的中繼資料。
    - 當您執行移轉時，可以選取任何目的地區域。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-physical/migrate-project.png)


7. 按 [下一步]  。
8. 在 [選取評量工具]  中，選取 **[Azure Migrate：伺服器評量]**  > [下一步]  。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-physical/assessment-tool.png)

9. 在 [選取移轉工具]  中，選取 [暫時跳過新增移轉工具]   > [下一步]  。
10. 在 [檢閱 + 新增工具]  中檢閱設定，然後按一下 [新增工具]  。
11. 等候幾分鐘讓 Azure Migrate 專案完成部署。 您會進入專案頁面。 如果您沒有看到專案，則可以從 Azure Migrate 儀表板中的 [伺服器]  來存取。


## <a name="set-up-the-appliance"></a>設定設備

Azure Migrate：伺服器評定會執行輕量型設備。

- 此設備會執行實體伺服器探索，並將伺服器中繼資料與效能資料傳送至「Azure Migrate 伺服器評定」。
- 若要設定設備，請：
    - 從 Azure 入口網站下載含有 Azure Migrate 安裝程式指令碼的 ZIP 壓縮檔案。
    - 從 ZIP 壓縮檔案解壓縮內容。 以系統管理權限啟動 PowerShell 主控台。
    - 執行 PowerShell 指令碼以啟動設備 Web 應用程式。
    - 第一次設定設備，並向 Azure Migrate 專案進行註冊。
- 您可以為單一 Azure Migrate 專案設定多個設備。 在所有設備上，您可以探索任何數目的實體伺服器。 每個設備最多可探索 250 部伺服器。

### <a name="download-the-installer-script"></a>下載安裝程式指令碼

下載設備的 ZIP 壓縮檔案。

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2. 在 [探索機器]   > [機器是否已虛擬化?]  中，按一下 [未虛擬化/其他]  。
3. 按一下 [下載]  以下載 ZIP 壓縮檔案。

    ![下載安裝程式](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3.  針對最新的設備版本，產生的雜湊應符合這些設定。

  **演算法** | **雜湊值**
  --- | ---
  MD5 | 1e92ede3e87c03bd148e56a708cdd33f
  SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-azure-migrate-installer-script"></a>執行 Azure Migrate 安裝程式指令碼

此安裝程式指令碼會執行下列作業︰

- 安裝用於實體伺服器探索及評定的代理程式與 Web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 與 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續設定詳細資料來更新登錄機碼 (HKLM)。
- 在路徑底下建立下列檔案：
    - **組態檔**: %ProgramData%\Microsoft Azure\Config
    - **記錄檔**: %ProgramData%\Microsoft Azure\Logs

執行指令碼，如下所示：

1. 將 ZIP 壓縮檔案解壓縮至會裝載設備之伺服器上的資料夾。
2. 在上述伺服器上，使用系統管理 (提高的) 權限來啟動 PowerShell。
3. 將 PowerShell 目錄變更為已從下載的 ZIP 壓縮檔案解壓縮內容的資料夾。
4. 藉由執行下列命令，以執行名為 **AzureMigrateInstaller.ps1** 的指令碼：
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
指令碼會在成功完成時啟動設備 Web 應用程式。

如果發生任何問題，您可以存取位於 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>時間戳記</em>.log 的指令碼記錄，以進行疑難排解。

> [!NOTE]
> 請不要在現有的 Azure Migrate 設備上執行 Azure Migrate 安裝程式指令碼。

### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確定設備可以連線至 [Azure URL](migrate-appliance.md#url-access)。


### <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在任何可連線至設備的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址  :44368**。

   或者，您也可以按一下應用程式捷徑，從桌面開啟應用程式。
2. 在 [Web 應用程式] > [設定必要條件]  中，執行下列動作：
    - **授權**：接受授權條款，並閱讀第三方資訊。
    - **連線能力**：應用程式會確認伺服器是否能夠存取網際網路。 如果伺服器使用 Proxy：
        - 按一下 [Proxy 設定]  ，然後以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定 Proxy 位址和接聽連接埠。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**：系統會確認時間。 設備上的時間應該與網際網路時間同步，伺服器探索才能正常運作。
    - **安裝更新**：Azure Migrate 伺服器評估會確認設備是否已安裝最新的更新。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 按一下 [登入]  。 如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
2. 在新的索引標籤上，使用您的 Azure 認證登入。
    - 以您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。
4. 選取 Azure Migrate 專案建立所在的訂用帳戶。 然後選取專案。
5. 指定設備的名稱。 名稱應該是英數位元，且長度不超過 14 個字元。
6. 按一下 [註冊]  。


## <a name="start-continuous-discovery"></a>開始連續探索

現在，從設備連線至要探索的實體伺服器，然後開始探索。

1. 按一下 [新增認證]  以指定設備會用來探索伺服器的帳戶認證。  
2. 指定**作業系統**、認證的易記名稱、**使用者名稱**與**密碼**，然後按一下 [新增]  。
您可以新增一組適用於 Windows 與 Linux 伺服器的認證。
4. 按一下 [新增伺服器]  ，然後指定伺服器詳細資料 - FQDN/IP 位址與認證的易記名稱 (每列一筆輸入) 以連線至伺服器。
3. 按一下 **[驗證]** 。 驗證之後，就會顯示可探索的伺服器清單。
    - 如果伺服器驗證失敗，請將滑鼠停留在 [狀態]  欄中的圖示上以檢閱錯誤。 修正問題，然後再次驗證。
    - 若要移除伺服器，請選取 > [刪除]  。
4. 驗證之後，按一下 [儲存並開始探索]  來開始探索程序。

這會開始探索。 每部伺服器大約耗用 1.5 分鐘，讓所探索到伺服器的中繼資料出現在 Azure 入口網站中。

### <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

探索完成後，便可以驗證伺服器是否出現在 Azure 入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器   >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器  計數。

## <a name="set-up-an-assessment"></a>設定評估

您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 以所收集效能資料為基礎的評估 | **建議的 VM 大小**：以 CPU 和記憶體使用量資料為基礎。<br/><br/> **建議的磁碟類型 (標準或進階受控磁碟**)：以內部部署磁碟的 IOPS 和輸送量為基礎。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：以內部部署伺服器大小為基礎<br/><br> **建議的磁碟類型**：以您為評估選取的儲存體類型設定為基礎。


### <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 [伺服器]  索引標籤的 **[Azure Migrate：伺服器評量]** 磚中，按一下 [評估]  。

    ![評定](./media/tutorial-assess-physical/assess.png)

2. 在 [評估伺服器]  中，指定評估的名稱。
3. 按一下 [檢視全部]  來檢閱評估屬性。

    ![評量屬性](./media/tutorial-assess-physical/view-all.png)

3. 在 [選取或建立群組]  中，選取 [新建]  ，然後指定群組名稱。 群組會將一或多部伺服器收集在一起以進行評量。
4. 在 [將電腦新增至群組]  中，選取要新增至群組的伺服器。
5. 按一下 [建立評估]  以建立群組，然後執行評估。

    ![建立評估](./media/tutorial-assess-physical/assessment-create.png)

6. 評量建立好之後，可在 [伺服器]   >  **[Azure Migrate：伺服器評量]**  > [評量]  中加以檢視。
7. 按一下 [匯出評估]  ，將其下載為 Excel 檔案。



## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 移轉整備程度**：伺服器是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行伺服器的預估每月計算與儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。

### <a name="view-an-assessment"></a>檢視評估

1. 在 [移轉目標]   >  [伺服器]  中，按一下 [Azure Migrate：伺服器評量]  中的 [評量]  。
2. 在 [評量]  中，按一下評量來加以開啟。

    ![評量摘要](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度]  中，確認伺服器是否已準備好移轉至 Azure。
2. 檢閱狀態：
    - **可供 Azure 使用**：Azure Migrate 會針對評估中的 VM 建議 VM大小和成本估計值。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：當 Azure Migrate 因資料可用性問題而無法評估整備程度時，便會使用此狀態。

2. 按一下 [Azure 移轉整備程度]  狀態。 您可以檢視伺服器的整備程度詳細資料，並向下切入以查看伺服器詳細資料，包括計算、儲存體與網路設定。



### <a name="review-cost-details"></a>檢閱成本詳細資料

此檢視會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。

1. 檢閱每月的計算和儲存體成本。 系統會針對評估群組中的所有伺服器彙總成本。

    - 系統會根據機器的大小建議和其磁碟與屬性來預估成本。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 成本估計方式是以 IaaS VM 形式執行內部部署伺服器。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。

2. 您可以檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。
3. 您可以向下切入以查看特定伺服器的詳細資料。


### <a name="review-confidence-rating"></a>檢閱信賴評等

當您執行以效能為基礎的評估時，系統會對評估指派信賴評等。

![信賴評等](./media/tutorial-assess-physical/confidence-rating.png)

- 會給予 1 星 (最低) 到 5 星 (最高) 的評等。
- 信賴評等可協助您預估評估作業所提供的大小建議是否可靠。
- 信賴評等會以計算評估所需的資料點可用性為基礎。

評估的信賴評等如下所示。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[深入了解](best-practices-assessment.md#best-practices-for-confidence-ratings)信賴評等的最佳做法。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 已設定 Azure Migrate 設備
> * 已建立和檢閱評估

請繼續進行此系列的第三個教學課程，以了解如何使用 Azure Migrate 將實體伺服器移轉到 Azure：伺服器移轉。

> [!div class="nextstepaction"]
> [移轉實體伺服器](./tutorial-migrate-physical-virtual-machines.md)
