---
title: 使用 Azure 備份將 SAP HANA 資料庫備份到 Azure
description: 在本文中，瞭解如何使用 Azure 備份服務將 SAP HANA 資料庫備份到 Azure 虛擬機器。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248056"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>將 SAP Hana 資料庫備份到 Azure VM

SAP HANA 資料庫是需要低復原點目標 （RPO） 和長期保留的關鍵工作負載。 可以使用[Azure 備份](backup-overview.md)備份來備份在 Azure 虛擬機器 （VM） 上運行的 SAP HANA 資料庫。

本文演示如何將 Azure VM 上運行的 SAP HANA 資料庫備份到 Azure 備份恢復服務保存庫。

在本文中，您將了解如何：
> [!div class="checklist"]
>
> * 建立和設定保存庫
> * 探索資料庫
> * 設定備份
> * 運行按需備份作業

>[!NOTE]
>**Azure VM 中的 SQL 伺服器虛刪除和 Azure VM 工作負荷中 SAP HANA 的虛刪除**現在在預覽版中可用。<br>
>要註冊預覽版，請寫信給我們：AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prerequisites

請參閱[先決條件](tutorial-backup-sap-hana-db.md#prerequisites)和[預註冊腳本執行](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)哪些部分來設置資料庫進行備份。

### <a name="set-up-network-connectivity"></a>設定網路連線

在執行所有作業時，SAP HANA VM 都需要連線至 Azure 公用 IP 位址。 若未連線至 Azure 公用 IP 位址，VM 作業 (資料庫探索、設定備份、排程備份、還原復原點等) 將會失敗。

請使用下列其中一個選項來建立連線：

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>允許 Azure 資料中心 IP 範圍

此選項會允許所下載檔案中的 [IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 若要存取網路安全性群組 (NSG)，請使用 Set-AzureNetworkSecurityRule Cmdlet。 如果安全收件者清單只包含區域專屬的 IP，您還必須更新安全收件者清單 Azure Active Directory (Azure AD) 服務標籤以啟用驗證。

#### <a name="allow-access-using-nsg-tags"></a>允許使用 NSG 標籤來存取

如果您使用 NSG 來限制連線，則請使用 AzureBackup 服務標籤來允許針對 Azure 備份進行輸出存取。 此外，您也應該使用 Azure AD 和 Azure 儲存體的[規則](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，來允許驗證和資料傳輸使用連線能力。 這可以從 Azure 入口網站或透過 PowerShell 來進行。

若要使用入口網站建立規則：

  1. 在 [所有服務]**** 中，移至 [網路安全性群組]****，然後選取網路安全性群組。
  2. 選取 [設定]**** 底下的 [輸出安全性規則]****。
  3. 選取 [加入]****。 輸入可供用於建立新規則的所有必要詳細資料，如[安全性規則設定](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 請確定 [目的地]**** 選項已設定為 [服務標籤]****，且 [目的地服務標籤]**** 已設定為 [AzureBackup]****。
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

**部署 HTTP Proxy 伺服器以路由流量**。 當您在 Azure VM 上備份 SAP HANA 資料庫時，VM 上的備份擴充功能會使用 HTTPS API 將管理命令傳送至 Azure 備份，並將資料傳送至 Azure 儲存體。 備份延伸模組也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 延伸模組是唯一為了要存取公用網際網路而設定的元件。

連線能力選項包含下列優缺點：

**選項** | **優點** | **缺點**
--- | --- | ---
允許 IP 範圍 | 沒有額外的成本 | 由於 IP 位址範圍會隨著時間改變，因此難以管理 <br/><br/> 提供整個 Azure 的存取權，而不只是 Azure 儲存體的存取權
使用 NSG 服務標籤 | 會自動合併範圍變更，因此更容易管理 <br/><br/> 沒有額外的成本 <br/><br/> | 只能搭配 NSG 使用 <br/><br/> 提供整個服務的存取權
使用 Azure 防火牆 FQDN 標籤 | 會自動管理所需的 FQDN，因此更容易管理 | 只能搭配 Azure 防火牆使用
使用 HTTP Proxy | 允許在 Proxy 中精確控制儲存體 URL <br/><br/> VM 的單一網際網路存取點 <br/><br/> 不會隨著 Azure IP 位址變更 | 使用 Proxy 軟體執行 VM 時的額外成本

#### <a name="private-endpoints"></a>專用終結點

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>探索資料庫

1. 在保存庫的 [使用者入門]**** 中，按一下 [備份]****。 在 [工作負載的執行位置？]**** 中，選取 [Azure VM 中的 SAP HANA]****。
2. 按一下 [開始探索]****。 這會對保存庫區域中未受保護的 Linux VM 起始探索。

   * 發現後，未受保護的 VM 將顯示在門戶中，按名稱和資源組列出。
   * 如果 VM 未按預期列出，請檢查 VM 是否已在保存庫中備份。
   * 多個 VM 可以具有相同的名稱，但它們屬於不同的資源組。

3. 在 [選取虛擬機器]**** 中按一下連結，以下載提供權限讓 Azure 備份服務存取 SAP Hana VM 以進行資料庫探索的指令碼。
4. 在託管要備份的 SAP HANA 資料庫的每個 VM 上運行腳本。
5. 在 VM 上運行腳本後，在 **"選擇虛擬機器"** 中，選擇 VM。 然後，按一下 [探索資料庫]****。
6. Azure 備份會探索 VM 上的所有 SAP HANA 資料庫。 在探索期間，Azure 備份會向保存庫註冊 VM，並在 VM 上安裝延伸模組。 資料庫上不會安裝代理程式。

    ![發現 SAP HANA 資料庫](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>設定備份  

現在啟用備份。

1. 在步驟 2 中，按一下 **"配置備份**"。

    ![設定備份](./media/backup-azure-sap-hana-database/configure-backup.png)
2. 在 **"選擇要備份的專案"** 中，選擇要保護的所有資料庫>**確定**。

    ![選取要備份的項目](./media/backup-azure-sap-hana-database/select-items.png)
3. 在**備份策略** > **選擇備份策略**中，根據以下說明為資料庫創建新的備份策略。

    ![選擇備份原則](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 創建策略後，在 **"備份"** 功能表上按一下"**啟用備份**"。

    ![啟用備份](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 在入口網站的 [通知]**** 區域中，追蹤備份設定進度。

### <a name="create-a-backup-policy"></a>建立備份原則 

備份原則會定義備份的進行時間和備份的保留時間長度。

* 原則會建立於保存庫層級上。
* 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。

指定原則設定，如下所示：

1. 在 [原則名稱]**** 中，為新原則輸入名稱。

   ![輸入策略名稱](./media/backup-azure-sap-hana-database/policy-name.png)
2. 在 [完整備份原則]**** 中選取 [備份頻率]****，然後選擇 [每日]**** 或 [每週]****。
   * **每日**：選擇備份作業開始的小時和時區。
       * 您必須運行完整的備份。 不能關閉此選項。
       * 按一下 [完整備份]**** 以檢視原則。
       * 您無法為每日完整備份建立差異備份。
   * **每週**：選擇運行備份作業的星期數、小時和時區。

   ![選擇備份頻率](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. 在 [保留範圍]**** 中，設定完整備份的保留期設定。
    * 預設情況下，所有選項都被選中。 清除不想使用的任何保留範圍限制，並設置您所做的限制。
    * 所有備份類型 (完整/差異/記錄) 的最小保留期間皆為七天。
    * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    * 系統會根據每週保留範圍和設定，標記和保留特定日期的備份。
    * 每月和每年保留範圍會以類似方式運作。

4. 在 [完整備份原則]**** 功能表中，按一下 [確定]**** 以接受設定。
5. 選擇**差異備份**以添加差異策略。
6. 在 [差異備份原則]**** 中，選取 [啟用]**** 以開啟頻率和保留控制項。
    * 您每天最多可以觸發一次差異備份。
    * 差異備份最多可以保留 180 天。 如果您需要保留更久，則必須使用完整備份。

    ![差異備份原則](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 目前不支援增量備份。

7. 按一下 [確定]**** 以儲存原則，然後返回主要 [備份原則]**** 功能表。
8. 選取 [記錄備份]****，以新增交易記錄備份原則。
    * 在**記錄備份**中，選擇**啟用**。  此選項無法停用，因為 SAP HANA 管理所有的記錄備份。
    * 設置頻率和保留控制。

    > [!NOTE]
    > 記錄備份僅在成功完成完整備份後才開始流動。

9. 按一下 [確定]**** 以儲存原則，然後返回主要 [備份原則]**** 功能表。
10. 在完成備份原則的定義後，按一下 [確定]****。

> [!NOTE]
> 每個記錄備份都連結到以前的完整備份，以形成恢復鏈。 此完整備份將保留，直到上次記錄備份的保留過期。 這可能意味著完整備份將保留一段額外的時間，以確保可以恢復所有日誌。 假設使用者每週有一個完整備份、每日差分和 2 小時日誌。 所有保留時間為30天。 但是，每週滿可以真正清理/刪除後，下一個完整備份可用，即，30 + 7 天后。 假設，每週一次的完整備份發生在 11 月 16 日。 根據保留政策，應保留至 12 月 16 日。 此完整的最後一個記錄備份發生在 11 月 22 日下一個計畫的完全之前。 在此日誌可用到 12 月 22 日之前，無法刪除 11 月 16 日的滿。 因此，11月16日的滿滿將保留到12月22日。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

備份根據策略計畫運行。 您可以按需運行備份，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]****。
2. 在**備份項**中，選擇運行 SAP HANA 資料庫的 VM，然後按一下 **"立即備份**"。
3. 在 **"立即備份"** 中，使用日曆控制項選擇應保留復原點的最後一天。 然後按一下 **[確定]**。
4. 監視入口網站通知。 您可以在保存庫儀表板>**正在進行的備份作業** > **中**監視作業進度。 根據資料庫的大小，創建初始備份可能需要一段時間。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在啟用 Azure 備份的資料庫上運行 SAP HANA 工作室備份

如果要獲取使用 Azure 備份的資料庫的本地備份（使用 HANA Studio），請執行以下操作：

1. 等待資料庫完成的任何完整備份或記錄備份。 檢查 SAP HANA 工作室 /駕駛艙中的狀態。
2. 禁用記錄備份，並將備份目錄設置為相關資料庫的檔案系統。
3. 為此，按兩下**系統db** > **配置** > **選擇資料庫** > **篩選器（日誌）。**
4. **enable_auto_log_backup**設置為 **"否**"。
5. **將log_backup_using_backint**設置為**false**。
6. 按需對資料庫進行完整備份。
7. 等待完整備份和目錄備份完成。
8. 將以前的設置還原到 Azure 的設置：
    * **將enable_auto_log_backup**設置為 **"是**"。
    * **將log_backup_using_backint**設置為 **"真**"。

## <a name="next-steps"></a>後續步驟

* 了解如何[還原執行於 Azure VM 上的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 瞭解如何[管理使用 Azure 備份備份的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
