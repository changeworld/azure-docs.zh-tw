---
title: 備份 Azure VM 中的 SQL Server 資料庫
description: 在本文中，瞭解如何使用 Azure 備份在 Azure 虛擬機器上備份 SQL Server 資料庫。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5b10907738feeecbec06669175e82578f2915f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273328"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>備份 Azure VM 中的 SQL Server 資料庫

SQL Server 資料庫是需要低復原點目標 （RPO） 和長期保留的關鍵工作負載。 可以使用[Azure 備份](backup-overview.md)來備份在 Azure 虛擬機器 （VM） 上運行的 SQL Server 資料庫。

本文演示如何將在 Azure VM 上運行的 SQL Server 資料庫備份到 Azure 備份恢復服務保存庫。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 建立和設定保存庫。
> * 發現資料庫並設置備份。
> * 為資料庫設定自動保護。

>[!NOTE]
>**Azure VM 中的 SQL 伺服器虛刪除和 Azure VM 工作負荷中 SAP HANA 的虛刪除**現在在預覽版中可用。<br>
>要註冊預覽版，請寫信給我們：AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Prerequisites

在備份 SQL Server 資料庫之前，請檢查以下條件：

1. 標識或與託管 SQL Server 實例的 VM 位於同一區域中的[恢復服務保存庫](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)和訂閱。
2. 驗證 VM 是否具有[網路連接](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。
3. 確保 SQL Server 資料庫遵循[Azure 備份 的資料庫命名準則](#database-naming-guidelines-for-azure-backup)。
4. 檢查您是否未為資料庫啟用任何其他備份解決方案。 在備份資料庫之前禁用所有其他 SQL Server 備份。

> [!NOTE]
> 您可以為 Azure VM 以及運行在 VM 上的 SQL Server 資料庫啟用 Azure 備份，而不會發生衝突。

### <a name="establish-network-connectivity"></a>建立網路連線

對於所有操作，SQL Server VM 都需要連接到 Azure 公共 IP 位址。 若未連線至 Azure 公用 IP 位址，VM 作業 (資料庫探索、設定備份、排程備份、還原復原點等) 將會失敗。

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

**部署 HTTP Proxy 伺服器以路由流量**。 備份 Azure VM 上的 SQL Server 資料庫時，VM 上的備份擴展程式使用 HTTPS API 將管理命令發送到 Azure 備份和資料到 Azure 存儲。 備份延伸模組也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 Azure 備份沒有萬用字元域要添加到代理規則的允許清單中。 對於 Azure 提供的這些服務，您需要使用公共 IP 範圍。 延伸模組是唯一為了要存取公用網際網路而設定的元件。

連線能力選項包含下列優缺點：

**選項** | **優點** | **缺點**
--- | --- | ---
允許 IP 範圍 | 沒有額外的成本 | 由於 IP 位址範圍會隨著時間改變，因此難以管理 <br/><br/> 提供整個 Azure 的存取權，而不只是 Azure 儲存體的存取權
使用 NSG 服務標籤 | 會自動合併範圍變更，因此更容易管理 <br/><br/> 沒有額外的成本 <br/><br/> | 只能搭配 NSG 使用 <br/><br/> 提供整個服務的存取權
使用 Azure 防火牆 FQDN 標籤 | 會自動管理所需的 FQDN，因此更容易管理 | 只能搭配 Azure 防火牆使用
使用 HTTP Proxy | VM 的單一網際網路存取點 <br/> | 使用 Proxy 軟體執行 VM 時的額外成本 <br/> 沒有已發佈的 FQDN 位址，允許規則將受 Azure IP 位址更改的約束

#### <a name="private-endpoints"></a>專用終結點

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure 備份的資料庫命名指南

避免在資料庫名稱中使用以下元素：

* 尾隨和領先空間
* 尾隨驚嘆號 （！）
* 右括弧 （*）
* 分號';'
* 向前斜杠 "/"

別名可用於不支援的字元，但我們建議避免它們。 如需詳細資訊，請參閱 [了解表格服務資料模型](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)。

>[!NOTE]
>不支援對名稱中具有特殊字元（如"+"或"&"）的資料庫進行**配置保護**操作。 您可以更改資料庫名稱或啟用**自動保護**，從而成功保護這些資料庫。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>探索 SQL Server 資料庫

如何發現在 VM 上運行的資料庫：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您用來備份資料庫的復原服務保存庫。

2. 在**恢復服務保存庫**儀表板中，選擇 **"備份**"。

   ![選取 [備份] 以開啟 [備份目標] 功能表](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在 **"備份目標**"中，設置**工作負荷在何處運行？** **Azure**

4. 在 [您要備份什麼?]**** 中，選取 [Azure VM 中 SQL Server]****。

    ![選取 Azure VM 中的 SQL Server 來進行備份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. 在 **"備份目標** > **發現 VM"中**，選擇 **"開始發現"** 以搜索訂閱中未受保護的 VM。 此搜索可能需要一段時間，具體取決於訂閱中未受保護的 VM 的數量。

   * 在探索之後，未受保護的 VM 應該會出現在清單中，並依照名稱和資源群組列出。
   * 如果 VM 未按預期列出，請參閱它是否已在保存庫中備份。
   * 多個 VM 可以具有相同的名稱，但它們將屬於不同的資源組。

     ![在搜尋 VM 中的 DB 期間會將備份擱置](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 清單中，選取執行 SQL Server 資料庫的 VM，然後選取 [探索資料庫]****。

7. 在**通知**中跟蹤資料庫發現。 此操作所需的時間取決於 VM 資料庫的數量。 探索到選取的資料庫之後，即會出現成功訊息。

    ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure 備份會探索 VM 上的所有 SQL Server 資料庫。 在發現期間，以下元素出現在後臺：

    * Azure 備份將 VM 註冊到保存庫以進行工作負載備份。 只能備份到此保存庫上已註冊 VM 上的所有資料庫。
    * Azure Backup 在 VM 上安裝 AzureBackupWindowsWorkload 擴充功能。 SQL 資料庫上未安裝代理。
    * Azure Backup 在 VM 上建立服務帳戶 NT Service\AzureWLBackupPluginSvc。
      * 所有備份和還原作業都會使用此服務帳戶。
      * NT 服務_AzureWL 備份外掛程式Svc 需要 SQL sysadmin 許可權。 在應用商店中創建的所有 SQL Server VM 都附帶安裝了 SqlIaaS 擴展。 AzureBackupWindowsWorkload 延伸模組會使用 SQLIaaSExtension 來自動取得必要的權限。
    * 如果您沒有從應用商店創建 VM，或者您在 SQL 2008 和 2008 R2 上，則 VM 可能沒有安裝 SqlIaaS 擴展，並且發現操作在錯誤訊息 UserErrorSQLNoSysAdmins 時失敗。 要解決此問題，請按照["設置 VM 許可權"](backup-azure-sql-database.md#set-vm-permissions)下的說明操作。

        ![選取 VM 和資料庫](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>設定備份  

1. 在**備份目標** > **步驟 2：配置備份**中，選擇 **"配置備份**"。

   ![選取 [設定備份]](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 在 **"選擇要備份的專案**"中，您將看到所有已註冊的可用性組和獨立 SQL Server 實例。 選擇行左側的箭頭以展開該實例中所有未受保護的資料庫的清單或"始終打開可用性"組。  

    ![顯示所有具備獨立資料庫的 SQL Server 執行個體](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 選擇要保護的所有資料庫，然後選擇 **"確定**"。

   ![保護資料庫](./media/backup-azure-sql-database/select-database-to-protect.png)

   為了將備份負載最佳化，Azure 備份會將每個備份作業的資料庫數目上限設為 50。

     * 若要保護 50 個以上的資料庫，請設定多個備份。
     * 要[啟用](#enable-auto-protection)整個實例或"始終打開的可用性"組，請在 **"AUTOPROTECT"** 下拉清單中選擇"**打開**"，然後選擇 **"確定**"。

    > [!NOTE]
    > [自動保護](#enable-auto-protection)功能不僅能夠同時保護所有現有資料庫，還可以自動保護添加到該實例或可用性組的任何新資料庫。  

4. 選擇 **"確定"** 以打開**備份策略**。

    ![為"始終打開的可用性"組啟用自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

5. 在**備份策略**中，選擇策略，然後選擇 **"確定**"。

   * 選擇預設策略作為每小時記錄備份。
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
* 瞭解[不同類型的備份策略](backup-architecture.md#sql-server-backup-types)。

若要建立備份原則：

1. 在保存庫中，選擇 **"添加備份策略** > **添加**"。
2. 在 **"添加**"**中**，在 Azure VM 中選擇 SQL Server 以定義策略類型。

   ![針對新備份原則選擇原則類型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在 [原則名稱]**** 中，為新原則輸入名稱。
4. 在 [完整備份原則]**** 中，選取 [備份頻率]****。 選擇**每日**或**每週**。

   * 若選擇 [每日]****，請選取備份作業的開始時間和時區。
   * 若選擇 [每週]****，請選取備份作業的開始日期 (星期幾)、時間和時區。
   * 運行完整備份，因為無法關閉 **"完整備份"** 選項。
   * 選擇 **"完全備份**"以查看策略。
   * 您無法為每日完整備份建立差異備份。

     ![新的備份原則欄位](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 在**保留範圍內**，預設情況下選擇所有選項。 清除不需要的任何保留範圍限制，然後設置要使用的間隔。

    * 任何類型的備份（完整備份、差分和日誌）的最短保留期為 7 天。
    * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    * 根據每週保留範圍和每週保留設置標記和保留特定一天的備份。
    * 每月和每年的保留範圍以類似方式進行。

       ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)

6. 在 [完整備份原則]**** 功能表中，選取 [確定]**** 接受設定。
7. 若要新增差異備份原則，請選取 [差異備份]****。

   ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![開啟差異備份原則功能表](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在 [差異備份原則]**** 中，選取 [啟用]**** 以開啟頻率和保留控制項。

    * 您每天只能觸發一個差分備份。
    * 差異備份最多可以保留 180 天。 要延長保留時間，請使用完整備份。

9. 選取 [確定]**** 以儲存原則，然後返回主要 [備份原則]**** 功能表。

10. 若要新增交易記錄備份原則，請選取 [記錄備份]****。
11. 在 [記錄備份]**** 中選取 [啟用]****，然後設定頻率和保留控制項。 記錄備份可以每隔 15 分鐘進行一次，並且可以保留長達 35 天。
12. 選取 [確定]**** 以儲存原則，然後返回主要 [備份原則]**** 功能表。

    ![編輯記錄備份原則](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在 **"備份策略"** 功能表上，選擇是否啟用**SQL 備份壓縮**。 預設會停用這個選項。 如果啟用，SQL Server 將向 VDI 發送壓縮備份流。  請注意，Azure 備份會根據此控制項的值，使用壓縮/NO_COMPRESSION子句覆蓋實例級別預設值。

14. 完成備份原則的編輯之後，請選取 [確定]****。

> [!NOTE]
> 每個記錄備份都連結到以前的完整備份，以形成恢復鏈。 此完整備份將保留，直到上次記錄備份的保留過期。 這可能意味著完整備份將保留一段額外的時間，以確保可以恢復所有日誌。 假設使用者每週有一個完整備份、每日差分和 2 小時日誌。 所有保留時間為30天。 但是，每週滿可以真正清理/刪除後，下一個完整備份可用，即，30 + 7 天后。 假設，每週一次的完整備份發生在 11 月 16 日。 根據保留政策，應保留至 12 月 16 日。 此完整的最後一個記錄備份發生在 11 月 22 日下一個計畫的完全之前。 在此日誌可用到 12 月 22 日之前，無法刪除 11 月 16 日的滿。 因此，11月16日的滿滿將保留到12月22日。

## <a name="enable-auto-protection"></a>啟用自動保護  

您可以啟用自動保護，以自動將所有現有和將來的資料庫備份到獨立的 SQL Server 實例或"始終打開"可用性組。

* 一次可以選擇自動保護的資料庫數量沒有限制。
* 在啟用自動保護時，無法有選擇地保護或排除資料庫中在實例中免受保護。
* 如果您的實例已經包含一些受保護的資料庫，則即使在您打開自動保護後，它們仍將受其各自策略的保護。 以後添加的所有未受保護的資料庫將只有您在啟用自動保護時定義的單個策略，該策略列在 **"配置備份**"下。 但是，您可以稍後更改與自動保護資料庫關聯的策略。  

要啟用自動保護：

  1. 在 [要備份的項目]**** 中，選取要啟用自動保護的執行個體。
  2. 選擇 **"自動保護**"下的下拉清單，選擇 **"打開**"，然後選擇 **"確定**"。

      ![在可用性組啟用自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 所有資料庫會一起設定備份，且您可在 [備份作業]**** 中加以追蹤。

如果需要禁用自動保護，請在 **"配置備份**"下選擇實例名稱，然後選擇實例的 **"禁用自動保護**"。 將繼續備份所有資料庫，但將來的資料庫不會自動保護。

![禁用該實例上的自動保護](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>後續步驟

了解如何：

* [還原備份的 SQL Server 資料庫](restore-sql-database-azure-vm.md)
* [管理備份的 SQL Server 資料庫](manage-monitor-sql-database-backup.md)
