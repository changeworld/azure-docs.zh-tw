---
title: 備份 Azure VM 中的 SQL Server 資料庫
description: 在本文中，您將瞭解如何使用 Azure 備份來備份 Azure 虛擬機器上的 SQL Server 資料庫。
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 16e24ed94d8017d9fb922193bb16a33ec7a9cdfd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84817539"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>備份 Azure VM 中的 SQL Server 資料庫

SQL Server 資料庫是需要低復原點目標 (RPO) 和長期保留的重要工作負載。 您可以使用 [Azure 備份](backup-overview.md)來備份在 Azure 虛擬機器 (VM) 上執行的 SQL Server 資料庫。

本文說明如何將執行於 Azure VM 上的 SQL Server 資料庫備份至 Azure 備份復原服務保存庫。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 建立和設定保存庫。
> * 探索資料庫，並設定備份。
> * 為資料庫設定自動保護。

>[!NOTE]
>**Azure VM 中的 SQL server 虛刪除和 Azure VM 工作負載中的 SAP Hana 虛刪除**現在已有預覽版。<br>
>若要註冊預覽版，請透過此位址發送電子郵件給我們：AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prerequisites

備份 SQL Server 資料庫之前，請先檢查下列條件：

1. 在與裝載 SQL Server 執行個體的 VM 相同的區域和訂用帳戶中識別或[建立](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)復原服務保存庫。
1. 確認 VM 具有[網路連線](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。
1. 確認 SQL Server 資料庫的命名符合 [Azure 備份的資料庫命名方針](#database-naming-guidelines-for-azure-backup)。
1. 請確定 Azure Resource Manager （ARM） Vm （或傳統 Vm 的77個字元）的 SQL Server VM 名稱和資源組名的組合長度不能超過84個字元。 之所以有此限制，是因為服務會保留某些字元。
1. 檢查您未針對資料庫啟用任何其他備份解決方案。 在備份資料庫前，請停用所有其他的SQL Server 備份。

> [!NOTE]
> 您可以同時為 Azure VM 以及在該 VM 上執行的SQL Server 資料庫啟用 Azure 備份，而不會發生任何衝突。

### <a name="establish-network-connectivity"></a>建立網路連線

在進行所有作業時，SQL Server VM 都需要連線到 Azure 備份服務、Azure 儲存體和 Azure Active Directory。 這可以藉由使用私人端點，或允許存取所需的公用 IP 位址或 FQDN 來達成。 若不允許正確連線到所需的 Azure 服務，可能會導致資料庫探索、設定備份、執行備份和還原資料等作業失敗。

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

1. 選取 [設定]**** 底下的 [輸出安全性規則]****。

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

當您在 Azure VM 上備份 SQL Server 資料庫時，VM 上的備份擴充功能會使用 HTTPS API 將管理命令傳送至 Azure 備份，並將資料傳送至 Azure 儲存體。 備份延伸模組也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 使用上述的 IP 和 FQDN 清單來允許存取所需的服務。 不支援已驗證的 Proxy 伺服器。

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure 備份的資料庫命名方針

避免在資料庫名稱中使用下列元素：

* 後置/前置空格
* 後置驚嘆號 (!)
* 右方括號 (])
* 分號 (;)
* 斜線 (/)

對於不支援的字元，可以使用別名，但建議避免採用此做法。 如需詳細資訊，請參閱 [了解表格服務資料模型](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)。

>[!NOTE]
>不支援在其名稱中使用特殊字元 (例如「+」或「&」) 資料庫的**設定保護**作業。 您可以變更資料庫名稱，或啟用**自動保護**以成功保護這些資料庫。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>探索 SQL Server 資料庫

如何探索在 VM 上執行的資料庫：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您用來備份資料庫的復原服務保存庫。

2. 在 [復原服務保存庫] 儀表板中，選取 [備份]。

   ![選取 [備份] 以開啟 [備份目標] 功能表](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在 [備份目標] 中，將 [工作負載的執行位置] 設為 [Azure]。

4. 在 [您要備份什麼?] 中，選取 [Azure VM 中 SQL Server]。

    ![選取 Azure VM 中的 SQL Server 來進行備份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. 在 [備份目標] > [探索 VM 中的 DB] 中，選取 [開始探索] 以搜尋訂用帳戶中未受保護的 VM。 搜尋可能需要一些時間，視訂用帳戶中未受保護的 VM 數目而定。

   * 在探索之後，未受保護的 VM 應該會出現在清單中，並依照名稱和資源群組列出。
   * 如果 VM 未如預期列出，請查看該 VM 是否已備份在保存庫中。
   * 多個 VM 可以有相同名稱，但會屬於不同的資源群組。

     ![在搜尋 VM 中的 DB 期間會將備份擱置](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 清單中，選取執行 SQL Server 資料庫的 VM，然後選取 [探索資料庫]。

7. 在 [通知] 中追蹤資料庫探索進度。 此動作所需的時間取決於 VM 資料庫的數目。 探索到選取的資料庫之後，即會出現成功訊息。

    ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure 備份會探索 VM 上的所有 SQL Server 資料庫。 在探索期間，會在背景中執行下列元素：

    * Azure 備份向用來備份工作負載的保存庫註冊 VM。 已註冊 VM 上的所有資料庫都只能備份至此保存庫。
    * Azure Backup 在 VM 上安裝 AzureBackupWindowsWorkload 擴充功能。 SQL 資料庫上不會安裝代理程式。
    * Azure Backup 在 VM 上建立服務帳戶 NT Service\AzureWLBackupPluginSvc。
      * 所有備份和還原作業都會使用此服務帳戶。
      * NT Service\AzureWLBackupPluginSvc 需要 SQL 系統管理員權限。 在 Marketplace 中建立的所有 SQL Server VM 都會預先安裝 SqlIaaSExtension。 AzureBackupWindowsWorkload 延伸模組會使用 SQLIaaSExtension 來自動取得必要的權限。
    * 如果您尚未從 Marketplace 建立 VM，或者，如果您使用 SQL 2008 和 2008 R2，則 VM 不會安裝 SqlIaaSExtension，因此探索作業將會失敗，並顯示錯誤訊息 UserErrorSQLNoSysAdminMembership。 若要修正此問題，請遵循[設定 VM 權限](backup-azure-sql-database.md#set-vm-permissions)底下的指示。

        ![選取 VM 和資料庫](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>設定備份  

1. 在 [備份目標] >  **[步驟 2：設定備份]** 中，選取 [設定備份]。

   ![選取 [設定備份]](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 在 [選取要備份的項目]中，您會看到所有已登錄的可用性群組和獨立 SQ LServer 執行個體。 選取資料列左邊的箭號，以展開該執行個體或 Always On 可用性群組中所有未受保護的資料庫清單。  

    ![顯示所有具備獨立資料庫的 SQL Server 執行個體](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 選擇您要保護的資料庫，然後選取 [確定]。

   ![保護資料庫](./media/backup-azure-sql-database/select-database-to-protect.png)

   為了將備份負載最佳化，Azure 備份會將每個備份作業的資料庫數目上限設為 50。

     * 若要保護 50 個以上的資料庫，請設定多個備份。
     * 若要[啟用](#enable-auto-protection)整個執行個體或 Always On 可用性群組，請在 [自動保護] 下拉式清單中，選取 [開啟]，然後選取 [確定]。

    > [!NOTE]
    > [自動保護](#enable-auto-protection)功能不僅可一次性地在所有現有的資料庫上啟用保護，也會自動保護新增至該執行個體或可用性群組的任何新資料庫。  

4. 選取 [確定] 開啟 [備份原則]。

    ![在 Always On 可用性群組上啟用自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

5. 在 [備份原則] 中，選擇原則，然後選取 [確定]。

   * 選取 HourlyLogBackup 的預設原則。
   * 選擇之前為 SQL 建立的現有備份原則。
   * 根據 RPO 和保留範圍來定義新的原則。

     ![選取備份原則](./media/backup-azure-sql-database/select-backup-policy.png)

6. 在 [備份] 中，選取 [啟用備份]。

    ![啟用所選備份原則](./media/backup-azure-sql-database/enable-backup-button.png)

7. 在入口網站的 [通知] 區域中，追蹤設定進度。

    ![[通知] 區域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>建立備份原則

備份原則會定義備份的進行時間和備份的保留時間長度。

* 原則會建立於保存庫層級上。
* 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。
* 建立備份原則時，預設值是每日完整備份。
* 您可以新增差異備份，但僅限於將完整備份設定為每週進行時。
* 瞭解[不同類型的備份原則](backup-architecture.md#sql-server-backup-types)。

若要建立備份原則：

1. 在保存庫中，選取 [備份原則] > [新增]。
2. 在 [新增] 中，選取 [Azure VM 中的 SQL Server] 來定義原則類型。

   ![針對新備份原則選擇原則類型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在 [原則名稱] 中，為新原則輸入名稱。
4. 在 [完整備份原則] 中，選取 [備份頻率]。 選擇 [每日] 或 [每週]。

   * 若選擇 [每日]，請選取備份作業的開始時間和時區。
   * 若選擇 [每週]，請選取備份作業的開始日期 (星期幾)、時間和時區。
   * 執行完整備份，因為無法關閉 [完整備份] 選項。
   * 選取 [完整備份] 以檢視原則。
   * 您無法為每日完整備份建立差異備份。

     ![新的備份原則欄位](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 在 [保留範圍] 中，依預設會選取所有選項。 請清除任何保留範圍限制，然後設定要使用的間隔。

    * 所有備份類型 (完整、差異和記錄) 的最小保留期間為七天。
    * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    * 系統會根據每週保留範圍和每週保留設定，標記和保留特定日期的備份。
    * 每月和每年保留範圍會以類似方式運作。

       ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)

6. 在 [完整備份原則] 功能表中，選取 [確定] 接受設定。
7. 若要新增差異備份原則，請選取 [差異備份]。

   ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![開啟差異備份原則功能表](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。

    * 您每天僅可以觸發一次差異備份。
    * 差異備份最多可以保留 180 天。 如需較長的保留期，請使用完整備份。

9. 選取 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。

10. 若要新增交易記錄備份原則，請選取 [記錄備份]。
11. 在 [記錄備份] 中選取 [啟用]，然後設定頻率和保留控制項。 記錄備份可以每隔 15 分鐘頻繁地出現，而且最多可以保留 35 天。
12. 選取 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。

    ![編輯記錄備份原則](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在 [備份原則] 功能表上，選擇是否要啟用 [SQL 備份壓縮]。 此選項預設為停用。 若已啟用，SQL Server 會將壓縮的備份串流處理至 VDI。  請注意，Azure 備份會根據此控制項的值，以 COMPRESSION / NO_COMPRESSION 子句覆寫執行個體層級預設值。

14. 完成備份原則的編輯之後，請選取 [確定]。

> [!NOTE]
> 每個記錄備份都會連結到先前的完整備份，以形成復原鏈結。 系統會保留此完整備份，直到最後一個記錄備份的保留期到期為止。 這可能表示完整備份會保留一段額外的時間，以確保可以復原所有記錄。 假設使用者已設定每週進行完整備份、每日進行差異備份和每 2 小時進行記錄備份。 所有這些項目都會保留 30 天。 但是，只有在下一次的完整備份可以使用之後 (也就是 30 + 7 天之後)，才能真正清除/刪除每週的完整備份。 比方說，每週的完整備份會在 11 月 16 日執行。 根據保留原則，它應該保留到12月16日為止。 此完整備份的最後一個記錄備份會在下一次排定的完整備份 (也就是 11 月 22 日) 之前進行。 直到 12 月 22 日，也就是此記錄備份到期之前，您都無法刪除 11 月 16 日的完整備份。 因此，11 月 16 日的完整備份會保留到 12 月 22 日。

## <a name="enable-auto-protection"></a>啟用自動保護  

您可以啟用自動保護，將所有現有和未來的資料庫自動備份至獨立 SQL Server 執行個體或 Always On 可用性群組。

* 您一次可以選取自動保護的資料庫數目沒有限制。 探索通常會每隔八小時執行一次。 不過，如果您藉由選取 [重新探索**db** ] 選項來手動執行探索，則可以立即探索並保護新的資料庫。
* 啟用自動保護時，您無法選擇保護或排除執行個體中的資料庫不受保護。
* 如果您的執行個體已包含一些受保護的資料庫，即使在您開啟自動保護之後，這些資料庫仍會在其各自的原則之下受到保護。 稍後新增的所有未受保護資料庫，只會具有在啟用自動保護時所定義的單一原則 (列在 [設定備份] 底下)。 不過，您可以在稍後變更與自動保護資料庫建立關聯的原則。  

若要啟用自動保護：

  1. 在 [要備份的項目] 中，選取要啟用自動保護的執行個體。
  2. 選取 [自動保護] 下的下拉式清單，選擇 [開啟]，然後選取 [確定]。

      ![在可用性群組上啟用自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 所有資料庫會一起設定備份，且您可在 [備份作業] 中加以追蹤。

如果您要停用自動保護，請選取 [設定備份] 下方的執行個體名稱，然後針對該執行個體選取 [停用自動保護]。 所有資料庫都會繼續備份，但未來的資料庫將不會自動受到保護。

![在該執行個體上停用自動保護](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>後續步驟

了解如何：

* [還原 SQL Server 資料庫備份](restore-sql-database-azure-vm.md)
* [管理 SQL Server 資料庫備份](manage-monitor-sql-database-backup.md)
