---
title: 教學課程 - 將 SAP HANA 資料庫備份至 Azure VM
description: 在本教學課程中，您將了解如何將執行於 Azure VM 上的 SAP HANA 資料庫備份至 Azure 備份復原服務保存庫。
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f89d21a252870befae7807d2dda96828aaaa1326
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439665"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>教學課程：將 SAP HANA 資料庫備份至 Azure VM

本教學課程說明如何將執行於 Azure VM 上的 SAP HANA 資料庫備份至 Azure 備份復原服務保存庫。 在本文中，您將了解如何：

> [!div class="checklist"]
>
> * 建立和設定保存庫
> * 探索資料庫
> * 設定備份

[這裡](sap-hana-backup-support-matrix.md#scenario-support)提供我們目前支援的所有案例。

>[!NOTE]
>從 2020 年 8 月 1 日起，適用於 RHEL 的 SAP Hana 備份 (7.4、7.6、7.7 和 8.1) 已正式推出。

## <a name="prerequisites"></a>必要條件

設定備份之前，請務必先執行下列動作：

* 在與執行 SAP Hana 的 VM 相同的區域和訂用帳戶中識別或建立[復原服務保存庫](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)。
* 允許從 VM 到網際網路的連線，使其可連線到 Azure，如下方的[設定網路連線](#set-up-network-connectivity)程序所說明。
* 確定 Azure Resoure Manager ARM_ VM 的 SAP Hana 伺服器 VM 名稱和資源群組名稱的合併長度未超過 84 個字元 (傳統 VM 的限制為 77 個字元)。 之所以有此限制，是因為服務會保留某些字元。
* **hdbuserstore** 中應該會有一個金鑰符合下列準則：
  * 此金鑰應位於預設的 **hdbuserstore** 中。 預設值是已安裝之 SAP HANA 下的 `<sid>adm` 帳戶。
  * 針對 MDC，此金鑰應指向 **NAMESERVER**的 SQL 連接埠。 在 SDC 的案例中，則應指向 **INDEXSERVER** 的 SQL 連接埠
  * 此金鑰應具有新增和刪除使用者的認證
* 以根使用者身分，在安裝 HANA 的虛擬機器中執行 SAP HANA 備份設定指令碼 (預先註冊指令碼)。 [此指令碼](https://aka.ms/scriptforpermsonhana)會為 HANA 系統做好備份的準備。 請參閱[預先註冊指令碼的功能](#what-the-pre-registration-script-does)一節，以深入了解預先註冊指令碼。

>[!NOTE]
>預先註冊指令碼會為在 RHEL (7.4、7.6 及 7.7) 上執行的 SAP Hana 工作負載安裝 **compat-unixODBC234**，並為 RHEL 8.1 安裝 **unixODBC**。 [此套件位於 RHEL for SAP Hana (適用於 RHEL 7 伺服器) SAP 解決方案 (RPM) 的更新服務存放庫](https://access.redhat.com/solutions/5094721) \(英文\)。  對於 Azure Marketplace RHEL 映像，存放庫是 **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>設定網路連線

進行所有作業時，在 Azure VM 上執行的 SAP Hana 資料庫都需要連線到 Azure 備份服務、Azure 儲存體和 Azure Active Directory。 這可以藉由使用私人端點，或允許存取所需的公用 IP 位址或 FQDN 來達成。 若不允許正確連線到所需的 Azure 服務，可能會導致資料庫探索、設定備份、執行備份和還原資料等作業失敗。

下表列出您可以用來建立連線的各種替代方案：

| **選項**                        | **優點**                                               | **缺點**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 私人端點                 | 允許透過虛擬網路內的私人 IP 進行備份  <br><br>   可對網路和保存庫端進行更細微的控制 | 會產生標準私人端點的[費用](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG 服務標籤                  | 會自動合併範圍變更，因此更容易管理   <br><br>   沒有額外的成本 | 只能搭配 NSG 使用  <br><br>    提供整個服務的存取權 |
| Azure 防火牆 FQDN 標籤          | 會自動管理所需的 FQDN，因此更容易管理 | 只能搭配 Azure 防火牆使用                         |
| 允許存取服務 FQDN/IP | 沒有額外的成本   <br><br>  適用於所有網路安全性應用裝置和防火牆 | 可能需要存取一組廣泛的 IP 或 FQDN   |
| 使用 HTTP Proxy                 | VM 的單一網際網路存取點                       | 使用 Proxy 軟體執行 VM 時的額外成本         |

如需使用這些選項的更多詳細資料，請瀏覽下列內容：

### <a name="private-endpoints"></a>私人端點

私人端點可讓您從虛擬網路內的伺服器安全地連線到您的復原服務保存庫。 私人端點會針對您的保存庫使用 VNET 位址空間中的 IP。 您在虛擬網路內的資源與保存庫之間的網路流量，會透過您的虛擬網路和 Microsoft 骨幹網路上的私人連結來傳輸。 這可避免資料在公用網際網路暴露。 請在[這裡](./private-endpoints.md)詳讀 Azure 備份的私人端點詳細資料。

### <a name="nsg-tags"></a>NSG 標籤

如果您使用網路安全性群組 (NSG)，請使用 AzureBackup 服務標籤，以允許對 Azure 備份進行輸出存取。 除了 Azure 備份標籤之外，您還需要為 Azure AD 和 Azure 儲存體建立類似的 [NSG](../virtual-network/security-overview.md#service-tags) 規則，以允許用於驗證和資料傳輸的連線。  下列步驟說明建立 Azure 備份標籤規則的程序：

1. 在 [所有服務] 中，移至 [網路安全性群組]，然後選取網路安全性群組。

1. 選取 [設定] 底下的 [輸出安全性規則]。

1. 選取 [新增]。 輸入可供用於建立新規則的所有必要詳細資料，如[安全性規則設定](../virtual-network/manage-network-security-group.md#security-rule-settings)中所述。 請確定 [目的地] 選項已設定為 [服務標籤]，且 [目的地服務標籤] 已設定為 [AzureBackup]。

1. 按一下 [新增] 以儲存新建立的輸出安全性規則。

同樣地，您也可以建立 Azure 儲存體和 Azure AD 的 NSG 輸出安全性規則。 如需服務標記的詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)。

### <a name="azure-firewall-tags"></a>Azure 防火牆標籤

如果您使用的是 Azure 防火牆，請使用 [AzureBackup] 的 [Azure 防火牆標籤](../firewall/fqdn-tags.md)來建立應用程式規則。 這會允許對 Azure 備份進行的所有輸出存取。

### <a name="allow-access-to-service-ip-ranges"></a>允許存取服務 IP 範圍

如果您選擇允許存取服務 IP，請在[此處](https://www.microsoft.com/download/confirmation.aspx?id=56519)參閱 JSON 檔案中可用的 IP 範圍。 您必須允許存取對應至 Azure 備份、Azure 儲存體和 Azure Active Directory 的 IP。

### <a name="allow-access-to-service-fqdns"></a>允許存取服務 FQDN

您也可以使用下列 FQDN 來允許從伺服器存取所需的服務：

| 服務    | 要存取的網域名稱                             |
| -------------- | ------------------------------------------------------------ |
| Azure 備份  | `*.backup.windowsazure.com`                             |
| Azure 儲存體 | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | 請遵循[本文](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)中第 56 和第 59 節來允許存取 FQDN |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>使用 HTTP Proxy 伺服器以路由流量

當您備份執行於 Azure VM 上的 SAP Hana 資料庫時，VM 上的備份擴充功能會使用 HTTPS API 將管理命令傳送至 Azure 備份，並將資料傳送至 Azure 儲存體。 備份延伸模組也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 使用上述的 IP 和 FQDN 清單來允許存取所需的服務。 不支援已驗證的 Proxy 伺服器。

## <a name="what-the-pre-registration-script-does"></a>預先註冊指令碼的功能

執行預先註冊指令碼時，會執行下列功能：

* 根據您的 Linux 發行版本，指令碼會安裝或更新 Azure 備份代理程式所需的任何必要套件。
* 會對 Azure 備份伺服器和相依服務 (如 Azure Active Directory 和 Azure 儲存體) 執行輸出網路連線檢查。
* 會使用列為[必要條件](#prerequisites)之一的使用者金鑰登入您的 HANA 系統。 使用者金鑰可用來在 HANA 系統中建立備份使用者 (AZUREWLBACKUPHANAUSER)，且使用者金鑰可在預先註冊指令碼成功執行之後刪除。
* AZUREWLBACKUPHANAUSER 會被指派下列必要的角色和權限：
  * 資料庫管理員 (如果是 MDC) 和備份管理員 (如果是 SDC)：用以在還原期間建立新的資料庫。
  * 目錄讀取：讀取備份目錄。
  * SAP_INTERNAL_HANA_SUPPORT：存取數個私人資料表。
* 此指令碼會將金鑰新增至 AZUREWLBACKUPHANAUSER 的 **hdbuserstore**，供 HANA 備份外掛程式處理所有作業 (資料庫查詢、還原作業、設定和執行備份)。

>[!NOTE]
> 您可以明確傳遞列於[必要條件](#prerequisites)中的使用者金鑰，以作為預先註冊指令碼的參數：`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>若要了解指令碼可接受的其他參數，請使用命令 `bash msawb-plugin-config-com-sap-hana.sh --help`

若要確認金鑰是否已建立，請使用 SIDADM 認證在 HANA 電腦上執行 HDBSQL 命令：

```hdbsql
hdbuserstore list
```

命令輸出應該會顯示 {SID}{DBNAME} 金鑰，且使用者會顯示為 AZUREWLBACKUPHANAUSER。

>[!NOTE]
> 請確定您在 `/usr/sap/{SID}/home/.hdb/` 下有一組唯一的 SSFS 檔案。 此路徑中應該只有一個資料夾。

## <a name="create-a-recovery-service-vault"></a>建立復原服務保存庫

復原服務保存庫是一個實體，可儲存一段時間以來建立的備份和復原點。 復原服務保存庫也包含與受保護虛擬機器相關聯的備份原則。

若要建立復原服務保存庫：

1. 在 [Azure 入口網站](https://portal.azure.com/)中登入您的訂用帳戶。

2. 在左側功能表上，選取 [所有服務]

   ![選取所有服務](./media/tutorial-backup-sap-hana-db/all-services.png)

3. 在 [所有服務]  對話方塊中，輸入 **Recovery Services**。 資源清單會根據您的輸入進行篩選。 在資源清單中，選取 [復原服務保存庫]。

   ![選取 [復原服務保存庫]](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. 在 [復原服務保存庫] 儀表板上，選取 [新增]。

   ![新增復原服務保存庫](./media/tutorial-backup-sap-hana-db/add-vault.png)

   [復原服務保存庫] 對話方塊隨即開啟。 提供 [名稱]、[訂用帳戶]、[資源群組] 和 [位置] 的值

   ![建立復原服務保存庫](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**：名稱會用來識別復原服務保存庫，且對於 Azure 訂用帳戶必須是唯一的。 指定的名稱至少要有兩個字元，但不能超過 50 個字元。 名稱開頭必須是字母，且只能包含字母、數字和連字號。 在本教學課程中，我們使用名稱 **SAPHanaVault**。
   * 訂用帳戶：選擇要使用的訂用帳戶。 如果您是唯一一個訂用帳戶的成員，就會看到該名稱。 如果您不確定要使用哪個訂用帳戶，請使用預設 (建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。 在此，我們使用了 **SAP HANA 解決方案實驗室訂用帳戶**訂用帳戶。
   * **資源群組**：使用現有的資源群組或建立新群組。 在此，我們使用了 **SAPHANADemo**。<br>
   若要查看您訂用帳戶中可用的資源群組清單，請選取 [使用現有的]﹐然後從下拉式清單方塊中選取資源。 若要建立新的資源群組，請選取 [新建]，然後輸入名稱。 如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/management/overview.md)。
   * **位置**：選取保存庫的地理區域。 保存庫必須與執行 SAP HANA 的虛擬機器位於相同的區域中。 我們使用**美國東部 2**。

5. 選取 [檢閱 + 建立]。

   ![選取 [檢閱並建立]](./media/tutorial-backup-sap-hana-db/review-create.png)

此時會建立復原服務保存庫。

## <a name="discover-the-databases"></a>探索資料庫

1. 在保存庫的 [使用者入門] 中，按一下 [備份]。 在 [工作負載的執行位置？] 中，選取 [Azure VM 中的 SAP HANA]。
2. 按一下 [開始探索]。 這會對保存庫區域中未受保護的 Linux VM 起始探索。 您會看到您想要保護的 Azure VM。
3. 在 [選取虛擬機器] 中按一下連結，以下載提供權限讓 Azure 備份服務存取 SAP Hana VM 以進行資料庫探索的指令碼。
4. 在您要備份的 SAP HANA 資料庫裝載所在的 VM 上執行指令碼。
5. 在 VM 上執行指令碼後，在 [選取虛擬機器] 中選取 VM。 然後，按一下 [探索資料庫]。
6. Azure 備份會探索 VM 上的所有 SAP HANA 資料庫。 在探索期間，Azure 備份會向保存庫註冊 VM，並在 VM 上安裝延伸模組。 資料庫上不會安裝代理程式。

   ![探索資料庫](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>設定備份

在探索到要備份的資料庫後，接下來我們將啟用備份。

1. 按一下 [設定備份]。

   ![設定備份](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. 在 [選取要備份的項目] 中，選取一或多個要保護的資料庫，然後按一下 [確定]。

   ![選取要備份的項目](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. 在 [備份原則] > [選擇備份原則] 中，根據下一節中的指示，為資料庫建立新的備份原則。

   ![選擇備份原則](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. 在建立原則後，在 [備份] 功能表上按一下 [啟用備份]。

   ![按一下 [啟用備份]](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. 在入口網站的 [通知] 區域中，追蹤備份設定進度。

## <a name="creating-a-backup-policy"></a>建立備份原則

備份原則會定義備份的進行時間和備份的保留時間長度。

* 原則會建立於保存庫層級上。
* 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。

指定原則設定，如下所示：

1. 在 [原則名稱] 中，為新原則輸入名稱。 在此案例中，請輸入 **SAPHANA**。

   ![輸入新原則的名稱](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. 在 [完整備份原則] 中，選取 [備份頻率]。 您可以選擇 [每日] 或 [每週]。 在本教學課程中，我們選擇 [每日] 備份。

   ![選取備份頻率](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. 在 [保留範圍] 中，設定完整備份的保留期設定。
   * 依預設會選取所有選項。 請清除您不想要使用的任何保留範圍限制，並設定您要使用的限制。
   * 所有備份類型 (完整/差異/記錄) 的最小保留期間皆為七天。
   * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
   * 系統會根據每週保留範圍和設定，標記和保留特定日期的備份。
   * 每月和每年保留範圍會以類似方式運作。
4. 在 [完整備份原則] 功能表中，按一下 [確定] 以接受設定。
5. 然後，選取 [差異備份] 以新增差異原則。
6. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。 我們已啟用會在每**週日**的**凌晨 2:00** 執行的差異備份，此備份會保留 **30 天**。

   ![差異備份原則](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >目前不支援增量備份。
   >

7. 按一下 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。
8. 選取 [記錄備份]，以新增交易記錄備份原則。
   * [記錄備份] 會預設為 [啟用]。 此選項無法停用，因為 SAP HANA 管理所有的記錄備份。
   * 我們將備份排程設為 **2 小時**，並將保留期間設為 **15 天**。

    ![記錄備份原則](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > 只有在順利完成一個完整備份後，記錄備份才會開始運作。
   >

9. 按一下 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。
10. 在完成備份原則的定義後，按一下 [確定]。

現在，您已成功設定 SAP HANA 資料庫的備份。

## <a name="next-steps"></a>後續步驟

* 了解如何[在執行於 Azure VM 的 SAP HANA 資料庫上執行隨選備份](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* 了解如何[還原執行於 Azure VM 上的 SAP HANA 資料庫](sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](sap-hana-db-manage.md)
* 了解如何[對 SAP HANA 資料庫備份時的常見問題進行疑難排解](backup-azure-sap-hana-database-troubleshoot.md)
