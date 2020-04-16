---
title: 容解 SSIS 整合執行時管理
description: 本文為 SSIS 整合時 (SSIS IR) 的管理問題提供故障排除指南
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 0324044d93f12f6ac6ec96ff1a31be8ee02ada41
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414704"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>在 Azure 資料工廠中排除 SSIS 整合執行時管理故障

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文為 Azure-SQL 伺服器整合服務 (SSIS) 整合執行時 (IR) 中的管理問題(也稱為 SSIS IR)提供故障排除指南。

## <a name="overview"></a>概觀

如果在預配或取消預配 SSIS IR 時遇到任何問題,則會在 Microsoft Azure 數據工廠門戶中看到一條錯誤消息或從 PowerShell cmdlet 返回的錯誤。 錯誤始終以帶有詳細錯誤消息的錯誤代碼格式顯示。

如果錯誤代碼為內部伺服器錯誤,則服務有暫時性問題,您應該稍後重試該操作。 如果重試不起作用,請與 Azure 數據工廠支援團隊聯繫。

否則,三個主要的外部依賴項可能會導致錯誤:Azure SQL 資料庫伺服器或託管實例、自定義設置腳本和虛擬網路配置。

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL 資料庫伺服器或託管實例問題

如果您要使用 SSIS 目錄資料庫來佈建 SSIS IR，則需要 Azure SQL Database 伺服器或受控執行個體。 SSIS IR 必須要能夠存取 Azure SQL Database 伺服器或受控執行個體。 此外，Azure SQL Database 伺服器或受控執行個體的帳戶也應該要有可建立 SSIS 目錄資料庫 (SSISDB) 的權限。 如果發生錯誤，Data Factory 入口網站中會顯示錯誤碼與詳細的 SQL 例外狀況訊息。 請使用下列清單中的資訊來針對錯誤碼進行疑難排解。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

在佈建新的 SSIS IR 或 IR 正在執行時，您可能會看到此問題。 如果您在 IR 佈建期間遇到此錯誤，便可能會在錯誤訊息中獲得詳細的 SqlException 訊息，其內容會指出下列其中一個問題：

* 網路連線問題。 請檢查 SQL Server 或受控執行個體主機名稱是否可供存取。 也請確認沒有防火牆或網路安全性群組 (NSG) 封鎖 SSIS IR 對伺服器的存取。
* SQL 驗證期間登入失敗。 所提供的帳戶無法登入 SQL Server 資料庫。 請確定您提供的使用者帳戶是否正確。
* Microsoft Azure Active Directory (Azure AD) 驗證 (受控識別) 期間登入失敗。 將中心的受控識別新增至 AAD 群組，並確定受控識別具有目錄資料庫伺服器的存取權限。
* 連線逾時。 此錯誤一律是由安全性相關設定所造成的。 建議您：
  1. 創建新的 VM。
  1. 如果 IR 在虛擬網路中,則將 VM 加入同一的 Microsoft Azure IR 虛擬網路。
  1. 安裝 SSMS 並檢查 Azure SQL 資料庫伺服器或託管實例狀態。

若為其他問題，請修正詳細 SQL 例外狀況錯誤訊息中所顯示的問題。 如果您仍然遇到問題，請連絡 Azure SQL Database 伺服器或受控執行個體支援小組。

如果您在 IR 執行時看到錯誤，則可能是網路安全性群組或防火牆變更而導致 SSIS IR 背景工作角色節點無法存取 Azure SQL Database 伺服器或受控執行個體。 請將 SSIS IR 背景工作角色節點解除封鎖，使其可以存取 Azure SQL Database 伺服器或受控執行個體。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

以下是此類錯誤消息的外觀:「資料庫」SSISDB」已達到其大小配額。 分區或刪除數據、刪除索引或查閱文檔以尋求可能的解決方案。 

可能的解決方案包括：
* 增加 SSISDB 的配額大小。
* 變更 SSISDB 的設定來減少大小，方法如下：
   * 縮短保留期和專案版本數量。
   * 縮短日誌的保留期。
   * 更改日誌的預設等級。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

此錯誤表示 Azure SQL Database 伺服器或受控執行個體已經有 SSISDB，而且正由另一個 IR 使用中。 您必須提供不同的 Azure SQL Database 伺服器或受控執行個體，否則請刪除現有 SSISDB，然後重新啟動新的 IR。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

此錯誤可能會因為下列其中一個原因而發生：

* 針對 SSIS IR 設定的使用者帳戶沒有可建立資料庫的權限。 您可以對使用者授與建立資料庫的權限。
* 資料庫建立期間發生逾時，例如執行逾時或 DB 作業逾時。 請稍後再重試作業。 如果重試沒有用，請連絡 Azure SQL Database 伺服器或受控執行個體支援小組。

若為其他問題，請查看 SQL 例外狀況錯誤訊息，並修正錯誤詳細資料中所述的問題。 如果您仍然遇到問題，請連絡 Azure SQL Database 伺服器或受控執行個體支援小組。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

此類錯誤消息如下所示:"無效物件名稱'目錄.catalog_properties'"。在此情況下,您已經擁有名為 SSISDB 的資料庫,但它不是由 SSIS IR 創建的,或者資料庫處於無效狀態,該狀態由上次 SSIS IR 預配中的錯誤引起。 您可以使用名稱 SSISDB 來卸除現有資料庫，也可以為 IR 設定新的 Azure SQL Database 伺服器或受控執行個體。

## <a name="custom-setup-issues"></a>自訂設定問題

自訂設定所提供的介面可讓您自行新增在佈建或重新設定 SSIS IR 期間的設定步驟。 如需詳細資訊，請參閱[自訂 Azure-SSIS Integration Runtime 的設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

請確定您的容器只包含必要的自訂設定檔案；容器中的所有檔案都會下載到 SSIS IR 背景工作角色節點。 建議您在本機電腦上測試自訂設定指令碼，先修正任何指令碼執行問題，再於 SSIS IR 中執行指令碼。

SSIS IR 會定期更新，因此系統會在 IR 正在執行時檢查自訂設定指令碼容器。 此更新作業需要存取容器才能下載自訂設定指令碼並重新安裝。 此程序也會檢查容器是否可供存取，以及 main.cmd 檔案是否存在。

對於任何涉及自定義設置的錯誤,您將看到一個自定義設置文本失敗錯誤代碼,該代碼包含子代碼,如自定義設置腳本Blob容器不可存取或自定義設置腳本未找到。

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

此錯誤表示 SSIS IR 無法存取 Azure Blob 容器以取得自訂設定。 請確定容器的 SAS URI 可供連線且未過期。

如果 IR 正在執行，請將其停止，以新的自訂設定容器 SAS URI 重新設定 IR，然後重新啟動 IR。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

此錯誤表示 SSIS IR 無法在 Blob 容器中找到自訂設定指令碼 (main.cmd)。 請確定 main.cmd 存在於容器中，這是自訂設定安裝的進入點。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

此錯誤表示自訂設定指令碼 (main.cmd) 執行失敗。 請先在本機電腦上嘗試該指令碼，或檢查 Blob 容器上的自訂設定執行記錄。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

此錯誤表示自訂設定指令碼執行逾時。 請確定您的指令碼可以透過無訊息方式執行，而且不需要互動式輸入，並確定您的 Blob 容器只包含必要的自訂安裝檔。 建議您先在本機電腦上測試指令碼。 您也應該檢查 Blob 容器中的自訂設定執行記錄。 自訂設定的最大期限為 45 分鐘後才會逾時，而最長期限包含從容器下載所有檔案並安裝在 SSIS IR 上的時間。 如果您需要較長的期限，請提出支援票證。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

此錯誤表示將自訂設定執行記錄上傳至 Blob 容器的嘗試失敗。 此問題的發生原因可能是 SSIS IR 沒有寫入到 Blob 容器的權限，或是儲存體或網路有問題。 如果自訂設定成功，此錯誤並不會影響任何 SSIS 功能，但記錄將會遺失。 如果自訂設定失敗並發生其他錯誤，而且記錄未上傳，我們會先回報此錯誤，以便上傳記錄以供分析。 此外，在解決此問題之後，我們還會報告任何更具體的問題。 如果重試後未解決此問題，請連絡 Azure Data Factory 支援小組。

## <a name="virtual-network-configuration"></a>虛擬網路設定

當您將 SSIS IR 加入 Azure 虛擬網路時，SSIS IR 會使用位於使用者訂用帳戶下的虛擬網路。 如需詳細資訊，請參閱[將 Azure-SSIS Integration Runtime 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

發生虛擬網路相關的問題時，您會看到下列其中一個錯誤。

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

此錯誤可能會因為各種原因而發生。 若要進行疑難排解，請參閱[禁止](#forbidden)、[InvalidPropertyValue](#invalidpropertyvalue) 和 [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) 等節。

### <a name="forbidden"></a>禁止

此類錯誤可能類似於這種情況:「未為往來帳戶啟用子網Id。 Microsoft.Batch 資源提供程式未在 VNet 的同一訂閱下註冊。

這些詳細資料表示 Azure Batch 無法存取您的虛擬網路。 請將 Microsoft.Batch 資源提供者註冊到與虛擬網路相同的訂用帳戶下。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

這類錯誤可能會像下列其中之一： 

- 指定的 VNet 不存在,或者批處理服務無法訪問它。
- 指定的子網 xxx 不存在。

這些錯誤表示虛擬網路不存在、Azure Batch 服務無法存取該虛擬網路，或提供的子網路不存在。 請確定虛擬網路和子網路存在，而且 Azure Batch 可以存取兩者。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

此類錯誤消息可能如下所示:「在 VNet 中預配集成運行時失敗。 如果配置了 DNS 伺服器或 NSG 設置,請確保 DNS 伺服器可訪問且 NSG 配置正確。

在此情況下，您可能會有 DNS 伺服器或 NSG 設定的自訂設定，但這會導致系統無法解析或存取 SSIS IR 所需的 Azure 伺服器名稱。 如需詳細資訊，請參閱 [SSIS IR 虛擬網路設定](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果您仍然遇到問題，請連絡 Azure Data Factory 支援小組。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR 會定期自動更新。 升級期間會建立新的 Azure Batch 集區，並刪除舊的 Azure Batch 集區。 此外，系統還會刪除舊集區的虛擬網路相關資源，並在您的訂用帳戶下建立新的虛擬網路相關資源。 此錯誤表示在刪除舊集區的虛擬網路相關資源時失敗，原因是訂用帳戶或資源群組層級有刪除鎖定。 因為刪除鎖定是由客戶控制和設定的，所以在這種情況下，其必須移除刪除鎖定。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

如果 SSIS IR 佈建失敗，則系統會刪除所有已建立的資源。 不過，如果訂用帳戶或資源群組層級有資源刪除鎖定，則系統無法如預期地刪除虛擬網路資源。 若要修正此錯誤，請移除刪除鎖定，然後重新啟動 IR。

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

當您停止 SSIS IR 時，系統會刪除與虛擬網路相關的所有資源。 但如果訂用帳戶或資源群組層級有資源刪除鎖定，則刪除作業會失敗。 在此情況下，刪除鎖定同樣是由客戶控制和設定的。 因此，其必須移除刪除鎖定，然後再次停止 SSIS IR。

### <a name="nodeunavailable"></a>NodeUnavailable

當 IR 正在執行時會發生此錯誤，這表示 IR 已變得狀況不良。 造成此錯誤的原因一律是 DNS 伺服器或 NSG 設定有變更，因此讓 SSIS IR 無法連線至必要服務。 由於 DNS 伺服器和 NSG 的設定是由客戶控制的，因此客戶必須在其那一端修正執行問題。 如需詳細資訊，請參閱 [SSIS IR 虛擬網路設定](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果您仍然遇到問題，請連絡 Azure Data Factory 支援小組。

## <a name="static-public-ip-addresses-configuration"></a>靜態公共 IP 位址設定

當您將 Azure-SSIS IR 加入 Azure 虛擬網路時,還可以為 IR 帶來自己的靜態公共 IP 位址,以便 IR 可以存取限制對特定 IP 位址存取的資料來源。 如需詳細資訊，請參閱[將 Azure-SSIS Integration Runtime 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

除了上述虛擬網路問題外,您還可能會遇到與靜態公共 IP 位址相關的問題。 請檢查以下錯誤以尋求説明。

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>不合法的公共 IP 指定

啟動 Azure-SSIS IR 時,由於多種原因,可能會出現此錯誤:

| 錯誤訊息 | 解決方法|
|:--- |:--- |
| 提供的靜態公共 IP 位址已使用,請為 Azure-SSIS 整合時提供兩個未使用的位址。 | 應選擇兩個未使用的靜態公共 IP 位址或刪除對指定公共 IP 位址的目前引用,然後重新啟動 Azure-SSIS IR。 |
| 提供的靜態公共 IP 位址沒有 DNS 名稱,請為 Azure-SSIS 整合時提供其中兩個具有 DNS 名稱。 | 您可以在 Azure 門戶中設置公共 IP 位址的 DNS 名稱,如下圖所示。 具體步驟如下:(1) 打開 Azure 門戶並轉到此公共 IP 位址的資源頁;(2) 選擇 **「設定**」部分並設置 DNS 名稱,然後按下 **「儲存**」按鈕;(3) 重新啟動 Azure-SIS IR。 |
| 為 Azure-SSIS 整合執行時提供的 VNet 和靜態公共 IP 位址必須位於同一位置。 | 根據 Azure 網路的要求,靜態公共 IP 位址和虛擬網路應位於同一位置和訂閱中。 請提供兩個有效的靜態公共 IP 位址並重新啟動 Azure-SSIS IR。 |
| 提供的靜態公共 IP 位址是基本位址,請為 Azure-SSIS 整合時提供兩個標準位址。 | 有關説明[,請參閱公共 IP 位址的 SKU。](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>公共IP資源群組在啟動期間鎖定

如果 Azure-SSIS IR 預配失敗,則將刪除創建的所有資源。 但是,如果訂閱或資源組(包含靜態公共 IP 位址)級別存在資源刪除鎖,則網路資源不會按預期刪除。 要修復錯誤,請刪除刪除鎖並重新啟動 IR。

### <a name="publicipresourcegrouplockedduringstop"></a>公共 IP 資源群組在停止期間鎖定

停止 Azure-SSIS IR 時,包含公共 IP 位址的資源組中創建的所有網路資源都將被刪除。 但是,如果訂閱或資源組(包含靜態公共 IP 位址)級別存在資源刪除鎖,則刪除可能會失敗。 請刪除刪除鎖並重新啟動 IR。

### <a name="publicipresourcegrouplockedduringupgrade"></a>公共IP資源群組在升級期間鎖定

Azure-SSIS IR 會定期自動更新。 升級期間將創建新的 IR 節點,並將刪除舊節點。 此外,舊節點的創建網路資源(例如負載均衡器和網路安全組)將被刪除,並在訂閱下創建新的網路資源。 此錯誤意味著刪除舊節點的網路資源失敗,因為刪除鎖定在訂閱或資源組(其中包含您的靜態公共 IP 位址)級別。 請刪除刪除鎖,以便我們可以清理舊節點並釋放舊節點的靜態公共 IP 位址。 否則,無法釋放靜態公共 IP 位址,我們將無法進一步升級您的 IR。

### <a name="publicipnotusableduringupgrade"></a>升級期間公共 IP 無法使用

當您要自帶靜態公共 IP 位址時,應提供兩個公共 IP 位址。 其中一個節點將立即用於創建 IR 節點,另一個節點將在升級 IR 期間使用。 當其他公共 IP 位址在升級期間不可用時,可能會發生此錯誤。 有關可能的原因,請參閱[無效公共 IP 指定](#InvalidPublicIPSpecified)。