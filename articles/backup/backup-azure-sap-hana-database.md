---
title: 使用 Azure 備份將 SAP Hana 資料庫備份至 Azure
description: 在本文中，您將了解如何使用 Azure 備份服務，將 SAP Hana 資料庫備份至 Azure 虛擬機器。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 4183c1eca6b1149c5c61ed77c0ca1101c86f8f4f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745419"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>將 SAP Hana 資料庫備份到 Azure VM

SAP Hana 資料庫是需要低復原點目標 (RPO) 和長期保留的重要工作負載。 您可以使用 [Azure 備份](backup-overview.md)來備份在 Azure 虛擬機器 (VM) 上執行的 SAP Hana 資料庫。

本文將說明如何將執行於 Azure VM 上的 SAP Hana 資料庫備份至 Azure 備份復原服務保存庫。

在本文中，您將了解如何：
> [!div class="checklist"]
>
> * 建立和設定保存庫
> * 探索資料庫
> * 設定備份
> * 執行隨選備份作業

>[!NOTE]
>**Azure VM 中的 SQL server 虛刪除和 Azure VM 工作負載中的 SAP Hana 虛刪除**現在已有預覽版。<br>
>若要註冊預覽版，請透過此位址發送電子郵件給我們：AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prerequisites

請參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預先註冊指令碼功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)區段來設定用於備份的資料庫。

### <a name="establish-network-connectivity"></a>建立網路連線

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

#### <a name="private-endpoints"></a>私人端點

私人端點可讓您從虛擬網路內的伺服器安全地連線到您的復原服務保存庫。 私人端點會針對您的保存庫使用 VNET 位址空間中的 IP。 您在虛擬網路內的資源與保存庫之間的網路流量，會透過您的虛擬網路和 Microsoft 骨幹網路上的私人連結來傳輸。 這可避免資料在公用網際網路暴露。 請在[這裡](https://docs.microsoft.com/azure/backup/private-endpoints)詳讀 Azure 備份的私人端點詳細資料。

#### <a name="nsg-tags"></a>NSG 標籤

如果您使用網路安全性群組 (NSG)，請使用 AzureBackup 服務標籤，以允許對 Azure 備份進行輸出存取。 除了 Azure 備份標籤之外，您還需要為 Azure AD 和 Azure 儲存體建立類似的 [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 規則，以允許用於驗證和資料傳輸的連線。  下列步驟說明建立 Azure 備份標籤規則的程序：

1. 在 [所有服務] 中，移至 [網路安全性群組]，然後選取網路安全性群組。

1. 選取 [設定] 底下的 [輸出安全性規則]。

1. 選取 [新增]。 輸入可供用於建立新規則的所有必要詳細資料，如[安全性規則設定](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 請確定 [目的地] 選項已設定為 [服務標籤]，且 [目的地服務標籤] 已設定為 [AzureBackup]。

1. 按一下 [新增] 以儲存新建立的輸出安全性規則。

同樣地，您也可以建立 Azure 儲存體和 Azure AD 的 NSG 輸出安全性規則。

#### <a name="azure-firewall-tags"></a>Azure 防火牆標籤

如果您使用的是 Azure 防火牆，請使用 [AzureBackup] 的 [Azure 防火牆標籤](https://docs.microsoft.com/azure/firewall/fqdn-tags)來建立應用程式規則。 這會允許對 Azure 備份進行的所有輸出存取。

#### <a name="allow-access-to-service-ip-ranges"></a>允許存取服務 IP 範圍

如果您選擇允許存取服務 IP，請在[此處](https://www.microsoft.com/download/confirmation.aspx?id=56519)參閱 JSON 檔案中可用的 IP 範圍。 您必須允許存取對應至 Azure 備份、Azure 儲存體和 Azure Active Directory 的 IP。

#### <a name="allow-access-to-service-fqdns"></a>允許存取服務 FQDN

您也可以使用下列 FQDN 來允許從伺服器存取所需的服務：

| 服務    | 要存取的網域名稱                             |
| -------------- | ------------------------------------------------------------ |
| Azure 備份  | `*.backup.windowsazure.com`                             |
| Azure 儲存體 | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | 請遵循[本文](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)中第 56 和第 59 節來允許存取 FQDN |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>使用 HTTP Proxy 伺服器以路由流量

當您備份執行於 Azure VM 上的 SAP Hana 資料庫時，VM 上的備份擴充功能會使用 HTTPS API 將管理命令傳送至 Azure 備份，並將資料傳送至 Azure 儲存體。 備份延伸模組也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 使用上述的 IP 和 FQDN 清單來允許存取所需的服務。 不支援已驗證的 Proxy 伺服器。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>探索資料庫

1. 在保存庫的 [使用者入門] 中，按一下 [備份]。 在 [工作負載的執行位置？] 中，選取 [Azure VM 中的 SAP HANA]。
2. 按一下 [開始探索]。 這會對保存庫區域中未受保護的 Linux VM 起始探索。

   * 進行探索之後，未受保護的 VM 會出現在入口網站中，並依照名稱和資源群組列出。
   * 如果 VM 未如預期列出，請檢查該 VM 是否已備份在保存庫中。
   * 多個 VM 可以有相同名稱，但會屬於不同的資源群組。

3. 在 [選取虛擬機器] 中按一下連結，以下載提供權限讓 Azure 備份服務存取 SAP Hana VM 以進行資料庫探索的指令碼。
4. 在每個裝載 SAP Hana 資料庫以進行備份的 VM 上執行指令碼。
5. 在 VM 上執行指令碼後，在 [選取虛擬機器] 中選取 VM。 然後，按一下 [探索資料庫]。
6. Azure 備份會探索 VM 上的所有 SAP HANA 資料庫。 在探索期間，Azure 備份會向保存庫註冊 VM，並在 VM 上安裝延伸模組。 資料庫上不會安裝代理程式。

    ![探索 SAP Hana 資料庫](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>設定備份  

現在，啟用備份。

1. 在步驟 2 中，按一下 [設定備份]。

    ![設定備份](./media/backup-azure-sap-hana-database/configure-backup.png)
2. 在 [選取要備份的項目] 中，選取您想要保護的所有資料庫 > [確定]。

    ![選取要備份的項目](./media/backup-azure-sap-hana-database/select-items.png)
3. 在 [備份原則] > [選擇備份原則] 中，根據下方指示，為資料庫建立新的備份原則。

    ![選擇備份原則](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 在建立原則後，在 [備份] 功能表上按一下 [啟用備份]。

    ![啟用備份](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 在入口網站的 [通知] 區域中，追蹤備份設定進度。

### <a name="create-a-backup-policy"></a>建立備份原則

備份原則會定義備份的進行時間和備份的保留時間長度。

* 原則會建立於保存庫層級上。
* 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。

>[!NOTE]
>在 Azure VM 中執行 SAP Hana 資料庫備份時，Azure 備份不會自動調整日光節約時間變更。
>
>請視需要手動修改原則。

指定原則設定，如下所示：

1. 在 [原則名稱] 中，為新原則輸入名稱。

   ![輸入原則名稱](./media/backup-azure-sap-hana-database/policy-name.png)
2. 在 [完整備份原則] 中選取 [備份頻率]，然後選擇 [每日] 或 [每週]。
   * **每日**：選取備份作業的開始時間和時區。
       * 您必須執行完整備份。 您無法關閉此選項。
       * 按一下 [完整備份] 以檢視原則。
       * 您無法為每日完整備份建立差異備份。
   * **每週**：選取備份作業的執行日期 (星期幾)、時間和時區。

   ![選取備份頻率](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. 在 [保留範圍] 中，設定完整備份的保留期設定。
    * 依預設會選取所有選項。 請清除您不想要使用的任何保留範圍限制，並設定您要使用的限制。
    * 所有備份類型 (完整/差異/記錄) 的最小保留期間皆為七天。
    * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    * 系統會根據每週保留範圍和設定，標記和保留特定日期的備份。
    * 每月和每年保留範圍會以類似方式運作。

4. 在 [完整備份原則] 功能表中，按一下 [確定] 以接受設定。
5. 選取 [差異備份] 以新增差異原則。
6. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。
    * 您每天最多可以觸發一次差異備份。
    * 差異備份最多可以保留 180 天。 如果您需要保留更久，則必須使用完整備份。

    ![差異備份原則](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 目前不支援增量備份。

7. 按一下 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。
8. 選取 [記錄備份]，以新增交易記錄備份原則。
    * 在 [記錄備份] 中，選取 [啟用]。  此選項無法停用，因為 SAP HANA 管理所有的記錄備份。
    * 設定頻率和保留控制。

    > [!NOTE]
    > 只有在順利完成一個完整備份後，記錄備份才會開始運作。

9. 按一下 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。
10. 在完成備份原則的定義後，按一下 [確定]。

> [!NOTE]
> 每個記錄備份都會連結到先前的完整備份，以形成復原鏈。 系統會保留此完整備份，直到最後一個記錄備份的保留期到期為止。 這可能表示完整備份會保留一段額外的時間，以確保可以復原所有記錄。 假設使用者已設定每週進行完整備份、每日進行差異備份和每 2 小時進行記錄備份。 所有這些項目都會保留 30 天。 但是，只有在下一次的完整備份可以使用之後 (也就是 30 + 7 天之後)，才能真正清除/刪除每週的完整備份。 比方說，每週的完整備份會在 11 月 16 日執行。 根據保留原則，其應該保留到 12 月 16 日為止。 此完整備份的最後一個記錄備份會在下一次排定的完整備份 (也就是 11 月 22 日) 之前進行。 直到 12 月 22 日，也就是此記錄備份到期之前，您都無法刪除 11 月 16 日的完整備份。 因此，11 月 16 日的完整備份會保留到 12 月 22 日。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

備份會根據原則排程執行。 您可以視需要執行備份，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [備份項目] 中，選取執行 SAP Hana 資料庫的 VM，然後按一下 [立即備份]。
3. 在 [立即備份] 中，使用行事曆控制項來選取復原點應該保留的最後一天。 然後按一下 [確定] 。
4. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 根據您的資料庫大小，建立初始備份可能需要花一點時間。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在已啟用 Azure 備份的資料庫上執行 SAP Hana Studio 備份

如果您想要針對使用 Azure 備份來備份的資料庫，取得本機備份 (使用 HANA Studio)，請執行下列動作：

1. 等待資料庫的完整或記錄備份完成。 檢查 SAP HANA Studio / Cockpit 中的狀態。
2. 停用記錄備份，並將備份類別目錄設定為相關資料庫的檔案系統。
3. 若要這麼做，請按兩下 [systemdb] >  > [選取資料庫] > [篩選 (記錄)]。
4. 將 **enable_auto_log_backup** 設定為 [否]。
5. 將 **log_backup_using_backint** 設定為 [False]。
6. 對資料庫進行隨選完整備份。
7. 等待完整備份和目錄備份完成。
8. 將先前的設定還原回 Azure 的設定：
    * 將 **enable_auto_log_backup** 設定為 [是]。
    * 將 **log_backup_using_backint** 設定為 [True]。

## <a name="next-steps"></a>後續步驟

* 了解如何[還原執行於 Azure VM 上的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 備份進行備份的 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
