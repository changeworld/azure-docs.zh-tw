---
title: 設定 Azure VMware 解決方案的 Azure 備份伺服器
description: 設定您的 Azure VMware 解決方案環境，以使用 Azure 備份伺服器來備份虛擬機器。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: c45d2030e282057c487d142669ab634cd81f6929
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079531"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>設定 Azure VMware 解決方案的 Azure 備份伺服器

Azure 備份伺服器是健全的企業備份和復原系統，可為您的商務持續性和嚴重損壞修復（BCDR）策略提供貢獻。 在 Azure VMware 解決方案預覽期間，您只能使用 Azure 備份伺服器來設定虛擬機器（VM）層級的備份。 

Azure 備份伺服器可以將備份資料儲存到：

- **磁片**：針對短期儲存，Azure 備份伺服器會將資料備份到磁片集區。
- **Azure**.. 針對短期和長期的內部部署儲存體，可以使用 Azure 備份，將儲存在磁片集區中的 Azure 備份伺服器資料備份至 Microsoft Azure 雲端。

發生中斷且無法使用來源資料時，您可以使用 Azure 備份伺服器，輕鬆地將資料還原至來源或替代位置。 如此一來，如果原始資料因為計畫或非預期的問題而無法使用，您可以輕鬆地將資料還原至替代位置。

在本文中，我們將協助您準備 Azure VMware 解決方案環境，以使用 Azure 備份伺服器來備份 Vm。 我們會逐步引導您完成下列步驟： 

> [!div class="checklist"]
> * 決定要使用的建議 VM 磁片類型和大小。
> * 建立儲存復原點的復原服務保存庫。
> * 設定復原服務保存庫的儲存體複寫。
> * 將儲存體新增至 Azure 備份伺服器。

## <a name="supported-vmware-features"></a>支援的 VMware 功能

- **無代理程式備份：** Azure 備份伺服器不需要在 vCenter 或 ESXi 伺服器上安裝代理程式來備份虛擬機器。 相反地，只需提供 IP 位址或完整功能變數名稱（FQDN），以及用來驗證 VMware 伺服器與 Azure 備份伺服器的登入認證。
- **雲端整合式備份：** Azure 備份伺服器可保護磁片和雲端的工作負載。 Azure 備份伺服器的備份和復原工作流程可協助您管理長期保留和異地備份。
- 偵測**並保護 vCenter 所管理的 vm：** Azure 備份伺服器會偵測並保護部署在 vCenter 或 ESXi 伺服器上的 Vm。 Azure 備份伺服器也會偵測 vCenter 管理的 Vm，讓您可以保護大型部署。
- **資料夾層級 start-autoprotection：** vCenter 可讓您在 vm 資料夾中組織您的 vm。 Azure 備份伺服器會偵測到這些資料夾，您可以使用它來保護資料夾層級（包括所有子資料夾）的 Vm。 保護資料夾時，Azure 備份伺服器不僅會保護該資料夾中的 Vm，也會保護稍後新增的 Vm。 Azure 備份伺服器會每天偵測新的 Vm，並自動加以保護。 當您在遞迴資料夾中組織 Vm 時，Azure 備份伺服器會自動偵測並保護部署在遞迴資料夾中的新 Vm。
- **Azure 備份伺服器會繼續保護叢集中的 VMotioned vm：** 當 Vm 在叢集內 vMotioned 以進行負載平衡時，Azure 備份伺服器會自動偵測並繼續 VM 保護。
- **更快復原所需**的檔案：Azure 備份伺服器可以從 Windows VM 復原檔案或資料夾，而不需要復原整個 VM。

## <a name="limitations"></a>限制

- 必須安裝 Azure 備份伺服器 v3 的更新彙總套件1。
- 第一次 Azure 備份伺服器備份之前，您無法備份使用者快照集。 Azure 備份伺服器完成第一次備份之後，您就可以備份使用者快照集。
- Azure 備份伺服器無法使用傳遞磁片和實體的原始裝置對應（pRDMs）來保護 VMware Vm。
- Azure 備份伺服器無法偵測或保護 VMware vApps。

若要設定 Azure VMware 解決方案的 Azure 備份伺服器，您必須完成下列步驟：

- 設定必要條件和環境。
- 建立復原服務保存庫。
- 下載並安裝 Azure 備份伺服器。
- 將儲存體新增至 Azure 備份伺服器。

### <a name="deployment-architecture"></a>部署架構

Azure 備份伺服器會部署為 Azure 基礎結構即服務（IaaS） VM，以保護 Azure VMware 解決方案 Vm。

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="AVS 部署架構" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure 備份伺服器環境的必要條件

當您在 Azure 環境中安裝 Azure 備份伺服器時，請考慮本節中的建議。

### <a name="azure-virtual-network"></a>Azure 虛擬網路

請確定您已[在 Azure 中為您的 VMware 私人雲端設定網路](tutorial-configure-networking.md)功能。

### <a name="determine-the-size-of-the-virtual-machine"></a>判斷虛擬機器的大小

您必須在上一個步驟中建立的虛擬網路中建立 Windows 虛擬機器。 當您選擇要執行 Azure 備份伺服器的伺服器時，請從 Windows Server 2019 Datacenter 的資源庫映射開始。 本教學課程會[在 Azure 入口網站中建立您的第一個 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)，讓您在 azure 中開始使用建議的 VM，即使您從未使用過 azure 也一樣。

下表摘要說明每個 Azure 備份伺服器虛擬機器大小之受保護工作負載的最大數目。 此資訊會取決於內部效能和延展性測試，以及工作負載的大小和變換的標準值。 實際的工作負載大小可能較大，但應由連接到 Azure 備份伺服器虛擬機器的磁片容納。

| 受保護的工作負載上限 | 平均工作負載大小 | 平均工作負載變換 (每日) | 儲存體 IOPS 下限 | 建議的磁片類型/大小      | 建議的 VM 大小 |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | 5% 淨變換                   | 2,000             | 標準 HDD （每個磁片 8 TB 或以上大小）  | A4V2       |
| 40                      | 150 GB                | 10% 淨變換                  | 4,500             | 進階 SSD * （1 TB 或以上的每個磁片大小） | DS3_V2     |
| 60                      | 200 GB                | 10% 淨變換                  | 10500            | 進階 SSD * （每個磁片 8 TB 或以上的大小） | DS3_V2     |

* 若要取得所需的 IOPs，請使用最小的建議大小或更大的磁片。 大小較小的磁片會提供較低的 IOPs。

> [!NOTE]
> Azure 備份伺服器的設計目的是在專用、單一用途的伺服器上執行。 您無法在下列電腦上安裝 Azure 備份伺服器：
> * 以網域控制站的形式執行。
> * 已安裝應用程式伺服器角色。
> * 是 System Center Operations Manager 管理伺服器。
> * 執行 Exchange Server。
> * 是叢集的節點。

### <a name="disks-and-storage"></a>磁碟與儲存體

Azure 備份伺服器需要安裝磁片，其中包括系統檔案、安裝檔案、先決條件軟體、資料庫檔案，以及存放集區的專用磁片。

| 需求                      | 建議大小  |
|----------------------------------|-------------------------|
| Azure 備份伺服器安裝                | 安裝位置： 3 GB<br />資料庫檔案磁碟機：900 MB<br />系統磁片磁碟機： 1 GB 用於 SQL Server 安裝<br /><br />當您封存時，您也需要 Azure 備份伺服器空間，將檔案類別目錄複寫到暫存安裝位置。      |
| 存放集區的磁碟<br />（使用基本磁碟區，不可以在動態磁碟上） | 受保護資料大小的二到三倍。<br />如需詳細的儲存體計算，請參閱[DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301)。   |

若要瞭解如何將新的受控資料磁片連結至現有的 Azure VM，請參閱[使用 Azure 入口網站將受控資料磁片連結至 WINDOWS VM](../virtual-machines/windows/attach-managed-disk-portal.md)。

> [!NOTE]
> 單一 Azure 備份伺服器的儲存集區具有 120 TB 的軟性限制。

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>將備份資料儲存在本機磁片和 Azure 中

將備份資料儲存在 Azure 中可減少 Azure 備份伺服器 VM 上的備份基礎結構。 針對作業復原（備份），Azure 備份伺服器會將備份資料儲存在連接至 VM 的 Azure 磁片上。 將磁片和儲存空間連接至 VM 之後，Azure 備份伺服器為您管理儲存體。 備份資料儲存體的數量取決於連接到每個 Azure VM 的磁片數目和大小。 Azure VM 的每個大小都有可以附加的磁片數目上限。 例如，A2 是四個磁片，A3 是八個磁片，而 A4 是16個磁片。 同樣地，磁片大小和數量會決定備份存放集區的總容量。

> [!IMPORTANT]
> 您*不*應該將操作修復資料保留在 Azure 備份伺服器連接的磁片上超過五天。 如果資料超過五天，請將其儲存在復原服務保存庫中。

若要將備份資料儲存在 Azure 中，請建立或使用復原服務保存庫。 當您準備備份 Azure 備份伺服器工作負載時，您可以[設定復原服務保存庫](#create-a-recovery-services-vault)。 一旦設定之後，每次執行線上備份作業時，就會在保存庫中建立復原點。 每個復原服務保存庫最多可容納9999個復原點。 視所建立的復原點數目和保留的時間長度而定，您可以保留備份資料數年。 例如，您可以建立每月復原點，並保留5年。

> [!IMPORTANT]
> 無論您是將備份資料傳送至 Azure 或將其保留在本機，都必須向復原服務保存庫註冊 Azure 備份伺服器。

### <a name="scale-deployment"></a>調整部署

如果您想要調整部署，您有以下幾種選擇：

- 相應**增加**：將 Azure 備份伺服器 VM 的大小從數列增加至 DS3 數列，並增加本機儲存體。
- 卸載**資料**：將較舊的資料傳送至 Azure，並只將最新資料保留在連接至 Azure 備份伺服器機的儲存體上。
- **相應**放大：新增更多 Azure 備份伺服器機來保護工作負載。

### <a name="net-framework"></a>.NET Framework

VM 必須安裝 .NET Framework 3.5 SP1 或更新版本。

### <a name="join-a-domain"></a>加入網域

Azure 備份伺服器的 VM 必須加入網域，且在 VM 上具有系統管理員許可權的網域使用者必須安裝 Azure 備份伺服器。

雖然在預覽期間不受支援，但在 Azure VM 中部署 Azure 備份伺服器可以在 Azure VMware 解決方案中備份 Vm 上的工作負載。 工作負載應位於相同的網域中，才能啟用備份作業。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是儲存一段時間後所建立之復原點的儲存體實體。 它也包含與受保護專案相關聯的備份原則。

1. 在 [Azure 入口網站](https://portal.azure.com/)中登入您的訂用帳戶。

1. 在左側功能表上，選取 [**所有服務**]。

   ![選取所有服務](../backup/media/backup-create-rs-vault/click-all-services.png)

1. 在 [**所有服務**] 對話方塊中，輸入復原**服務**，並從清單中選取 [復原**服務保存庫**]。

   ![輸入並選擇復原服務保存庫](../backup/media/backup-create-rs-vault/all-services.png)

   隨即會在訂用帳戶中出現 [復原服務保存庫] 清單。

1. 在 [復原**服務保存庫**] 儀表板上，選取 [**新增**]。

   ![新增復原服務保存庫](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   [復原服務保存庫]**** 對話方塊隨即開啟。

1. 輸入 [**名稱**]、[**訂**用帳戶]、[**資源群組**] 和 [**位置**] 的值。

   ![設定復原服務保存庫](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **名稱**：輸入可識別保存庫的易記名稱。 該名稱必須是 Azure 訂用帳戶中唯一的名稱。 請指定至少有兩個字元，但不能超過50個字元的名稱。 名稱開頭必須是字母，且只能包含字母、數字和連字號。
   - 訂用帳戶****：選擇要使用的訂用帳戶。 如果您是唯一一個訂用帳戶的成員，就會看到該名稱。 如果您不確定要使用哪個訂用帳戶，請使用預設 (建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
   - **資源群組**：使用現有的資源群組或建立新群組。 若要查看您訂用帳戶中可用的資源群組清單，請選取 [使用現有的]****，然後從下拉式清單中選取資源。 若要建立新的資源群組，請選取 [新建]****，然後輸入名稱。
   - **位置**：選取保存庫的地理區域。 若要建立保存庫來保護 Azure VMware 解決方案虛擬機器，保存庫*必須*位於與 Azure vmware 解決方案私人雲端相同的區域中。

1. 當您準備好建立復原服務保存庫時，選取 [建立]****。

   ![建立復原服務保存庫](../backup/media/backup-create-rs-vault/click-create-button.png)

   建立復原服務保存庫可能需要一些時間。 在入口網站右上角的 [**通知**] 區域中，監視狀態通知。 保存庫建立之後，就可以在 [復原服務保存庫] 的清單中看到。 如果您沒有看到保存庫，請選取 [重新整理]。

   ![重新整理備份保存庫的清單](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>設定儲存體複寫

儲存體複寫選項可讓您在異地冗余儲存體（預設值）和本機多餘儲存體之間做選擇。 異地冗余儲存體會將儲存體帳戶中的資料複製到次要區域，讓您的資料持久。 本機多餘的儲存空間是較便宜的選項，不是持久的。 若要深入瞭解異地冗余和本機多餘的儲存體選項，請參閱[Azure 儲存體冗余](../storage/common/storage-redundancy.md)。

> [!IMPORTANT]
> 在保存庫中設定備份之前，必須先變更復原服務保存庫的**儲存體複寫類型設定-多餘/異地-多餘**。 在您設定備份之後，將會停用修改的選項，而且您無法變更儲存體複寫類型。

1. 在 [復原**服務保存庫**] 中，選取新的保存庫。 

1. 在 [設定]**** 下方，選取 [屬性]****。 在 [**備份**設定] 底下，選取 [**更新**]。

1. 選取儲存體複寫類型，然後選取 [**儲存]。**

   ![設定新保存庫的存放裝置配置](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-the-software-package"></a>下載並安裝軟體套件

遵循本節中的步驟，下載、解壓縮及安裝軟體套件。

### <a name="download-the-software-package"></a>下載軟體套件

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 如果您已開啟復原服務保存庫，請繼續進行下一個步驟。 如果您未開啟復原服務保存庫，但位於 Azure 入口網站，請在主功能表上選取 **[流覽]**。

   1. 在資源清單中，輸入**復原服務**。

   1. 當您開始輸入時，清單會根據您的輸入來篩選。 當您看到 [復原服務保存庫] **** 時，請選取它。

   ![建立復原服務保存庫步驟1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. 在 [復原服務保存庫] 清單中選取保存庫。

   選取的保存庫儀表板隨即開啟。

   ![開啟保存庫儀表板](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   預設會開啟 [**設定**] 選項。 如果已關閉，請選取 [**設定**] 以開啟它。

   ![開啟保存庫設定選項](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. 選取 [**備份**] 以開啟 [**消費者入門**wizard]。

   ![消費者入門 wizard 開啟備份](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. 在開啟的視窗中，執行下列動作：

   1. 從 [**您的工作負載在何處執行？** ] 功能表中，選取 [**內部部署**]。

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="您的工作負載在何處執行？":::

   1. 從 [**您要備份什麼？** ] 功能表中，選取您想要使用 Azure 備份伺服器來保護的工作負載。

   1. 選取 [**準備基礎結構**] 以下載並安裝 Azure 備份伺服器和保存庫認證。

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="準備基礎結構":::

1. 在開啟的 [**準備基礎結構**] 視窗中，執行下列動作：

   1. 選取 [**下載**] 連結以安裝 Azure 備份伺服器。

   1. 選取 [**已下載] 或 [使用最新的 Azure 備份伺服器安裝**] 核取方塊，然後選取 [**下載**]，以下載保存庫認證。 在 Azure 備份伺服器註冊復原服務保存庫期間，您會使用保存庫認證。 這些連結會帶您前往下載中心，您可以在其中下載軟體套件。

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="準備基礎結構-Azure 備份伺服器":::

1. 在下載頁面上，選取所有檔案，然後選取 **[下一步]**。

   > [!NOTE]
   > 您必須將所有檔下載到相同的資料夾。 因為檔案的下載大小同時大於 3 GB，所以下載完成最多可能需要60分鐘的時間。 

   ![在下載中心選取檔案](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>解壓縮軟體套件

如果您已將軟體套件下載至不同的伺服器，請將檔案複製到您所建立的虛擬機器以部署 Azure 備份伺服器。

> [!WARNING]
> 至少 4 GB 的可用空間，才能將安裝程式檔案解壓縮。

1. 下載所有檔案之後，請按兩下 [ **MicrosoftAzureBackupInstaller.exe** ] 以開啟 [ **Microsoft Azure 備份**安裝程式]，然後選取 **[下一步]**。

1. 選取要解壓縮檔案的位置，然後選取 **[下一步]**。

1. 選取 [**解壓縮**] 以開始解壓縮程式。

   ![Microsoft Azure 備份安裝程式嚮導](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. 解壓縮之後，請選取要**執行 setup.exe**的選項，然後選取 **[完成]**。

> [!TIP]
> 您也可以從解壓縮軟體套件的資料夾中找出 setup.exe 檔案。

### <a name="install-the-software-package"></a>安裝軟體封裝

1. 在安裝程式視窗的 [**安裝**] 下，選取 [ **Microsoft Azure 備份**] 以開啟安裝程式。

   ![Microsoft Azure 備份安裝程式的 [安裝] 按鈕](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. 在 [**歡迎使用**] 畫面上，選取 **[下一步]** 以繼續前往 [必要條件**檢查**] 頁面。

1. 再次選取 [**檢查**]，以判斷是否符合 Azure 備份伺服器的硬體和軟體必要條件。 如果成功符合，請選取 **[下一步]**。

   ![Azure 備份伺服器必要條件檢查](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Azure 備份伺服器安裝套件隨附所需的適當 SQL Server 二進位檔。 當您啟動新的 Azure 備份伺服器安裝時，請選取 [**使用此安裝選項安裝 SQL Server 的新實例**]。 然後選取 [**檢查並安裝**]。

   ![Azure 備份伺服器 SQL Server 檢查](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > 如果您想要使用自己的 SQL Server 實例，支援的 SQL Server 版本為 SQL Server 2014 SP1 或更高版本、2016和2017。 所有 SQL Server 版本都應為 Standard 或 Enterprise 64-bit。 Azure 備份伺服器無法與遠端 SQL Server 實例搭配使用。 Azure 備份伺服器所使用的執行個體必須位於本機。 如果您針對 Azure 備份伺服器使用現有的 SQL Server 實例，則安裝程式只支援使用 SQL Server 的*已命名實例*。

   如果發生失敗且建議您重新開機電腦，請執行這項操作，然後再次選取 [**檢查**]。 如果有任何 SQL Server 設定問題，請根據 SQL Server 方針重新設定 SQL Server。 然後，使用現有的 SQL Server 實例重試安裝或升級 Azure 備份伺服器。

   **手動設定**

   當您使用自己的 SQL Server 實例時，請務必將 builtin\Administrators 加入至 master 資料庫的系統管理員（sysadmin）角色。

   **具有 SQL Server 2017 的 SSRS 設定**

   當您使用自己的 SQL Server 2017 實例時，您必須手動設定 SQL Server 2017 Reporting Services （SSRS）。 在 SSRS 設定之後，請確定 SSRS 的**IsInitialized**屬性已設為**True**。 當這個屬性設定為**True**時，Azure 備份伺服器會假設 ssrs 已設定並略過 ssrs 設定。

   若要檢查 SSRS 設定狀態，請執行下列命令：

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   將下列值使用於 SSRS 設定：

   * **服務帳戶**： [**使用內建帳戶**] 應該是 [**網路服務**]。
   * **Web 服務 URL**：**虛擬目錄**應**ReportServer_ \<SQLInstanceName> **。
   * **資料庫**： **DatabaseName**應該是**ReportServer $ \<SQLInstanceName> **。
   * **入口網站 URL**：**虛擬目錄**應**Reports_ \<SQLInstanceName> **。

   [深入了解](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) SSRS 設定。

   > [!NOTE]
   > [Microsoft 線上服務條款](https://www.microsoft.com/licensing/product-licensing/products)（OST）會管理用來做為 Azure 備份伺服器資料庫之 SQL Server 的授權。 根據 OST，與 Azure 備份伺服器配套的 SQL Server 只能用來作為 Azure 備份伺服器的資料庫。

1. 安裝成功之後，請選取 **[下一步]**。

1. 提供安裝 Microsoft Azure 備份伺服器檔案的位置，然後選取 **[下一步]**。

   > [!NOTE]
   > 需要暫存位置才能備份至 Azure。 請確保暫存位置至少為打算備份至雲端的資料的 5%。 針對磁片保護，必須在安裝完成之後設定不同的磁片。 如需存放集區的詳細資訊，請參閱[設定存放集區和磁片儲存體](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))。

   ![Microsoft Azure 備份安裝程式安裝設定](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. 為受限制的本機使用者帳戶提供強式密碼，然後選取 **[下一步]**。

   ![Microsoft Azure 備份安裝程式安全性設定](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. 選取您是否要使用 Microsoft Update 來檢查更新，然後選取 **[下一步]**。

   > [!NOTE]
   > 我們建議您將 Windows Update 重新導向至 Microsoft Update，以提供 Windows 和其他產品（例如 Azure 備份伺服器）的安全性和重要更新。

   ![Microsoft Azure 備份安裝程式 Microsoft Update 加入宣告](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. 檢查**設定摘要**，然後選取 [**安裝**]。

   安裝分階段執行。 第一個階段會安裝 Microsoft Azure 復原服務代理程式，而第二個階段會檢查網際網路連線能力。 如果有網際網路連線能力，您可以繼續進行安裝。 如果沒有，您必須提供 proxy 詳細資料，以連線到網際網路。 最後一個階段會檢查先決條件軟體。 如果未安裝，則會與 Microsoft Azure 復原服務代理程式一起安裝任何遺失的軟體。

1. 選取 **[流覽]** 以找出您的保存庫認證，以向復原服務保存庫註冊機器，然後選取 **[下一步]**。

1. 選擇一個複雜密碼來加密或解密 Azure 與您的內部部署之間傳送的資料。

   > [!TIP]
   > 您可以自動產生複雜密碼，或提供您自己的複雜密碼，最少 16 個字元。

1. 輸入要儲存複雜密碼的位置，然後選取 **[下一步]** 以註冊伺服器。

   > [!IMPORTANT]
   > 將此複雜密碼儲存到本機伺服器以外的安全位置。 我們強烈建議使用 Azure Key Vault 來儲存複雜密碼。

   Microsoft Azure 復原服務代理程式安裝完成之後，安裝步驟會移至 SQL Server 和 Azure 備份伺服器元件的安裝和設定。

   ![Microsoft Azure 備份安裝程式安裝](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. 安裝步驟完成後，請選取 [**關閉**]。

### <a name="install-update-rollup-1"></a>安裝更新彙總套件1

您必須先安裝 Azure 備份伺服器 v3 的更新彙總套件1，才能保護工作負載。 若要查看 bug 修正清單，以及 Azure 備份伺服器 v3 更新彙總套件1的安裝指示，請參閱知識庫文章[4534062](https://support.microsoft.com/en-us/help/4534062/)。

## <a name="add-storage-to-azure-backup-server"></a>在 Azure 備份伺服器中新儲存體

Azure 備份伺服器 v3 支援提供的 Modern Backup Storage：

-  節省50% 的儲存空間。
-  備份速度加快三倍。
-  更有效率的儲存空間。
-  工作負載感知儲存體。

### <a name="volumes-in-azure-backup-server"></a>Azure 備份伺服器中的磁片區

將具有所需儲存容量的資料磁片新增至 Azure 備份伺服器虛擬機器（如果尚未新增）。

Azure 備份伺服器 v3 只接受存放磁片區。 當您新增磁片區時，Azure 備份伺服器會將磁片區格式化為復原檔案系統（ReFS），Modern Backup Storage 需要此檔案。

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>將磁片區新增至 Azure 備份伺服器磁片儲存體

1. 在 [**管理**] 窗格中，重新掃描存放裝置，然後選取 [**新增**]。 

1. 從可用的磁片區中選取，以新增至存放集區。 

1. 新增可用的磁片區之後，請為它們提供易記名稱，以協助您進行管理。 

1. 選取 **[確定]** 將這些磁片區格式化為 ReFS，讓 Azure 備份伺服器可以使用 Modern Backup Storage 的優點。

![新增可用的磁片區](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>後續步驟

繼續進行下一個教學課程，以瞭解如何使用 Azure 備份伺服器來設定在 Azure VMware 解決方案上執行的 VMware Vm 備份。

> [!div class="nextstepaction"]
> [設定 Azure VMware 解決方案 Vm 的備份](backup-avs-vms-with-mabs.md)
