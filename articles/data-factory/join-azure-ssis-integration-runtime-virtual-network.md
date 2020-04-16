---
title: 將 Azure-SSIS 整合執行階段加入虛擬網路
description: 瞭解如何將 Azure-SSIS 整合執行時加入 Azure 虛擬網路。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: cf13dbe17738ca1ae658c73bb0092a219b4823d1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415911"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>將 Azure-SSIS 整合執行階段加入虛擬網路

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 資料工廠中使用 SQL 伺服器整合服務 (SSIS) 時,應在以下方案中將 Azure-SSIS 整合執行時 (IR) 加入 Azure 虛擬網路:

- 您希望從在 Azure-SSIS IR 上運行的 SSIS 包連接到本地數據存儲,而無需將自承載的 IR 設定為代理。 

- 您希望在 Azure SQL 資料庫中託管 SSIS 目錄資料庫 (SSISDB),該資料庫具有 IP 防火牆規則/虛擬網路服務終結點,或者具有專用終結點的託管實例。 

- 您希望從 Azure-SSIS IR 上運行的 SSIS 包連接到配置的 Azure 服務終結點。

- 您希望從 Azure-SSIS IR 上運行的 SSIS 套件連接到使用 IP 防火牆規則配置的數據存儲/ 資源。

數據工廠允許您將 Azure-SSIS IR 加入透過經典部署模型或 Azure 資源管理器部署模型創建的虛擬網路。

> [!IMPORTANT]
> 正在棄用經典虛擬網路,因此請使用 Azure 資源管理器虛擬網路。  如果您已經使用經典虛擬網路,請儘快切換到 Azure 資源管理器虛擬網路。

[配置 Azure-SQL 伺服器整合服務 (SSIS) 整合執行時 (IR) 以加入虛擬網路](tutorial-deploy-ssis-virtual-network.md)教學顯示了透過 Azure 門戶執行的最小步驟。 本文將介紹本教程,並介紹所有可選任務:

- 如果您使用的是虛擬網路(經典)。
- 如果為 Azure-SSIS IR 自帶公共 IP 位址。
- 如果您使用自己的網功能變數名稱系統 (DNS) 伺服器。
- 如果在子網上使用網路安全組 (NSG)。
- 如果使用 Azure 快速路由或使用者定義的路由 (UDR)。
- 如果使用自定義的 Azure SSIS IR。
- 如果使用 Azure 電源外殼預配。

## <a name="access-to-on-premises-data-stores"></a>存取內部部署資料存放區

如果 SSIS 包存取本地資料儲存,則可以將 Azure-SSIS IR 連接到連接到本地網路的虛擬網路。 或者,您可以將自承載的 IR 配置為 Azure-SSIS IR 的代理。 有關詳細資訊,請參閱[將自託管 IR 配置為 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)的代理。 

將 Azure-SSIS IR 加入虛擬網路時,請記住以下要點: 

- 如果沒有虛擬網路連接到本地網路,請先創建[Azure 資源管理器虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network),供 Azure-SSIS IR 加入。 然後配置網站到網站的[VPN 閘道連接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或從該虛擬網路到本地網路[的 ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)連接。 

- 如果 Azure 資源管理器虛擬網路已連接到與 Azure-SSIS IR 位於同一位置的本地網路,則可以將 IR 加入該虛擬網路。 

- 如果經典虛擬網路已連接到與 Azure-SSIS IR 不同位置的本地網路,則可以創建[Azure 資源管理器虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network),供 Azure-SSIS IR 加入。 然後設定[經典到 Azure 資源管理員虛擬網路連線](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。 
 
- 如果 Azure 資源管理器虛擬網路已連接到與 Azure-SSIS IR 不同位置的本地網路,則可以首先為 Azure-SSIS IR 創建 Azure[資源管理器虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)以加入。 然後配置 Azure 資源管理器到 Azure 資源管理器虛擬網路連接。 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>在 SQL 資料庫中託管 SSIS 目錄

如果將 SSIS 目錄託管在具有虛擬網路服務終結點的 Azure SQL 資料庫中,請確保將 Azure-SSIS IR 加入同一虛擬網路和子網。

如果在具有專用終結點的託管實例中託管 SSIS 目錄,請確保將 Azure-SSIS IR 加入同一虛擬網路,但加入與託管實例不同的子網中。 要將 Azure-SSIS IR 連接到與託管實例不同的虛擬網路,我們建議虛擬網路對等互連(僅限於同一區域)或從虛擬網路到虛擬網路的連接。 有關詳細資訊,請參閱[將應用程式連接到 Azure SQL 資料庫託管實例](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="access-to-azure-services"></a>對 Azure 服務的存取

如果 SSIS 包存取支援[虛擬網路服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)的 Azure 資源,並且希望從 Azure-SSIS IR 保護對這些資源的訪問,則可以將 Azure-SSIS IR 連接到為虛擬網路服務終結點配置的虛擬網路子網,然後將虛擬網路規則添加到相關的 Azure 資源以允許從同一子網進行訪問。

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>存取 IP 防火牆規則保護的資料來源

如果 SSIS 包存取僅允許特定靜態公共 IP 位址的資料儲存/資源,並且希望從 Azure-SSIS IR 保護對這些資源的訪問,則可以在將其加入虛擬網路時為 Azure-SSIS IR 引入自己的[公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address),然後將 IP 防火牆規則添加到相關資源,以允許從這些 IP 位址進行訪問。

在所有情況下,虛擬網路只能通過 Azure 資源管理器部署模型進行部署。

下列各節提供更多詳細資料。 

## <a name="virtual-network-configuration"></a>虛擬網路設定

設定虛擬網路以滿足以下要求: 

- 請確保它是`Microsoft.Batch`託管 Azure-SSIS IR 的虛擬網路子網的訂閱下的註冊提供程式。 如果使用經典虛擬網路,也可以加入`MicrosoftAzureBatch`該虛擬網路的經典虛擬機參與者角色。 

- 請確定您擁有必要權限。 有關詳細資訊,請參閱[設定權限](#perms)。

- 選取要裝載 Azure-SSIS IR 的適當子網路。 有關詳細資訊,請參閱[選擇子網](#subnet)。 

- 如果為 Azure-SSIS IR 自帶公共 IP 位址,請參閱[選擇靜態公共 IP 位址](#publicIP)

- 如果在虛擬網路上使用自己的網域名稱系統 (DNS) 伺服器,請參考設定[DNS 伺服器](#dns_server)。 

- 如果在子網路網路安全群組 (NSG),請參閱[設定 NSG](#nsg)。 

- 如果使用 Azure 快速路由或使用者定義的路由 (UDR),請參閱[使用 Azure 快速路由或 UDR](#route)。 

- 確保虛擬網路的資源群組(或公共 IP 位址的資源群組(如果自帶公共 IP 位址)可以創建和刪除某些 Azure 網路資源。 有關詳細資訊,請參閱[設定資源群組](#resource-group)。 

- 如果自定義[Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)IR 中的自定義 Azure-SSIS IR,則 Azure-SSIS IR 節點將從預定義的 172.16.0 到 172.31.255.255 之間獲取專用 IP 位址。 因此,請確保虛擬或本地網路的專用 IP 位址範圍不會與此範圍衝突。

此圖顯示 Azure-SSIS IR 所需的連線:

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>設定權限

建立 Azure SSIS IR 的使用者必須具有以下權限:

- 如果您正要將 SSIS IR 加入 Azure Resource Manager 虛擬網路，您有兩個選項：

  - 使用內建的「網路參與者」角色。 此角色隨附 _microsoft.network /\*_ 權限，它的權限範圍大於必要的範圍。

  - 建立包含只有必要 _Microsoft.Network/virtualNetworks/\*/join/action_ 權限的自訂角色。 如果還需要在將其加入 Azure 資源管理器虛擬網路時為 Azure-SSIS IR 引入自己的公共 IP 位址,請在角色中包括_Microsoft.Network/publicIP 位址/*/聯接/操作_許可權。

- 如果您要將 SSIS IR 加入傳統虛擬網路中，我們建議您使用內建的「傳統虛擬機器參與者」角色。 否則，您必須定義自訂角色，以包含可加入虛擬網路的權限。

### <a name="select-the-subnet"></a><a name="subnet"></a>選擇子網

當您選擇子網路時: 

- 不要選擇閘道裝置來部署 Azure-SSIS IR。 它專用於虛擬網路閘道。 

- 確保您選擇的子網路有足夠的可用位址空間供 Azure-SSIS IR 使用。 將可用的 IP 位址保留至少兩倍於 IR 節點編號。 Azure 會在每個子網路中保留一些 IP 位址。 不能使用這些位址。 子網的第一個和最後一個 IP 位址保留用於協定一致性,另外三個位址用於 Azure 服務。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- 不要使用由其他 Azure 服務(例如,SQL 資料庫託管實例、應用服務等)獨佔的子網。 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>選擇靜態公共 IP 位址

如果要在將其加入虛擬網路時為 Azure-SSIS IR 提供自己的靜態公共 IP 位址,請確保這些位址滿足以下要求:

- 應提供兩個尚未與其他 Azure 資源關聯的未使用的資源。 當我們定期升級 Azure-SSIS IR 時,將使用額外的一個。 請注意,一個公共 IP 位址不能在活動 Azure-SSIS IR 之間共用。

- 它們都應該是標準類型的靜態的。 有關詳細資訊[,請參閱公共 IP 位址的 SKU。](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)

- 它們都應該有一個 DNS 名稱。 如果在創建 DNS 名稱時未提供 DNS 名稱,則可以在 Azure 門戶上執行此操作。

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- 它們和虛擬網路應在同一訂閱下並在同一區域中。

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>設定 DNS 伺服器 
如果需要在 Azure-SSIS IR 加入的虛擬網路中使用自己的 DNS 伺服器來解決專用主機名,請確保它還可以解析全域 Azure 主機`<your storage account>.blob.core.windows.net`名(例如,名為的 Azure 儲存 Blob)。 

推薦的一種方法如下: 

-   配置自訂 DNS 以將請求轉發到 Azure DNS。 您可以將未解析的 DNS 記錄轉寄到自己的 DNS 伺服器上的 Azure 遞迴解析器 (168.63.129.16) 的 IP 位址。 

關於詳細資訊,請參閱[使用您自己的 DNS 伺服器的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

> [!NOTE]
> 請使用完全限定的網功能名稱 (FQDN) 作為專用主機名稱`<your_private_server>.contoso.com`,`<your_private_server>`例如使用 而不是 ,因為 Azure-SSIS IR 不會自動追加您自己的 DNS 後綴。

### <a name="set-up-an-nsg"></a><a name="nsg"></a>設定 NSG
如果需要為 Azure-SSIS IR 使用的子網實現 NSG,請允許透過以下埠進行入站和出站流量: 

-   **Azure-SSIS IR 的入站要求**

| 方向 | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination | 目標連接埠範圍 | 註解 |
|---|---|---|---|---|---|---|
| 輸入 | TCP | 批次管理 | * | VirtualNetwork | 29876, 29877 (如果您加入 IR 到資源管理員虛擬網路 ) <br/><br/>10100、20100、30100 (如果您將 IR 加入傳統虛擬網路)| 數據工廠服務使用這些埠與虛擬網路中的 Azure-SSIS IR 節點進行通訊。 <br/><br/> 無論是否創建子網級 NSG,資料工廠始終在連接到承載 Azure-SSIS IR 的虛擬機器的網路介面卡 (NIC) 級別配置 NSG。 該 NIC 層級的 NSG 僅允許來自指定連接埠上 Data Factory IP 位址的輸入流量。 即使您在子網級別向 Internet 流量打開這些埠,在 NIC 級別也會阻止來自不是資料工廠 IP 位址的 IP 位址的流量。 |
| 輸入 | TCP | 公司網路鋸 | * | VirtualNetwork | 3389 | ( 選擇性的 )僅當 Microsoft 支援者要求客戶打開進行高級故障排除時,才需要此規則,並且可以在故障排除後立即關閉。 **CorpNetSaw**服務標記只允許 Microsoft 公司網路上的安全訪問工作站使用遠端桌面。 無法從門戶中選擇此服務標記,並且只能通過 Azure PowerShell 或 Azure CLI 提供。 <br/><br/> 在 NIC 級別 NSG 中,埠 3389 預設處於打開狀態,我們允許您在子網級 NSG 上控制埠 3389,同時 Azure-SSIS IR 預設不允許埠 3389 出站在每個 IR 節點上的 Windows 防火牆規則進行保護。 |
||||||||

-   **Azure-SSIS IR 的出站要求**

| 方向 | 傳輸通訊協定 | 來源 | 來源連接埠範圍 | Destination | 目標連接埠範圍 | 註解 |
|---|---|---|---|---|---|---|
| 輸出 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虛擬網路中 Azure-SSIS IR 的節點使用此埠訪問 Azure 服務,如 Azure 儲存和 Azure 事件中心。 |
| 輸出 | TCP | VirtualNetwork | * | Internet | 80 | ( 選擇性的 )虛擬網路中的 Azure-SSIS IR 節點使用此埠從 Internet 下載證書吊銷清單。 如果阻止此流量,則在啟動 IR 時可能會遇到性能降級,並且失去檢查證書吊銷清單以檢查證書使用方式的功能。 如果要進一步縮小目標範圍到某些 FQDN,請參閱**使用 Azure ExpressRoute 或 UDR**部分|
| 輸出 | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | ( 選擇性的 )僅當虛擬網路中的 Azure-SSIS IR 節點存取 SQL 資料庫伺服器託管的 SSISDB 時,才需要此規則。 如果 SQL 資料庫伺服器連接策略設定為**代理**而不是**重定向**,則只需要埠 1433。 <br/><br/> 此出站安全規則不適用於由託管實例託管的虛擬網路或配置專用終結點的 Azure 資料庫伺服器託管的 SSISDB。 |
| 輸出 | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | ( 選擇性的 )僅當虛擬網路中的 Azure-SSIS IR 節點存取由虛擬網路中託管實例託管的 SSISDB 或配置了專用終結點的 Azure 資料庫伺服器時,才需要此規則。 如果 SQL 資料庫伺服器連接策略設定為**代理**而不是**重定向**,則只需要埠 1433。 |
| 輸出 | TCP | VirtualNetwork | * | 儲存體 | 445 | ( 選擇性的 )僅當要執行存儲在 Azure 檔中的 SSIS 包時,才需要此規則。 |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>使用 Azure 快速路由或 UDR
如果要檢查 Azure-SSIS IR 的出站流量,可以通過[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)強制隧道(通告 BGP 路由,0.0.0/0 到虛擬網路)將啟動的流量路由到本地防火牆設備,或者通過[UDR](../virtual-network/virtual-networks-udr-overview.md)將流量作為防火牆或[Azure 防火牆](https://docs.microsoft.com/azure/firewall/)路由。 

![Azure-SSIS IR 的 NVA 專案](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

您需要執行以下操作,以使整個場景正常工作
   -   Azure 批次處理管理服務和 Azure-SSIS IR 之間的入站流量無法透過防火牆設備路由。
   -   防火牆設備應允許 Azure-SSIS IR 所需的出站流量。

Azure 批處理管理服務和 Azure-SSIS IR 之間的入站流量無法路由到防火牆設備,否則流量將由於非對稱路由問題而中斷。 必須為入站流量定義路由,以便流量可以以相同的方式回復。 可以定義特定的 UDR,以在 Azure 批次管理服務和 Azure-SSIS IR 之間路由流量,下一個躍點類型為**Internet**。

例如,`UK South`如果您的 Azure-SSIS IR 位於該位置,並且希望透過 Azure 防火牆檢查出站`BatchNodeManagement.UKSouth`流量,則首先將從[服務標記 IP 範圍下載連結](https://www.microsoft.com/download/details.aspx?id=56519)或透過[服務標記發現 API](https://aka.ms/discoveryapi)獲取服務標記的 IP 範圍清單。 然後套用以下相關 IP 範圍路由的 UDR,將下一個躍點類型作為**Internet**以及 0.0.0.0/0 路由,下一個躍點類型為**虛擬設備**。

![Azure 批次處理 UDR 設定](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 此方法會產生額外的維護成本。 定期檢查 IP 範圍並將新的 IP 範圍添加到 UDR 中,以避免破壞 Azure-SSIS IR。 我們建議每月檢查 IP 範圍,因為當服務標記中出現新 IP 時,IP 將再需要一個月生效。 

為了簡化 UDR 規則的設定,可以按照 Powershell 文稿執行,為 Azure 批次管理服務添加 UDR 規則:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

對於防火牆設備允許出站流量,您需要允許出站到與 NSG 出站規則中要求相同的埠。
-   埠 443 的目標為 Azure 雲服務。

    如果使用 Azure 防火牆,則可以使用 Azure 雲端服務標記指定網路規則。 對於其他類型的防火牆,您可以簡單地允許埠 443 的所有目標,也可以根據 Azure 環境的類型在 FQDN 下方允許:

    | Azure 環境 | 端點                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 公用      | <ul><li><b>Azure 資料工廠(管理)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure 儲存(管理)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure 容器註冊表(自訂設定)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>事件中心(紀錄紀錄)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>微軟記錄服務(內部使用)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure 資料工廠(管理)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure 儲存(管理)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure 容器註冊表(自訂設定)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>事件中心(紀錄紀錄)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>微軟記錄服務(內部使用)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure 資料工廠(管理)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure 儲存(管理)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure 容器註冊表(自訂設定)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>事件中心(紀錄紀錄)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>微軟記錄服務(內部使用)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    對於 Azure 儲存、Azure 容器註冊錶和事件中心的 FQDN,您還可以選擇為虛擬網路啟用以下服務終結點,以便對這些終結點的網路流量通過 Azure 骨幹網,而不是路由到防火牆設備:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   埠 80 的目標為 CRL 下載網站。

    您應該允許在以下用作 CRL(憑證撤銷清單)的 FQDN 下載憑證站點,用於 Azure-SSIS IR 管理目的:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    如果使用具有不同 CRL 的證書,建議也包括它們。 您可以閱讀此內容,以瞭解有關[憑證撤銷清單的更多內容](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)。

    如果不允許此流量,則在啟動 Azure-SSIS IR 時可能會遇到性能降級,並且失去檢查證書吊銷清單以檢查證書使用的能力,這是安全角度不建議的。

-   埠 1433、11000-11999,目標為 Azure SQL(僅在虛擬網路中的 Azure-SSIS IR 節點訪問 SQL 資料庫伺服器託管的 SSISDB 時才需要)。

    如果使用 Azure 防火牆,則可以使用 Azure SQL 服務標記指定網路規則,否則可以在防火牆設備中允許目標作為特定的 Azure sql url。

-   埠 445 的目標為 Azure 儲存(僅在執行存儲在 Azure 檔中的 SSIS 包時才需要)。

    如果使用 Azure 防火牆,則可以使用儲存服務標記指定網路規則,否則可能會允許在防火牆設備中將目標指定為特定的 Azure 檔案儲存 URL。

> [!NOTE]
> 對於 Azure SQL 和儲存,如果在子網上配置虛擬網路服務終結點,則同一區域中的 Azure-SSIS IR 和 Azure SQL 之間的流量 + 同一區域中的 Azure 儲存或配對區域中的 Azure 儲存將直接路由到 Microsoft Azure 骨幹網路,而不是防火牆設備。

如果不需要檢查 Azure-SSIS IR 的出站流量的功能,只需應用路由來強制所有流量到下一躍點類型**Internet:**

-   在 Azure ExpressRoute 方案中,可以將具有下一個躍點類型的 0.0.0/0 路由應用為**Internet,** 將其作為 Internet 應用於承載 Azure-SSIS IR 的子網。 
-   在 NVA 專案中,可以修改應用於承載 Azure-SSIS IR 的子網上應用的現有 0.0.0.0 路由,從下一個躍點類型作為**虛擬裝置**到**Internet**。

![新增路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> 指定下一躍點類型的**Internet**路由並不意味著所有流量都將透過 Internet。 只要目標位址是 Azure 服務之一,Azure 就通過 Azure 的主幹網路將流量直接路由到服務,而不是將流量路由到 Internet。

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>設定資源群組

Azure-SSIS IR 需要在與虛擬網路相同的資源群組下，建立特定的網路資源。 這些資源包括:
- Azure 負載均衡器,名稱為*\<Guid>-azure 批次處理-雲端服務負載平衡器*。
- Azure 公共 IP 位址,名稱*\<為 Guid>-azurebatch-雲端服務公共 。*
- 一個網路工作安全組,名稱為*\<Guid>-azurebatch-雲端服務網路安全群組*。 

> [!NOTE]
> 現在,您可以為 Azure-SSIS IR 帶來自己的靜態公共 IP 位址。 在這種情況下,我們將只創建 Azure 負載均衡器和網路安全組下與靜態公共 IP 位址相同的資源組,而不是虛擬網路。

這些資源將在 Azure-SIS IR 啟動時創建。 當 Azure-SSIS IR 停止時,它們將被刪除。 如果為 Azure-SSIS IR 自帶靜態公共 IP 位址,則當 Azure-SSIS IR 停止時,將不會刪除自己的靜態公共 IP 位址。 為了避免阻止 Azure-SSIS IR 停止,請不要在其他資源中重用這些網路資源。

確保虛擬網路/靜態公共IP位址所屬的資源組/訂閱沒有資源鎖定。 如果設定唯讀/刪除鎖,則啟動和停止 Azure-SSIS IR 將失敗,否則將停止回應。

確保沒有 Azure 政策可阻止在虛擬網路/靜態公共 IP 位址所屬的資源組/訂閱下建立以下資源: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

確保訂閱的資源配額足以滿足上述三個網路資源。 具體而言,對於在虛擬網路中創建的每個 Azure-SSIS IR,您需要為上述三個網路資源中的每一個保留兩個免費配額。 當我們定期升級 Azure-SSIS IR 時,將使用額外的一個配額。

### <a name="faq"></a><a name="faq"></a> 常見問題集

- 如何保護 Azure-SSIS IR 上公開的用於入站連接的公共 IP 位址? 是否可以刪除公共 IP 位址?
 
  現在,當 Azure-SSIS IR 加入虛擬網路時,將自動創建公共 IP 位址。 我們確實有一個 NIC 級 NSG,只允許 Azure 批處理管理服務入站連接到 Azure-SSIS IR。 您還可以為入站保護指定子網級 NSG。

  如果不希望公開任何公共 IP 位址,請考慮[將自承載的 IR 配置為 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis),而不是將 Azure-SSIS IR 加入虛擬網路(如果這適用於您的方案)。
 
- 是否可以將 Azure-SSIS IR 的公共 IP 位址添加到防火牆的資料來源允許清單中?

  現在,您可以為 Azure-SSIS IR 帶來自己的靜態公共 IP 位址。 在這種情況下,您可以將 IP 位址添加到防火牆的數據來源允許清單中。 您還可以考慮以下其他選項,以確保從 Azure-SSIS IR 進行資料存取,具體取決於您的方案:

  - 如果數據源位於本地,則在將虛擬網路連接到本地網路並將 Azure-SSIS IR 連接到虛擬網路子網後,可以將該子網的專用 IP 位址範圍添加到數據源的防火牆允許清單中。
  - 如果數據來源是支援虛擬網路服務終結點的 Azure 服務,則可以在虛擬網路子網配置虛擬網路服務終結點,並將 Azure-SSIS IR 加入該子網。 然後,您可以將具有該子網的虛擬網路規則添加到數據源的防火牆中。
  - 如果資料來源是非 Azure 雲端服務,則可以使用 UDR 透過靜態公共 IP 位址將出站流量從 Azure-SSIS IR 路由到 NVA/Azure 防火牆。 然後,您可以將 NVA/Azure 防火牆的靜態公共 IP 位址添加到防火牆的數據源允許清單中。
  - 如果上述選項均不滿足您的需要,請考慮[配置自承載的 IR 作為 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)的代理。 然後,您可以將承載自承載 IR 的電腦的靜態公共 IP 位址添加到數據源的防火牆允許清單中。

- 如果我想為 Azure-SSIS IR 自帶自己的公共位址,為什麼需要提供兩個靜態公共位址?

  Azure-SSIS IR 會定期自動更新。 在升級期間創建新節點,舊節點將被刪除。 但是,為了避免停機,在新節點準備就緒之前不會刪除舊節點。 因此,舊節點使用的第一個靜態公共 IP 位址無法立即釋放,我們需要您的第二個靜態公共 IP 位址來創建新節點。

- 我帶來了 Azure-SSIS IR 的靜態公共 IP 位址,但為什麼它仍然無法訪問我的數據源?

  - 確認兩個靜態公共 IP 位址都添加到防火牆的資料來源允許清單中。 每次升級 Azure-SSIS IR 時,其靜態公共 IP 位址都會在您帶來的兩個地址之間切換。 如果僅將其中一個添加到允許清單中,則 Azure-SSIS IR 的數據訪問將在升級後中斷。
  - 如果數據源是 Azure 服務,請檢查是否已使用虛擬網路服務終結點配置它。 如果是這樣,從 Azure-SSIS IR 到數據源的流量將切換到使用 Azure 服務管理的專用 IP 位址,並將您自己的靜態公共 IP 位址添加到防火牆的數據源允許清單中將不會生效。

## <a name="azure-portal-data-factory-ui"></a>Azure 入口網站 (Data Factory UI)

本節介紹如何使用 Azure 門戶和數據工廠 UI 將現有的 Azure-SSIS IR 加入虛擬網路(經典或 Azure 資源管理器)。 

在將 Azure-SSIS IR 加入虛擬網路之前,需要正確配置虛擬網路。 按照適用於虛擬網路類型(經典或 Azure 資源管理員)的部分中的步驟操作。 然後按照第三部分中的步驟將 Azure-SSIS IR 加入虛擬網路。 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>設定 Azure 資源管理員虛擬網路

在嘗試將 Azure-SSIS IR 加入到該虛擬網路之前,使用門戶配置 Azure 資源管理器虛擬網路。

1. 啟動 Microsoft Edge 或 Google Chrome。 目前,只有這些 Web 瀏覽器支援數據工廠 UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選擇**更多服務**。 篩選並選取 [虛擬網路]****。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路]**** 頁面上，選取 [屬性]****。 

1. 選取 [資源識別碼]**** 的 [複製] 按鈕，將虛擬網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 在左邊選單上, 選擇**子網**。 確保可用位址數大於 Azure-SSIS IR 中的節點。 

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure 批處理提供程式。 如果訂閱中已有 Azure 批處理帳戶,則訂閱將註冊為 Azure 批處理。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   1. 在 Azure 門戶中,在左側功能表中,選擇 **「訂閱**」。。 

   1. 選取您的訂用帳戶。 

   1. 在左側,選擇**資源提供程式**,並確認**Microsoft.Batch**是註冊提供程式。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="configure-a-classic-virtual-network"></a>設定傳統網路

在嘗試將 Azure-SSIS IR 加入到它之前,使用門戶配置經典虛擬網路。 

1. 啟動 Microsoft Edge 或 Google Chrome。 目前,只有這些 Web 瀏覽器支援數據工廠 UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選擇**更多服務**。 篩選並選取 [虛擬網路 (傳統)]****。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路 (傳統)]**** 頁面上，選取 [屬性]****。 

   ![傳統虛擬網路資源識別碼](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. 選取 [資源識別碼]**** 的 [複製] 按鈕，將傳統網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 在左邊選單上, 選擇**子網**。 確保可用位址數大於 Azure-SSIS IR 中的節點。 

   ![虛擬網路中的可用位址數目](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. 將 **MicrosoftAzureBatch** 加入虛擬網路的**傳統虛擬機器參與者**角色。 

   1. 在左邊選單上,選擇 **「存取控制 (IAM)」,** 然後選擇「**角色分配**」選項卡。 

       ![[存取控制] 和 [新增] 按鈕](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. 選擇 **「添加角色分配**」。

   1. 在 **'新增角色配置'** 頁上,對於**角色**,請選擇**經典虛擬機參與者**。 在 **「選擇」** 框中,貼上**ddbf3205-c6bd-46ae-8127-60eb93363864,** 然後從搜尋結果清單中選擇**Microsoft Azure 批次處理**。 

       !['新增角色分配「頁上的搜尋結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. 選擇 **「儲存**」以儲存設定並關閉頁面。 

       ![儲存存取設定](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. 確認您在參與者清單中看到 **Microsoft Azure Batch**。 

       ![確認 Azure Batch 存取](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure 批處理提供程式。 如果訂閱中已有 Azure 批處理帳戶,則訂閱將註冊為 Azure 批處理。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   1. 在 Azure 門戶中,在左側功能表中,選擇 **「訂閱**」。。 

   1. 選取您的訂用帳戶。 

   1. 在左側,選擇**資源提供程式**,並確認**Microsoft.Batch**是註冊提供程式。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>將 Azure-SSIS IR 加入虛擬網路

設定 Azure 資源管理員虛擬網路或經典虛擬網路後,可以將 Azure-SSIS IR 加入虛擬網路:

1. 啟動 Microsoft Edge 或 Google Chrome。 目前,只有這些 Web 瀏覽器支援數據工廠 UI。 

1. 在[Azure 門戶](https://portal.azure.com)中,在左側功能表中,選擇 **「數據工廠**」。 如果在功能表上看不到**資料工廠**,請選擇 **"更多服務**",然後在 **"智慧 + 分析**"部分中選擇 **"資料工廠**"。 

   ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在清單中使用 Azure-SSIS IR 選擇資料工廠。 您會看到資料處理站的首頁。 選擇 **「作者&監視器磁貼**。 您會在個別的索引標籤上看到 Data Factory UI。 

   ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在 Data Factory UI 中，切換至 [編輯]**** 索引標籤，選取 [連線]****，然後切換至 [整合執行階段]**** 索引標籤。 

   ![[整合執行階段] 索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果 Azure-SSIS IR 正在執行,請在「**操作」** 欄中的 **「整合執行時**」清單中執行,選擇 Azure-SSIS IR 的 **「停止」** 按鈕。 在停止 Azure-SSIS IR 之前,無法對其進行編輯。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在 **「整合時」** 清單中,在 **「操作」** 欄中,為 Azure-SSIS IR 選擇 **「編輯」** 按鈕。 

   ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在整合式執行時設定面板上,透過選擇**下一步**「按鈕,提前完成 **」常規設定**「和 **」SQL 設定「** 部分。 

1. 在 **「進階設定」** 部分: 

   1. 選擇**Azure-SSIS 整合執行時的 VNet 以加入,允許 ADF 創建某些網路資源,並選擇攜帶自己的靜態公共 IP 位址**複選框。 

   1. 針對 [訂用帳戶]****，選取具有您的虛擬網路的 Azure 訂用帳戶。

   1. 針對 [位置]****，選取整合執行階段的相同位置。

   1. 對於**類型**,請選擇虛擬網路的類型:經典或 Azure 資源管理員。 我們建議您選擇 Azure 資源管理器虛擬網路,因為經典虛擬網路將很快被棄用。

   1. 針對 **[VNet 名稱]**，選取您虛擬網路的名稱。 它應該與 Azure SQL 資料庫伺服器使用的相同,該伺服器具有虛擬網路服務終結點,或者使用專用終結點託管 SSISDB 的託管實例。 或者它應該是連接到本地網路的同一個。 否則,任何虛擬網路都可以為 Azure-SSIS IR 帶來自己的靜態公共 IP 位址。

   1. 針對 [子網路名稱]****，選取虛擬網路的子網路名稱。 它應該與 Azure SQL 資料庫伺服器所用的相同,該伺服器具有虛擬網路服務終結點來承載 SSISDB。 或者,它應該是一個不同的子網,與用於託管實例的子網,具有專用終結點來承載 SSISDB。 否則,它可以是任何子網,為 Azure-SSIS IR 自帶靜態公共 IP 位址。

   1. 選擇 **「為 Azure-SSIS 整合時帶來靜態公共 IP 位址**」複選框,以選擇是否要為 Azure-SSIS IR 攜帶自己的靜態公共 IP 位址,以便允許在資料源的防火牆上設置這些位址。

      如果選擇該複選框,請完成以下步驟。

      1. 對於**第一個靜態公共 IP 位址**,選擇滿足 Azure-SSIS IR[要求](#publicIP)的第一個靜態公共 IP 位址。 如果沒有,請按下「**創建新**連結」在 Azure 門戶上創建靜態公共 IP 位址,然後單擊此處的刷新按鈕,以便選擇這些位址。
      
      1. 對於**第二個靜態公共 IP 位址**,選擇第二個[滿足](#publicIP)Azure-SSIS IR 要求的靜態公共 IP 位址。 如果沒有,請按下「**創建新**連結」在 Azure 門戶上創建靜態公共 IP 位址,然後單擊此處的刷新按鈕,以便選擇這些位址。

   1. 選擇**VNet 認證**。 如果驗證成功,請選擇"**繼續**"。 

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. 在 **「摘要」** 部分,查看 Azure-SSIS IR 的所有設定。 然後選擇 **「更新**」。

1. 以選擇 Azure-SSIS IR 的「**動作」** 欄中的 **「開始」** 按鈕來啟動 Azure-SSIS IR。 啟動加入虛擬網路的 Azure-SSIS IR 大約需要 20 到 30 分鐘。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>定義變數

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>設定虛擬網路

在將 Azure-SSIS IR 加入虛擬網路之前,需要配置虛擬網路。 要自動設定 Azure-SSIS IR 的虛擬網路權限和設定以加入虛擬網路,請新增以下文稿:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>建立 Azure-SSIS IR 並將其加入虛擬網路

您可以建立 Azure-SSIS IR，同時將其加入虛擬網路。 有關完整的文稿與說明,請參閱建立[Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>將現有的 Azure-SSIS IR 加入虛擬網路

["創建 Azure-SSIS IR"](create-azure-ssis-integration-runtime.md)一文演示如何建立 Azure-SSIS IR 並將其聯接到同一腳本中的虛擬網络。 如果您已經擁有 Azure-SSIS IR,請按照以下步驟將其加入虛擬網路: 
1. 停止 Azure-SSIS IR。 
1. 設定要加入虛擬網路的 Azure-SSIS IR。 
1. 啟動 Azure-SSIS IR。 

### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR

必須先停止 Azure-SSIS IR,然後才能將其加入到虛擬網路。 此命令會釋出其所有節點並停止計費：

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>進行虛擬網路設定讓 Azure-SSIS IR 可以加入

要配置 Azure-SSIS 將加入的虛擬網路的設定,請使用以下文稿: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>設定 Azure-SSIS IR

要將 Azure-SSIS IR 加入虛擬`Set-AzDataFactoryV2IntegrationRuntime`網路,請執行以下指令: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>啟動 Azure-SSIS IR

要啟動 Azure-SSIS IR,執行以下指令: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

此命令約需要 20 到 30 分鐘才能完成。

## <a name="next-steps"></a>後續步驟

有關 Azure-SSIS IR 的詳細資訊,請參閱以下文章: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供有關 IR 的一般概念資訊,包括 Azure-SSIS IR。 
- [教學:將 SSIS 套件部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本教學提供創建 Azure-SSIS IR 的分步說明。 它會使用 Azure SQL Database 來裝載 SSIS 目錄。 
- [建立 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。 本文將介紹本教程。 它提供了有關在虛擬網路中將 Azure SQL 資料庫與虛擬網路服務終結點或託管實例一起使用以承載 SSIS 目錄的說明。 它展示如何將 Azure-SSIS IR 加入虛擬網路。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文介紹如何獲取有關 Azure-SSIS IR 的資訊。 它提供返回的信息的狀態說明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文介紹如何停止、啟動或刪除 Azure-SSIS IR。 此外也會說明如何藉由新增節點來相應放大 Azure-SSIS IR。