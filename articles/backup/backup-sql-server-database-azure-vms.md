---
title: 備份 Azure VM 中的 SQL Server 資料庫
description: 在本文中，您將瞭解如何使用 Azure 備份來備份 Azure 虛擬機器上的 SQL Server 資料庫。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 887f15deed74330cf132e0574d166c074d2c7cad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685720"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>備份 Azure VM 中的 SQL Server 資料庫

SQL Server 資料庫是需要低復原點目標（RPO）和長期保留的重要工作負載。 您可以使用[Azure 備份](backup-overview.md)來備份在 Azure 虛擬機器（vm）上執行的 SQL Server 資料庫。

本文說明如何將在 Azure VM 上執行的 SQL Server 資料庫備份至 Azure 備份復原服務保存庫。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 建立和設定保存庫。
> * 探索資料庫並設定備份。
> * 為資料庫設定自動保護。

>[!NOTE]
>**AZURE vm 中的 SQL server 虛刪除和 AZURE vm 工作負載中的 SAP Hana 虛刪除**現已提供預覽。<br>
>若要註冊預覽版，請于前寫信給我們AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>先決條件

在備份 SQL Server 資料庫之前，請先檢查下列準則：

1. 在與裝載 SQL Server 實例的 VM 相同的區域和訂用帳戶中，識別或建立復原[服務保存庫](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)。
2. 確認 VM 具有網路連線[能力](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。
3. 請確定 SQL Server 資料庫遵循[Azure 備份的資料庫命名指導方針](#database-naming-guidelines-for-azure-backup)。
4. 請確認您沒有針對資料庫啟用的任何其他備份解決方案。 在備份資料庫之前，請先停用所有其他 SQL Server 備份。

> [!NOTE]
> 您可以啟用 Azure VM 的 Azure 備份，以及在 VM 上執行的 SQL Server 資料庫，而不會發生衝突。

### <a name="establish-network-connectivity"></a>建立網路連線

針對所有作業，SQL Server VM 需要連線到 Azure 公用 IP 位址。 若未連線至 Azure 公用 IP 位址，VM 作業 (資料庫探索、設定備份、排程備份、還原復原點等) 將會失敗。

請使用下列其中一個選項來建立連線：

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>允許 Azure 資料中心 IP 範圍

此選項會允許所下載檔案中的 [IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 若要存取網路安全性群組 (NSG)，請使用 Set-AzureNetworkSecurityRule Cmdlet。 如果安全收件者清單只包含區域專屬的 IP，您還必須更新安全收件者清單 Azure Active Directory (Azure AD) 服務標籤以啟用驗證。

#### <a name="allow-access-using-nsg-tags"></a>允許使用 NSG 標籤來存取

如果您使用 NSG 來限制連線，則請使用 AzureBackup 服務標籤來允許針對 Azure 備份進行輸出存取。 此外，您也應該使用 Azure AD 和 Azure 儲存體的[規則](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，來允許驗證和資料傳輸使用連線能力。 這可以從 Azure 入口網站或透過 PowerShell 來進行。

若要使用入口網站建立規則：

  1. 在 [所有服務]**** 中，移至 [網路安全性群組]****，然後選取網路安全性群組。
  2. 選取 [設定]**** 底下的 [輸出安全性規則]****。
  3. 選取 [新增]  。 輸入可供用於建立新規則的所有必要詳細資料，如[安全性規則設定](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 請確定 [目的地]**** 選項已設定為 [服務標籤]****，且 [目的地服務標籤]**** 已設定為 [AzureBackup]****。
  4. 按一下 [新增]**** 以儲存新建立的輸出安全性規則。

若要使用 PowerShell 建立規則：

 1. 新增 Azure 帳戶認證並更新國家雲端<br/>
      `Add-AzureRmAccount`<br/>

 2. 選取 NSG 訂用帳戶<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. 選取 NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. 新增 Azure 備份服務標籤的允許輸出規則<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. 新增儲存體服務標籤的允許輸出規則<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. 新增 AzureActiveDirectory 服務標籤的允許輸出規則<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. 儲存 NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**允許使用 Azure 防火牆標籤來存取**。 如果您使用的是 Azure 防火牆，請使用 AzureBackup [FQDN 標籤](https://docs.microsoft.com/azure/firewall/fqdn-tags)來建立應用程式規則。 這會允許針對 Azure 備份進行輸出存取。

**部署 HTTP Proxy 伺服器以路由流量**。 當您備份 Azure VM 上的 SQL Server 資料庫時，VM 上的備份擴充功能會使用 HTTPS Api 將管理命令傳送至 Azure 備份和資料以 Azure 儲存體。 備份延伸模組也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 沒有萬用字元網域與 Azure 備份搭配使用，以新增至您的 proxy 規則允許清單。 您必須將公用 IP 範圍用於 Azure 所提供的這些服務。 延伸模組是唯一為了要存取公用網際網路而設定的元件。

連線能力選項包含下列優缺點：

**選項** | **優點** | **缺點**
--- | --- | ---
允許 IP 範圍 | 沒有額外的成本 | 由於 IP 位址範圍會隨著時間改變，因此難以管理 <br/><br/> 提供整個 Azure 的存取權，而不只是 Azure 儲存體的存取權
使用 NSG 服務標籤 | 會自動合併範圍變更，因此更容易管理 <br/><br/> 沒有額外的成本 <br/><br/> | 只能搭配 NSG 使用 <br/><br/> 提供整個服務的存取權
使用 Azure 防火牆 FQDN 標籤 | 會自動管理所需的 FQDN，因此更容易管理 | 只能搭配 Azure 防火牆使用
使用 HTTP Proxy | VM 的單一網際網路存取點 <br/> | 使用 Proxy 軟體執行 VM 時的額外成本 <br/> 沒有已發佈的 FQDN 位址，允許規則將受限於 Azure IP 位址變更

#### <a name="private-endpoints"></a>私人端點

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure 備份的資料庫命名方針

避免在資料庫名稱中使用下列元素：

* 尾端和開頭空格
* 尾端驚嘆號（！）
* 右方括弧（]）
* 分號 '; '
* 正斜線 '/'

別名適用于不支援的字元，但我們建議您避免使用它們。 如需詳細資訊，請參閱 [了解表格服務資料模型](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)。

>[!NOTE]
>不支援在其名稱中使用特殊字元（例如 "+" 或 "&"）的資料庫**設定保護**作業。 您可以變更資料庫名稱或啟用**自動保護**，這樣可以成功保護這些資料庫。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>探索 SQL Server 資料庫

如何探索在 VM 上執行的資料庫：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您用來備份資料庫的復原服務保存庫。

2. 在復原**服務保存庫**儀表板中，選取 [**備份**]。

   ![選取 [備份] 以開啟 [備份目標] 功能表](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在 [**備份目標**] 中，將**工作負載**的執行位置設定為**Azure**。

4. 在 [您要備份什麼?]**** 中，選取 [Azure VM 中 SQL Server]****。

    ![選取 Azure VM 中的 SQL Server 來進行備份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. 在 [**備份目標** > ] 中**探索 vm 中的 db**，選取 [**開始探索**] 以搜尋訂用帳戶中未受保護的 vm。 這項搜尋可能需要一些時間，視訂用帳戶中未受保護的 Vm 數目而定。

   * 在探索之後，未受保護的 VM 應該會出現在清單中，並依照名稱和資源群組列出。
   * 如果 VM 未如預期般列出，請查看它是否已在保存庫中備份。
   * 多個 Vm 可以有相同的名稱，但它們會屬於不同的資源群組。

     ![在搜尋 VM 中的 DB 期間會將備份擱置](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 清單中，選取執行 SQL Server 資料庫的 VM，然後選取 [探索資料庫]****。

7. 在**通知**中追蹤資料庫探索。 此動作所需的時間取決於 VM 資料庫的數目。 探索到選取的資料庫之後，即會出現成功訊息。

    ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure 備份會探索 VM 上的所有 SQL Server 資料庫。 在探索期間，下列元素會在背景中發生：

    * Azure 備份會向保存庫註冊 VM 以進行工作負載備份。 已註冊 VM 上的所有資料庫只能備份至此保存庫。
    * Azure Backup 在 VM 上安裝 AzureBackupWindowsWorkload 擴充功能。 SQL 資料庫上未安裝任何代理程式。
    * Azure Backup 在 VM 上建立服務帳戶 NT Service\AzureWLBackupPluginSvc。
      * 所有備份和還原作業都會使用此服務帳戶。
      * NT Service\AzureWLBackupPluginSvc 需要 SQL 系統管理員許可權。 在 Marketplace 中建立的所有 SQL Server Vm 都隨附已安裝的 SqlIaaSExtension。 AzureBackupWindowsWorkload 延伸模組會使用 SQLIaaSExtension 來自動取得必要的權限。
    * 如果您沒有從 Marketplace 建立 VM，或如果您是在 SQL 2008 和 2008 R2 上，則 VM 可能未安裝 SqlIaaSExtension，且探索作業會失敗，並出現錯誤訊息 UserErrorSQLNoSysAdminMembership。 若要修正此問題，請遵循[設定 VM 許可權](backup-azure-sql-database.md#set-vm-permissions)底下的指示。

        ![選取 VM 和資料庫](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>設定備份  

1. 在 [**備份目標** > ]**步驟2： [設定備份**] 中，選取 [**設定備份**]。

   ![選取 [設定備份]](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 在 [**選取要備份的專案**] 中，您會看到所有已登錄的可用性群組和獨立 SQL Server 實例。 選取資料列左邊的箭號，以展開該實例或 Always On 可用性群組中所有未受保護的資料庫清單。  

    ![顯示所有具備獨立資料庫的 SQL Server 執行個體](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 選擇您想要保護的所有資料庫，然後選取 **[確定]**。

   ![保護資料庫](./media/backup-azure-sql-database/select-database-to-protect.png)

   為了將備份負載最佳化，Azure 備份會將每個備份作業的資料庫數目上限設為 50。

     * 若要保護 50 個以上的資料庫，請設定多個備份。
     * 若要[啟用](#enable-auto-protection)整個實例或 Always On 可用性群組，請在 [**自動保護**] 下拉式清單中，選取 [**開啟**]，然後選取 **[確定]**。

    > [!NOTE]
    > [自動保護](#enable-auto-protection)功能不僅會同時在所有現有的資料庫上啟用保護，也會自動保護新增至該實例或可用性群組的任何新資料庫。  

4. 選取 **[確定]** 以開啟 [**備份原則**]。

    ![啟用 Always On 可用性群組的自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

5. 在 [**備份原則**] 中，選擇原則，然後選取 **[確定]**。

   * 選取預設原則為 [HourlyLogBackup]。
   * 選擇之前為 SQL 建立的現有備份原則。
   * 根據 RPO 和保留範圍來定義新的原則。

     ![選取備份原則](./media/backup-azure-sql-database/select-backup-policy.png)

6. 在 [備份]**** 中，選取 [啟用備份]****。

    ![啟用所選備份原則](./media/backup-azure-sql-database/enable-backup-button.png)

7. 在入口網站的 [通知]**** 區域中，追蹤設定進度。

    ![[通知] 區域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>建立備份原則 

備份原則會定義備份的進行時間和備份的保留時間長度。

* 原則會建立於保存庫層級上。
* 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。
* 建立備份原則時，預設值是每日完整備份。
* 您可以新增差異備份，但僅限於將完整備份設定為每週進行時。
* 深入瞭解[不同類型的備份原則](backup-architecture.md#sql-server-backup-types)。

若要建立備份原則：

1. 在保存庫中，選取 [**備份原則** > ] [**新增**]。
2. 在 [**新增**] 中，選取 [ **Azure VM 中的 SQL Server** ] 以定義原則類型。

   ![針對新備份原則選擇原則類型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在 [原則名稱]**** 中，為新原則輸入名稱。
4. 在 [完整備份原則]**** 中，選取 [備份頻率]****。 選擇 [**每天**] 或 [**每週**]。

   * 若選擇 [每日]****，請選取備份作業的開始時間和時區。
   * 若選擇 [每週]****，請選取備份作業的開始日期 (星期幾)、時間和時區。
   * 執行完整備份，因為您無法關閉 [**完整備份**] 選項。
   * 選取 [**完整備份**] 以查看原則。
   * 您無法為每日完整備份建立差異備份。

     ![新的備份原則欄位](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 在 [**保留範圍**] 中，預設會選取所有選項。 清除您不想要的保留範圍限制，然後設定要使用的間隔。

    * 任何備份類型的最小保留期限（[完整]、[差異] 和 [記錄]）為7天。
    * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    * 特定日期的備份會根據每週保留範圍和每週保留期設定進行標記和保留。
    * 每月和每年保留範圍的運作方式類似。

       ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)

6. 在 [完整備份原則]**** 功能表中，選取 [確定]**** 接受設定。
7. 若要新增差異備份原則，請選取 [差異備份]****。

   ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![開啟差異備份原則功能表](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在 [差異備份原則]**** 中，選取 [啟用]**** 以開啟頻率和保留控制項。

    * 您每天只能觸發一次差異備份。
    * 差異備份最多可以保留 180 天。 如需較長的保留期，請使用完整備份。

9. 選取 [確定]**** 以儲存原則，然後返回主要 [備份原則]**** 功能表。

10. 若要新增交易記錄備份原則，請選取 [記錄備份]****。
11. 在 [記錄備份]**** 中選取 [啟用]****，然後設定頻率和保留控制項。 記錄備份會每隔15分鐘執行一次，而且最多可以保留35天。
12. 選取 [確定]**** 以儲存原則，然後返回主要 [備份原則]**** 功能表。

    ![編輯記錄備份原則](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在 [**備份原則**] 功能表上，選擇是否要啟用**SQL 備份壓縮**。 預設會停用這個選項。 若已啟用，SQL Server 會將壓縮的備份串流傳送到 VDI。  請注意，Azure 備份會根據此控制項的值，以 COMPRESSION/NO_COMPRESSION 子句覆寫實例層級預設值。

14. 完成備份原則的編輯之後，請選取 [確定]****。

> [!NOTE]
> 每個記錄備份都會連結到先前的完整備份，以形成復原鏈。 系統會保留此完整備份，直到最後一個記錄備份的保留期過期為止。 這可能表示完整備份會保留一段額外的時間，以確保可以復原所有記錄。 假設使用者有每週的完整備份、每日差異和2小時記錄。 所有這些專案都會保留30天。 但是，只有在下一次完整備份（也就是 30 + 7 天之後）可以使用之後，才能真正清除/刪除每週的完整功能。 比方說，每週的完整備份會在11月16日執行。 根據保留原則，它應該保留到12月16日為止。 此完整的最後一個記錄備份會在11月22日的下一次排定的完整時間之前進行。 直到 Dec 22 日為止，才可使用此記錄檔，但無法刪除11月16日完整版。 因此，11月16日的完整版會保留到 Dec 22 日。

## <a name="enable-auto-protection"></a>啟用自動保護  

您可以啟用自動保護，自動將所有現有和未來的資料庫備份到獨立 SQL Server 實例或 Always On 可用性群組。

* 您可以一次選取自動保護的資料庫數目沒有限制。
* 當您啟用自動保護時，您無法選擇性地保護或排除實例中的資料庫不受保護。
* 如果您的實例已包含一些受保護的資料庫，即使在您開啟自動保護之後，它們仍會在其各自的原則之下受到保護。 稍後新增的所有未受保護資料庫，只有在啟用自動保護時所定義的單一原則，會列在 [**設定備份**] 底下。 不過，您可以在稍後變更與自動保護資料庫相關聯的原則。  

若要啟用自動保護：

  1. 在 [要備份的項目]**** 中，選取要啟用自動保護的執行個體。
  2. 選取 [**自動保護**] 底下的下拉式清單，選擇 [**開啟**]，然後選取 **[確定]**。

      ![在可用性群組上啟用自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 所有資料庫會一起設定備份，且您可在 [備份作業]**** 中加以追蹤。

如果您需要停用自動保護，請在 [**設定備份**] 底下選取實例名稱，然後針對該實例選取 [**停用自動保護**]。 所有資料庫都會繼續備份，但未來的資料庫將不會自動受到保護。

![在該實例上停用自動保護](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>後續步驟

了解如何：

* [還原備份 SQL Server 資料庫](restore-sql-database-azure-vm.md)
* [管理備份的 SQL Server 資料庫](manage-monitor-sql-database-backup.md)
