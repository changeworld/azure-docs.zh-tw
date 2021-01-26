---
title: 針對 SSIS Integration Runtime 管理進行疑難排解
description: '本文提供 SSIS Integration Runtime (SSIS IR 之管理問題的疑難排解指引) '
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
ms.openlocfilehash: 34fd595a03f67201b303c94764668bf86a6c244b
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796904"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>針對 Azure Data Factory 中的 SSIS Integration Runtime 管理進行疑難排解

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文提供 Azure 中管理問題的疑難排解指引-SQL Server Integration Services (SSIS) Integration Runtime (IR) ，也稱為 SSIS IR。

## <a name="overview"></a>概觀

如果您在布建或取消布建 SSIS IR 時遇到任何問題，您會在 Microsoft Azure Data Factory 入口網站或 PowerShell Cmdlet 傳回的錯誤中看到錯誤訊息。 錯誤一律會以錯誤碼的格式顯示，並提供詳細的錯誤訊息。

如果錯誤碼是 InternalServerError，則服務會發生暫時性問題，您應該稍後再重試此作業。 如果重試沒有説明，請聯絡 Azure Data Factory 支援小組。

否則，有三個主要的外部相依性可能導致錯誤： Azure SQL Database 或 Azure SQL 受控執行個體、自訂安裝腳本和虛擬網路設定。

## <a name="sql-database-or-sql-managed-instance-issues"></a>SQL Database 或 SQL 受控執行個體問題

如果您要使用 SSIS 目錄資料庫布建 SSIS IR，則需要 SQL Database 或 SQL 受控執行個體。 SSIS IR 必須能夠存取 SQL Database 或 SQL 受控執行個體。 此外，SQL Database 或 SQL 受控執行個體的登入帳戶必須擁有建立 SSIS 目錄資料庫 (SSISDB) 的許可權。 如果發生錯誤，Data Factory 入口網站中會顯示錯誤碼與詳細的 SQL 例外狀況訊息。 請使用下列清單中的資訊來針對錯誤碼進行疑難排解。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

在佈建新的 SSIS IR 或 IR 正在執行時，您可能會看到此問題。 如果您在 IR 佈建期間遇到此錯誤，便可能會在錯誤訊息中獲得詳細的 SqlException 訊息，其內容會指出下列其中一個問題：

* 網路連線問題。 檢查 SQL Database 或 SQL 受控執行個體的主機名稱是否可供存取。 也請確認沒有防火牆或網路安全性群組 (NSG) 封鎖 SSIS IR 對伺服器的存取。
* SQL 驗證期間登入失敗。 所提供的帳戶無法登入 SQL Server 資料庫。 請確定您提供的使用者帳戶是否正確。
* Microsoft Azure Active Directory (Azure AD) 驗證 (受控識別) 期間登入失敗。 將中心的受控識別新增至 AAD 群組，並確定受控識別具有目錄資料庫伺服器的存取權限。
* 連線逾時。 此錯誤一律是由安全性相關設定所造成的。 建議您：
  1. 建立新的 VM。
  1. 如果 IR 位於虛擬網路中，請將 VM 加入相同的 IR Microsoft Azure 虛擬網路。
  1. 安裝 SSMS，並檢查 SQL Database 或 SQL 受控執行個體狀態。

若為其他問題，請修正詳細 SQL 例外狀況錯誤訊息中所顯示的問題。 如果您仍然遇到問題，請洽詢 SQL Database 或 SQL 受控執行個體支援小組。

如果您在 IR 執行時看到錯誤，則網路安全性群組或防火牆變更可能會導致 SSIS IR 背景工作角色節點無法存取 SQL Database 或 SQL 受控執行個體。 解除封鎖 SSIS IR 背景工作角色節點，讓它可以存取 SQL Database 或 SQL 受控執行個體。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

以下是這類錯誤訊息的外觀：「資料庫 ' SSISDB ' 已達到大小配額。 分割或刪除資料、卸載索引，或參閱檔以取得可能的解決方法。」 

可能的解決方案包括：
* 增加 SSISDB 的配額大小。
* 變更 SSISDB 的設定來減少大小，方法如下：
   * 減少保留期限和專案版本數目。
   * 減少記錄檔的保留期間。
   * 變更記錄檔的預設層級。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

這項錯誤表示 SQL Database 或 SQL 受控執行個體已經有 SSISDB，而另一個 IR 正在使用它。 您必須提供不同的 SQL Database 或 SQL 受控執行個體，否則請刪除現有的 SSISDB，然後重新開機新的 IR。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

此錯誤可能會因為下列其中一個原因而發生：

* 針對 SSIS IR 設定的使用者帳戶沒有可建立資料庫的權限。 您可以對使用者授與建立資料庫的權限。
* 資料庫建立期間發生逾時，例如執行逾時或 DB 作業逾時。 請稍後再重試作業。 如果重試無法運作，請聯絡 SQL Database 或 SQL 受控執行個體支援小組。

若為其他問題，請查看 SQL 例外狀況錯誤訊息，並修正錯誤詳細資料中所述的問題。 如果您仍然遇到問題，請洽詢 SQL Database 或 SQL 受控執行個體支援小組。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

這類錯誤訊息看起來像這樣：「不正確物件名稱 ' catalog.catalog_properties '」。在這種情況下，您已經有一個名為 SSISDB 但未由 SSIS IR 建立的資料庫，或資料庫處於在上一次 SSIS IR 布建中的錯誤所造成的無效狀態。 您可以使用名稱 SSISDB 來卸載現有的資料庫，也可以為 IR 設定新的 SQL Database 或 SQL 受控執行個體。

## <a name="custom-setup-issues"></a>自訂安裝問題

自訂設定所提供的介面可讓您自行新增在佈建或重新設定 SSIS IR 期間的設定步驟。 如需詳細資訊，請參閱[自訂 Azure-SSIS Integration Runtime 的設定](./how-to-configure-azure-ssis-ir-custom-setup.md)。

請確定您的容器只包含必要的自訂設定檔案；容器中的所有檔案都會下載到 SSIS IR 背景工作角色節點。 建議您在本機電腦上測試自訂設定指令碼，先修正任何指令碼執行問題，再於 SSIS IR 中執行指令碼。

SSIS IR 會定期更新，因此系統會在 IR 正在執行時檢查自訂設定指令碼容器。 此更新作業需要存取容器才能下載自訂設定指令碼並重新安裝。 此程序也會檢查容器是否可供存取，以及 main.cmd 檔案是否存在。

對於任何涉及自訂安裝的錯誤，您會看到 CustomSetupScriptFailure 錯誤碼，其中包含 CustomSetupScriptBlobContainerInaccessible 或 CustomSetupScriptNotFound 等副程式代碼。

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

當您將 SSIS IR 加入 Azure 虛擬網路時，SSIS IR 會使用位於使用者訂用帳戶下的虛擬網路。 如需詳細資訊，請參閱[將 Azure-SSIS Integration Runtime 加入虛擬網路](./join-azure-ssis-integration-runtime-virtual-network.md)。
SSIS IR 順利啟動之後，如果遇到網路連線問題，您可以嘗試使用診斷連線 [工具](ssis-integration-runtime-diagnose-connectivity-faq.md) 來自行診斷問題。
發生虛擬網路相關的問題時，您會看到下列其中一個錯誤。

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

此錯誤可能會因為各種原因而發生。 若要進行疑難排解，請參閱[禁止](#forbidden)、[InvalidPropertyValue](#invalidpropertyvalue) 和 [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) 等節。

### <a name="forbidden"></a>禁止

這類錯誤可能會像這樣：「目前的帳戶未啟用 SubnetId。 Microsoft.Batch 資源提供者未在 VNet 的相同訂用帳戶下註冊。」

這些詳細資料表示 Azure Batch 無法存取您的虛擬網路。 請將 Microsoft.Batch 資源提供者註冊到與虛擬網路相同的訂用帳戶下。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

這類錯誤可能會像下列其中之一： 

- 「指定的 VNet 不存在，或 Batch 服務沒有其存取權。」
- 「指定的子網 xxx 不存在。」

這些錯誤表示虛擬網路不存在、Azure Batch 服務無法存取該虛擬網路，或提供的子網路不存在。 請確定虛擬網路和子網路存在，而且 Azure Batch 可以存取兩者。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

這類錯誤訊息可能看起來像這樣：「無法在 VNet 中布建 Integration Runtime。 如果已設定 DNS 伺服器或 NSG 設定，請確定 DNS 伺服器可供存取，且 NSG 已正確設定。」

在此情況下，您可能會有 DNS 伺服器或 NSG 設定的自訂設定，但這會導致系統無法解析或存取 SSIS IR 所需的 Azure 伺服器名稱。 如需詳細資訊，請參閱 [SSIS IR 虛擬網路設定](./join-azure-ssis-integration-runtime-virtual-network.md)。 如果您仍然遇到問題，請連絡 Azure Data Factory 支援小組。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR 會定期自動更新。 升級期間會建立新的 Azure Batch 集區，並刪除舊的 Azure Batch 集區。 此外，系統還會刪除舊集區的虛擬網路相關資源，並在您的訂用帳戶下建立新的虛擬網路相關資源。 此錯誤表示在刪除舊集區的虛擬網路相關資源時失敗，原因是訂用帳戶或資源群組層級有刪除鎖定。 因為刪除鎖定是由客戶控制和設定的，所以在這種情況下，其必須移除刪除鎖定。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

如果 SSIS IR 佈建失敗，則系統會刪除所有已建立的資源。 不過，如果訂用帳戶或資源群組層級有資源刪除鎖定，則系統無法如預期地刪除虛擬網路資源。 若要修正此錯誤，請移除刪除鎖定，然後重新啟動 IR。

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

當您停止 SSIS IR 時，系統會刪除與虛擬網路相關的所有資源。 但如果訂用帳戶或資源群組層級有資源刪除鎖定，則刪除作業會失敗。 在此情況下，刪除鎖定同樣是由客戶控制和設定的。 因此，其必須移除刪除鎖定，然後再次停止 SSIS IR。

### <a name="nodeunavailable"></a>NodeUnavailable

當 IR 正在執行時會發生此錯誤，這表示 IR 已變得狀況不良。 造成此錯誤的原因一律是 DNS 伺服器或 NSG 設定有變更，因此讓 SSIS IR 無法連線至必要服務。 由於 DNS 伺服器和 NSG 的設定是由客戶控制的，因此客戶必須在其那一端修正執行問題。 如需詳細資訊，請參閱 [SSIS IR 虛擬網路設定](./join-azure-ssis-integration-runtime-virtual-network.md)。 如果您仍然遇到問題，請連絡 Azure Data Factory 支援小組。

## <a name="static-public-ip-addresses-configuration"></a>靜態公用 IP 位址設定

當您將 Azure-SSIS IR 加入 Azure 虛擬網路時，您也可以為 IR 攜帶您自己的靜態公用 IP 位址，讓 IR 可以存取限制特定 IP 位址存取的資料來源。 如需詳細資訊，請參閱[將 Azure-SSIS Integration Runtime 加入虛擬網路](./join-azure-ssis-integration-runtime-virtual-network.md)。

除了上述的虛擬網路問題之外，您也可以符合靜態公用 IP 位址的相關問題。 請檢查下列錯誤以取得協助。

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

當您啟動 Azure-SSIS IR 時，會發生此錯誤的原因有很多種：

| 錯誤訊息 | 解決方案|
|:--- |:--- |
| 提供的靜態公用 IP 位址已在使用中，請為您的 Azure-SSIS Integration Runtime 提供兩個未使用的 IP 位址。 | 您應該選取兩個未使用的靜態公用 IP 位址，或移除指定之公用 IP 位址的目前參考，然後重新開機 Azure-SSIS IR。 |
| 提供的靜態公用 IP 位址沒有 DNS 名稱，請為您的 Azure-SSIS Integration Runtime 提供兩個 DNS 名稱。 | 您可以在 Azure 入口網站中設定公用 IP 位址的 DNS 名稱，如下圖所示。 特定步驟如下： (1) 開啟 Azure 入口網站並移至此公用 IP 位址的資源頁面; (2) 選取 [設定 **] 區段並** 設定 DNS 名稱，然後按一下 [ **儲存** ] 按鈕; (3) 重新開機 Azure-SSIS IR。 |
| 為您的 Azure-SSIS Integration Runtime 提供的 VNet 和靜態公用 IP 位址必須位於相同的位置。 | 根據 Azure 網路的需求，靜態公用 IP 位址和虛擬網路應該位於相同的位置和訂用帳戶中。 請提供兩個有效的靜態公用 IP 位址，然後重新開機 Azure-SSIS IR。 |
| 提供的靜態公用 IP 位址是基本 IP 位址，請為您的 Azure-SSIS Integration Runtime 提供兩個標準。 | 請參閱 [公用 IP 位址的 sku](../virtual-network/public-ip-addresses.md#sku) 以取得協助。 |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

如果 Azure-SSIS IR 布建失敗，則會刪除所有已建立的資源。 但是，如果訂用帳戶或資源群組上有資源刪除鎖定 (其中包含您的靜態公用 IP 位址) 層級，則不會如預期般刪除網路資源。 若要修正錯誤，請移除刪除鎖定，然後重新開機 IR。

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

當您停止 Azure-SSIS IR 時，將會刪除在包含公用 IP 位址的資源群組中建立的所有網路資源。 但是，如果訂用帳戶或資源群組上有資源刪除鎖定 (（其中包含您的靜態公用 IP 位址) 層級），則刪除可能會失敗。 請移除刪除鎖定，然後重新開機 IR。

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR 會定期自動更新。 在升級期間會建立新的 IR 節點，而且會刪除舊的節點。 此外，所建立的網路資源 (例如，系統會刪除舊節點的負載平衡器和網路安全性群組) ，並在您的訂用帳戶下建立新的網路資源。 此錯誤表示因為訂用帳戶或資源群組的刪除鎖定 (，其中包含您的靜態公用 IP 位址) 層級，所以刪除舊節點的網路資源失敗。 請移除刪除鎖定，讓我們可以清除舊節點，並釋放舊節點的靜態公用 IP 位址。 否則無法釋出靜態公用 IP 位址，我們將無法再升級您的 IR。

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

當您想要攜帶自己的靜態公用 IP 位址時，應提供兩個公用 IP 位址。 其中一個會用來立即建立 IR 節點，而另一個節點將會在 IR 的升級期間使用。 當其他公用 IP 位址在升級期間無法使用時，就會發生此錯誤。 請參閱  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) 以取得可能的原因。