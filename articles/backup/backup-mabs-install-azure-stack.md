---
title: 在 Azure Stack 上安裝 Azure 備份伺服器
description: 在本文中，您將了解如何使用 Azure 備份伺服器來保護或備份 Azure Stack 中的工作負載。
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a95ee42c3229e642bd48b812eb6bc01636af3ba2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018122"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>在 Azure Stack 上安裝 Azure 備份伺服器

本文說明如何在 Azure Stack 上安裝 Azure 備份伺服器。 使用 Azure 備份伺服器，即可保護基礎結構即服務 (IaaS) 的 工作負載，例如於 Azure Stack 執行的虛擬機器。 使用 Azure 備份伺服器來保護工作負載的優點在於，您可以從單一主控台管理所有工作負載的保護作業。

> [!NOTE]
> 若要深入了解安全性功能，請參閱 [Azure 備份安全性功能文件](backup-azure-security-feature.md)。
>

## <a name="azure-backup-server-protection-matrix"></a>Azure 備份伺服器保護矩陣

Azure 備份伺服器會保護下列 Azure Stack 虛擬機器工作負載。

| 受保護的資料來源 | 保護和復原 |
| --------------------- | ----------------------- |
| Windows Server Semi Annual Channel - 資料中心/企業/標準 | 磁碟區、檔案、資料夾 |
| Windows Server 2016 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| Windows Server 2012 R2 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| Windows Server 2012 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| Windows Server 2008 R2 - 資料中心/企業版/標準版 | 磁碟區、檔案、資料夾 |
| SQL Server 2016 | 資料庫 |
| SQL Server 2014 | 資料庫 |
| SQL Server 2012 SP1 | 資料庫 |
| SharePoint 2016 | 伺服器陣列、資料庫、前端、網路伺服器 |
| SharePoint 2013 | 伺服器陣列、資料庫、前端、網路伺服器 |
| SharePoint 2010 | 伺服器陣列、資料庫、前端、網路伺服器 |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure 備份伺服器環境的必要條件

在 Azure Stack 環境中安裝 Azure 備份伺服器時，請將本節的建議納入考量。 Azure 備份伺服器安裝程式會檢查您的環境是否具備各項必要條件，但在安裝前先準備妥當可以省下更多時間。

### <a name="determining-size-of-virtual-machine"></a>決定虛擬機器大小

若要在 Azure Stack 虛擬機器上執行 Azure 備份伺服器，請使用 A2 或更大的大小。 如需選擇虛擬機器大小方面的協助，請下載 [Azure Stack VM 大小計算機](https://www.microsoft.com/download/details.aspx?id=56832) (英文)。

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Azure Stack 虛擬機器上的虛擬網路

Azure Stack 工作負載中使用的所有虛擬機器必須屬於同一個 Azure 虛擬網路與 Azure 訂用帳戶。

### <a name="azure-backup-server-vm-performance"></a>Azure 備份伺服器 VM 效能

如果與其他虛擬機器共用，儲存體帳戶大小與 IOPS 限制會影響 Azure 備份伺服器虛擬機器的效能。 有鑑於此，Azure 備份伺服器虛擬機器應使用另外的存放區帳戶。 在 Azure 備份伺服器上執行的 Azure 備份代理程式需要暫存存放區，以供：

- 自用 (快取位置)
- 供從雲端還原的資料使用 (本機暫存區域)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>設定 Azure 備份暫存磁碟存放區

每個 Azure Stack 虛擬機器皆隨附暫存磁碟存放區，可供使用者作為磁碟區 `D:\`。 Azure 備份所需的本機暫存區域可設定成位在 `D:\` 中，快取位置可位於 `C:\`。 如此一來，便無須將存放區與連結到 Azure 備份伺服器虛擬機器的資料磁碟劃分開來。

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>將備份資料儲存在本機磁碟與 Azure 中

Azure 備份伺服器會將備份資料儲存在連結至虛擬機器的 Azure 磁碟上，以復原作業。 在磁碟與儲存空間連結到虛擬機器後，Azure 備份伺服器便會為您管理存放區。 備份資料存放區的數量視連結到每個 [Azure Stack 虛擬機器](/azure-stack/user/azure-stack-storage-overview)的磁碟數目和大小而定。 每個 Azure Stack VM 的大小，均有可連結至虛擬機器的磁碟數目上限。 例如，A2 是四個磁碟。 A3 是八個磁碟。 A4 是 16 個磁碟。 同樣地，磁碟的大小和數目決定了總備份存放集區。

> [!IMPORTANT]
> 您**不應該**將作業復原 (備份) 資料保留在 Azure 備份伺服器連結的磁碟上超過五天。
>

將備份資料儲存在 Azure 中，會縮小 Azure Stack 上的備份基礎結構。 如果資料超過五天，請儲存到 Azure 中。

若要將備份資料儲存在 Azure 中，請建立或使用復原服務保存庫。 在準備 Azure 備份伺服器工作負載的備份作業時，需[設定復原服務保存庫](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault)。 設定完成後，每次執行備份作業時，系統就會在保存庫中建立復原點。 每個復原服務保存庫可保留最多 9999 個復原點。 根據建立的復原點數目以及保留的時間長度，您可以保留備份資料多年。 例如，您可以建立每月復原點，並保留復原點五年。

### <a name="scaling-deployment"></a>調整部署

如果您想要調整部署，您有以下幾種選擇：

- 縱向擴大：將 Azure 備份伺服器虛擬機器的大小從 A 系列增加到 D 系列，並[遵照 Azure Stack 虛擬機器指示](/azure-stack/user/azure-stack-manage-vm-disks)增加本機存放區。
- 卸載資料：將較舊的資料傳送至 Azure，只將最新資料保留在與 Azure 備份伺服器連結的存放區。
- 橫向擴展：新增更多的 Azure 備份伺服器來保護工作負載。

### <a name="net-framework"></a>.NET Framework

虛擬機器上需安裝 .NET framework 3.5 SP1 或更高版本。

### <a name="joining-a-domain"></a>加入網域

Azure 備份伺服器虛擬機器必須加入網域。 具有系統管理員權限的網域使用者必須在虛擬機器上安裝 Azure 備份伺服器。

## <a name="using-an-iaas-vm-in-azure-stack"></a>使用 Azure Stack 中的 IaaS 虛擬機器

為 Azure 備份伺服器選擇伺服器時，請從 Windows Server 2012 R2 Datacenter 或 Windows Server 2016 Datacenter 資源庫映像開始。 即使您之前從未使用過 Azure，[快速入門：在 Azure 入口網站中建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)一文提供有教學課程，說明如何使用建議的虛擬機器。 伺服器虛擬機器 (VM) 的最低建議需求應該是︰A2 標準，具備 2 個核心及 3.5 GB 的 RAM。

使用 Azure 備份伺服器保護工作負載有許多細節需要注意。 [MABS 的保護矩陣](./backup-mabs-protection-matrix.md)有助於說明這些細節。 在部署機器之前，請先確實閱讀此文章。

> [!NOTE]
> Azure 備份伺服器的設計目的是在專用、單一用途的虛擬機器上執行。 您無法在下列上安裝 Azure 備份伺服器：
>
> - 執行為網域控制站的電腦
> - 安裝應用程式伺服器角色所在的電腦
> - Exchange Server 執行所在的電腦
> - 屬於叢集節點的電腦

Azure 備份伺服器一律加入網域。 如果您需要將 Azure 備份伺服器移到不同的網域，請先安裝 Azure 備份伺服器，然後將該伺服器加入新網域。 一旦您部署 Azure 備份伺服器，即無法將它移到新的網域。

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>設定儲存體複寫

復原服務保存庫儲存體複寫選項有異地備援儲存體和本地備援儲存體可供您選擇。 根據預設，復原服務保存庫會使用異地備援儲存體。 如果這個保存庫是您的主要保存庫，儲存體選項請保持設定為異地備援儲存體。 如果您想要更便宜的選項，請選擇本地多餘的儲存體。 在 [Azure 儲存體複寫概觀](../storage/common/storage-redundancy.md)中，深入了解[異地備援](../storage/common/storage-redundancy.md)和[本地備援](../storage/common/storage-redundancy.md)儲存體選項。

若要編輯儲存體複寫設定︰

1. 選取保存庫以開啟保存庫儀表板和 [設定] 功能表。 如果未開啟 [ **設定** ] 功能表，請選取保存庫儀表板中的 **所有設定** 。
2. 在 [**設定**] 功能表上，選取 [**備份基礎結構**  >  **備份**設定] 以開啟 [**備份**設定] 功能表。 在 [備份組態]  功能表上，選擇保存庫的儲存體複寫選項。

    ![備份保存庫的清單](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>下載 Azure 備份伺服器安裝程式

有兩種方式可以下載 Azure 備份伺服器安裝程式。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=55269)下載 Azure 備份安裝程式。 您也可以在設定復原服務保存庫時，下載 Azure 備份伺服器安裝程式。 設定復原服務保存庫時，下列步驟會引導您從 Azure 入口網站下載安裝程式。

1. 請由您的 Azure Stack 虛擬機器，[在 Azure 入口網站中登入您的 Azure 訂用帳戶](https://portal.azure.com/)。
2. 在左側功能表中，選取 [所有服務]。

    ![在主功能表中選擇 [所有服務] 選項](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. 在 [所有服務] 對話方塊中，輸入 Recovery Services。 當您開始輸入時，您的輸入會篩選資源清單。 當您看到 [復原服務保存庫] 時，請選取該選項。

    ![在 [所有服務] 對話方塊中，輸入 Recovery Services](./media/backup-mabs-install-azure-stack/all-services.png)

    隨即會在訂用帳戶中出現 [復原服務保存庫] 清單。

4. 在復原服務保存庫清單中，選取您的保存庫以開啟其儀表板。

    ![選取您的保存庫以開啟儀表板](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. 在保存庫的 [消費者入門] 功能表中，選取 [ **備份** ] 以開啟消費者入門 wizard。

    ![備份開始使用](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    備份功能表隨即開啟。

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. 在備份功能表中，從 [您的工作負載在何處執行] 功能表，選取 [內部部署]。 從 [你想要備份哪些項目？] 下拉式功能表中，選取您要使用 Azure 備份伺服器保護的工作負載。 如果您不確定要選取哪些工作負載，請選擇 [ **Hyper-v 虛擬機器** ]，然後選取 [ **準備基礎結構**]。

    ![內部部署和做為目標的工作負載](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    [準備基礎結構] 功能表隨即開啟。

7. 在 [ **準備基礎結構** ] 功能表中，選取 [ **下載** ] 以開啟網頁，以下載 Azure 備份伺服器安裝檔案。

    ![開始使用精靈變更](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    內有 Azure 備份伺服器的可下載檔案的 Microsoft 網頁隨即開啟。

8. 在 Microsoft Azure 備份伺服器下載] 頁面中，選擇語言，然後選取 [ **下載**]。

    ![下載中心隨即開啟](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Azure 備份伺服器安裝程式是由八個檔案組成，共有一個安裝程式和七個 .bin 檔案。 檢查 **檔案名** 以選取所有必要的檔案，然後選取 **[下一步]**。 將所有檔案下載至相同的資料夾。

    ![下載中心，選取的檔案](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    所有安裝檔案的下載大小超過 3 GB。 在 10 Mbps 下載連結中，下載所有安裝檔案可能需要長達 60 分鐘。 檔案會下載到您指定的下載位置。

## <a name="extract-azure-backup-server-install-files"></a>擷取 Azure 備份伺服器安裝檔案

將所有檔案下載到您的 Azure Stack 虛擬機器之後，請移至下載位置。 安裝 Azure 備份伺服器的第一步是解壓縮檔案。

![下載 MABS 安裝程式](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. 若要開始安裝，請從下載的檔案清單中選取 [ **MicrosoftAzureBackupserverInstaller.exe**]。

    > [!WARNING]
    > 至少 4 GB 的可用空間，才能將安裝程式檔案解壓縮。
    >

2. 在 Azure 備份伺服器 wizard 中選取 **[下一步** ] 以繼續。

    ![Microsoft Azure 備份安裝精靈](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. 選擇 Azure 備份伺服器檔案的路徑，然後選取 [ **下一步]**。

   ![選取檔案的目的地](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. 確認解壓縮位置，然後選取 [ **解壓縮**]。

   ![確認解壓縮位置](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. 精靈會擷取檔案並準備安裝過程。

   ![Wizard 解壓縮檔案](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. 在解壓縮程式完成之後，請選取 **[完成]**。 依預設，已選取 [執行 setup.exe]。 當您選取 **[完成]** 時，Setup.exe 會將 Microsoft Azure 備份伺服器安裝至指定的位置。

   ![安裝程式會將 Microsoft Azure 備份的伺服器檔案解壓縮](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>安裝軟體封裝

在上一個步驟中，您選取 **[完成] 結束** 解壓縮階段，然後啟動 Azure 備份伺服器安裝程式]。

![Microsoft Azure 備份安裝 Wizard 啟動](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure 備份伺服器與 Data Protection Manager 共用程式碼。 您會在 Azure 備份伺服器安裝程式中看到 Data Protection Manager 和 DPM 的參考。 雖然 Azure 備份伺服器與 Data Protection Manager 是個別產品，但是這些產品息息相關。

1. 若要啟動安裝程式，請選取 [ **Microsoft Azure 備份 Server**]。

   ![選取 Microsoft Azure 備份 Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. 在 [ **歡迎使用** ] 畫面上，選取 **[下一步]**。

    ![Azure 備份伺服器-歡迎使用](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. 在 [必要條件 **檢查** ] 畫面上，選取 [ **檢查** ] 以判斷是否已符合 Azure 備份伺服器的硬體和軟體必要條件。

    ![Azure 備份伺服器-必要條件檢查](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    如果您的環境具有必要的必要條件，您會看到一則訊息，指出電腦符合需求。 選取 [下一步] 。  

    ![Azure 備份伺服器 - 必要條件檢查通過](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    如果您的環境不符合所需的必要條件，則會指出相關問題。 不符合的必要條件也會列在需 dpmsetup.ini 中。 請解決必要條件方面的錯誤，然後執行 [再檢查一次]。 在符合所有必要條件之前，無法繼續安裝。

    ![Azure 備份伺服器 - 不符合安裝必要條件](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure 備份伺服器需要 SQL Server。 Azure 備份伺服器安裝封裝隨附適當的 SQL Server 二進位檔。 您也可以使用您自己的 SQL 安裝。 不過，建議選擇讓安裝程式新增 SQL Server 新執行個體。 若要確保您的選擇可與您的環境搭配運作，請選取 [ **檢查並安裝**]。

   > [!NOTE]
   > Azure 備份伺服器無法與遠端 SQL Server 實例搭配運作。 Azure 備份伺服器所使用的執行個體必須位於本機。
   >

    ![Azure 備份伺服器-SQL 設定](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    檢查之後，如果虛擬機器具有安裝 Azure 備份伺服器所需的必要條件，請選取 **[下一步]**。

    ![符合 Azure 備份伺服器需求](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    如果發生失敗且建議您重新啟動電腦，請重新啟動電腦。 重新開機電腦之後，請重新開機安裝程式，當您進入 [ **SQL 設定** ] 畫面時，請選取 [ **檢查**]。

5. 在 [ **安裝設定**] 中，提供安裝 Microsoft Azure 備份伺服器檔案的位置，然後選取 **[下一步]**。

    ![提供安裝檔案的位置](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    需要暫存位置，才能備份至 Azure。 請確保暫存位置的大小至少等於為打算備份至 Azure 之資料的 5%。 在磁碟保護方面，安裝完成之後必須設定獨立的磁碟。 如需有關儲存集區的詳細資訊，請參閱 [準備資料存放區](/system-center/dpm/plan-long-and-short-term-data-storage?view=sc-dpm-2019)。

6. 在 [ **安全性設定** ] 畫面上，為受限的本機使用者帳戶提供強式密碼，然後選取 **[下一步]**。

    ![Security settings screen](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. 在 **Microsoft Update 加入宣告** ] 畫面上，選取您是否要使用 *Microsoft Update* 來檢查更新，然後選取 **[下一步]**。

   > [!NOTE]
   > 建議讓 Windows Update 重新導向至 Microsoft Update，此網站為 Windows 和 Microsoft Azure 備份伺服器等其他產品提供安全性和重要更新。
   >

    ![Microsoft Update 加入宣告畫面](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. 檢查 *設定的摘要* ，然後選取 [ **安裝**]。

    ![設定摘要](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    當 Azure 備份伺服器完成安裝時，安裝程式會立即啟動 Microsoft Azure 復原服務代理程式安裝程式。

9. Microsoft Azure 復原服務代理程式安裝程式隨即開啟，並開始檢查網際網路連線。 如果有網際網路連線可用，請繼續進行安裝。 如果沒有連線能力，請提供 proxy 詳細資料來連線到網際網路。 指定 proxy 設定之後，請選取 **[下一步]**。

    ![Proxy 組態](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. 若要安裝 Microsoft Azure 復原服務代理程式，請選取 [ **安裝**]。

    ![代理程式安裝](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Microsoft Azure 復原服務代理程式 (也稱為「Azure 備份代理程式」) 可將 Azure 備份伺服器設為復原服務保存庫。 一旦設定之後，Azure 備份伺服器一律會將資料備份至相同的復原服務保存庫。

11. Microsoft Azure 復原服務代理程式完成安裝之後，請選取 **[下一步** ] 以啟動下一個階段：向復原服務保存庫註冊 Azure 備份伺服器。

    ![代理程式安裝已成功完成](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    安裝程式會啟動**註冊伺服器精靈**。

12. 切換至您的 Azure 訂用帳戶和您的復原服務保存庫。 在 [ **準備基礎結構** ] 功能表中，選取 [ **下載** ] 以下載保存庫認證。 如果步驟2中的 [ **下載** ] 按鈕未啟用，請選取 **[已下載] 或使用最新的 Azure 備份伺服器安裝** 來啟用按鈕。 保存庫認證會下載到您儲存下載項目的位置。 請注意此位置，因為下一個步驟需要此位置。

    ![下載保存庫認證](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. 在 [保存 **庫識別** ] 功能表中，選取 **[流覽]** 以尋找復原服務保存庫認證。

    ![保存庫識別功能表](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    在 [ **選取保存庫認證** ] 對話方塊中，移至 [下載位置]，選取您的保存庫認證，然後選取 [ **開啟**]。

    認證的路徑會出現在 [保存庫識別] 功能表中。 選取 **[下一步]** 繼續進行 **加密設定**。

14. 在 [ **加密設定** ] 對話方塊中，提供備份加密的複雜密碼，以及用來儲存複雜密碼的位置，然後選取 **[下一步]**。

    ![加密設定](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    您可以提供您自己的複雜密碼，或使用複雜密碼產生器以建立一個密碼。 複雜密碼是您的，Microsoft 不會儲存或管理此複雜密碼。 若要為嚴重損壞做好準備，請將您的複雜密碼儲存在可存取的位置。

    選取 [ **下一步]** 之後，Azure 備份伺服器就會向復原服務保存庫註冊。 安裝程式會繼續安裝 SQL Server 和 Azure 備份伺服器。

    ![安裝程式會安裝 SQL 和 Azure 備份伺服器](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. 當安裝程式完成時， **狀態** 會顯示已成功安裝所有軟體。

    ![軟體已安裝成功](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    安裝完成時，會在伺服器桌面上建立 Azure 備份伺服器主控台和 Azure 備份伺服器 PowerShell 圖示。

## <a name="add-backup-storage"></a>新增備份儲存體

第一個備份複本會保存在連接至 Azure 備份伺服器機器的儲存體上。 如需新增磁碟的詳細資訊，請參閱[新增新式備份儲存體](/system-center/dpm/add-storage)。

> [!NOTE]
> 即使您打算將資料傳送至 Azure，也必須新增備份儲存體。 在「Azure 備份伺服器」架構中，復原服務保存庫會保存資料的*第二個*資料複本，而本機儲存體則是保存第一個 (必要的) 備份複本。
>
>

## <a name="network-connectivity"></a>網路連線

Azure 備份伺服器需要連線至 Azure 備份服務，產品才能順利運作。 若要驗證機器是否連接至 Azure，請在Azure 備份伺服器 PowerShell 主控台中使用 ```Get-DPMCloudConnection``` Cmdlet。 如果 Cmdlet 的輸出為 TRUE，表示連線存在，否則就不會有連線能力。

同時，Azure 訂用帳戶也必須處於狀況良好狀態。 若要了解訂用帳戶狀態並加以管理，請登入[訂用帳戶入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

在您了解 Azure 連線和 Azure 訂用帳戶的狀態後，您可以使用下表來確認提供的備份/還原功能會受到哪些影響。

| 連線狀態 | Azure 訂閱 | 備份至 Azure | 備份到磁碟 | 從 Azure 還原 | 從磁碟還原 |
| --- | --- | --- | --- | --- | --- |
| 連線 |Active |允許 |允許 |允許 |允許 |
| 連線 |已過期 |已停止 |已停止 |允許 |允許 |
| 連線 |已取消佈建 |已停止 |已停止 |已停止且已刪除 Azure 復原點 |已停止 |
| 連線中斷 > 15 天 |Active |已停止 |已停止 |允許 |允許 |
| 連線中斷 > 15 天 |已過期 |已停止 |已停止 |允許 |允許 |
| 連線中斷 > 15 天 |已取消佈建 |已停止 |已停止 |已停止且已刪除 Azure 復原點 |已停止 |

### <a name="recovering-from-loss-of-connectivity"></a>從連線中斷的情況復原

如果防火牆或 Proxy 阻止存取 Azure，請將下列網域位址加入防火牆/Proxy 設定檔允許清單中：

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

在 Azure 的連線還原至 Azure 備份伺服器之後，Azure 訂用帳戶狀態決定可以執行的操作。 當伺服器為 [已連線] 之後，請使用 [[網路連線]](backup-mabs-install-azure-stack.md#network-connectivity) 中的表格以查看可用操作。

### <a name="handling-subscription-states"></a>處理訂用帳戶狀態

您可以將 Azure 訂用帳戶從 [已過期] 或 [已取消佈建] 狀態變更為 [使用中] 狀態。 當訂用帳戶狀態 *為非作用*中時：

- 訂用帳戶為 [已取消佈建]，其功能無法運作。 將訂用帳戶還原至 [使用中]，恢復備份/還原功能。 如果本機磁碟上的備份資料保留了足夠長的保留期限，可以擷取備份資料。 不過，一旦訂用帳戶進入 [已取消佈建] 狀態，Azure 中的備份資料便會遺失而無法復原。
- 訂用帳戶為 [已過期]，其功能無法運作。 排程的備份在訂用帳戶 *過期*時不會執行。

## <a name="troubleshooting"></a>疑難排解

如果 Microsoft Azure 備份伺服器在安裝階段 (或是備份或還原) 失敗且出現錯誤，請參閱這份[錯誤碼文件](https://support.microsoft.com/kb/3041338)。
您也可以參考 [Azure 備份相關的常見問題集](backup-azure-backup-faq.md)

## <a name="next-steps"></a>後續步驟

[準備您的 DPM 環境](/system-center/dpm/prepare-environment-for-dpm)一文，包含關於支援之 Azure 備份伺服器組態的資訊。

請參閱下列文章，以深入了解使用 Microsoft Azure 備份伺服器來保護工作負載。

- [SQL Server 備份](./backup-mabs-sql-azure-stack.md)
- [SharePoint 伺服器備份](./backup-mabs-sharepoint-azure-stack.md)
- [替代伺服器備份](backup-azure-alternate-dpm-server.md)
