---
title: 將 Azure-SSIS 整合執行階段加入虛擬網路
description: 瞭解如何將 Azure SSIS 整合執行時間加入 Azure 虛擬網路。
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
ms.openlocfilehash: 7e8a1793a329a863c9df97ae5ddcbee6cef10e8e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964277"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>將 Azure-SSIS 整合執行階段加入虛擬網路

在 Azure Data Factory 中使用 SQL Server Integration Services （SSIS）時，您應該在下列案例中，將您的 Azure SSIS 整合執行時間（IR）加入 Azure 虛擬網路：

- 您想要從 Azure SSIS IR 上執行的 SSIS 套件連接到內部部署資料存放區，而不需要設定或管理自我裝載 IR 作為 proxy。 

- 您想要在具有 IP 防火牆規則/虛擬網路服務端點或具有私人端點的受控實例的 Azure SQL Database 中裝載 SSIS 目錄資料庫（SSISDB）。 

- 您想要從 Azure SSIS IR 上執行的 SSIS 套件，連接到設定了虛擬網路服務端點的 Azure 資源。

- 您想要從 Azure SSIS IR 上執行的 SSIS 套件，連接到使用 IP 防火牆規則設定的資料存放區/資源。

Data Factory 可讓您將 Azure SSIS IR 加入透過傳統部署模型或 Azure Resource Manager 部署模型所建立的虛擬網路。

> [!IMPORTANT]
> 傳統虛擬網路即將淘汰，因此請改用 Azure Resource Manager 的虛擬網路。  如果您已經使用傳統虛擬網路，請儘快切換到 Azure Resource Manager 的虛擬網路。

設定[Azure SQL Server Integration Services （SSIS）整合執行時間（IR）以加入虛擬網路](tutorial-deploy-ssis-virtual-network.md)教學課程會顯示透過 Azure 入口網站的最小步驟。 本文會針對教學課程進行擴充，並說明所有的選擇性工作：

- 如果您使用的是虛擬網路（傳統）。
- 如果您為 Azure SSIS IR 帶入自己的公用 IP 位址。
- 如果您使用自己的網域名稱系統（DNS）伺服器。
- 如果您在子網上使用網路安全性群組（NSG）。
- 如果您使用 Azure ExpressRoute 或使用者定義的路由（UDR）。
- 如果您使用自訂的 Azure SSIS IR。
- 如果您使用 Azure Powershell 布建。

## <a name="access-to-on-premises-data-stores"></a>存取內部部署資料存放區

如果您的 SSIS 套件存取內部部署資料存放區，您可以將您的 Azure SSIS IR 加入連線到內部部署網路的虛擬網路。 或者，您可以設定和管理 Azure SSIS IR 的自我裝載 IR 作為 proxy。 如需詳細資訊，請參閱[設定自我裝載 ir 作為 AZURE SSIS IR 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

將您的 Azure SSIS IR 加入虛擬網路時，請記住下列重點： 

- 如果沒有虛擬網路連線到您的內部部署網路，請先建立一個[Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)，讓您的 AZURE SSIS IR 加入。 然後，設定從該虛擬網路到內部部署網路的站對站[VPN 閘道聯](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)機或[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)連線。 

- 如果 Azure Resource Manager 的虛擬網路已連線到與您的 Azure SSIS IR 位於相同位置的內部部署網路，則可以將 IR 加入該虛擬網路。 

- 如果傳統虛擬網路已從您的 Azure SSIS IR 在不同位置連線到您的內部部署網路，您可以建立[Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)，讓您的 AZURE ssis ir 加入。 然後，設定[傳統對 Azure Resource Manager 虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)連線。 
 
- 如果 Azure Resource Manager 的虛擬網路已從您的 Azure SSIS IR 連線到不同位置的內部部署網路，您可以先建立[Azure Resource Manager 的虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)，讓您的 AZURE ssis ir 能夠加入。 然後設定 Azure Resource Manager 對 Azure Resource Manager 的虛擬網路連線。 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>在 SQL Database 中裝載 SSIS 目錄

如果您在具有虛擬網路服務端點的 Azure SQL Database 中裝載 SSIS 目錄，請務必將您的 Azure SSIS IR 加入相同的虛擬網路和子網。

如果您在具有私人端點的受控實例中裝載 SSIS 目錄，請務必將您的 Azure SSIS IR 加入相同的虛擬網路，但在與受控實例不同的子網中。 若要將您的 Azure SSIS IR 加入與受控實例不同的虛擬網路，我們建議虛擬網路對等互連（僅限於相同的區域）或從虛擬網路到虛擬網路的連線。 如需詳細資訊，請參閱[將您的應用程式連線至 Azure SQL Database 受控實例](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="access-to-azure-services"></a>對 Azure 服務的存取

如果您的 SSIS 套件存取支援[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)的 Azure 資源，而且您想要從 AZURE ssis ir 保護這些資源的存取權，您可以將您的 AZURE ssis ir 加入為虛擬網路服務端點設定的虛擬網路子網，然後將虛擬網路規則新增至相關的 Azure 資源，以允許來自相同子網的存取。

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>存取受 IP 防火牆規則保護的資料來源

如果您的 SSIS 套件存取資料存放區/資源，只允許特定的靜態公用 IP 位址，而您想要從 Azure SSIS IR 保護這些資源的存取權，您可以在將 Azure SSIS ir 加入虛擬網路時自備[公用 ip 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)，然後將 ip 防火牆規則新增至相關的資源，以允許來自這些 IP 位址的存取。

在所有情況下，只能透過 Azure Resource Manager 部署模型來部署虛擬網路。

下列各節提供更多詳細資料。 

## <a name="virtual-network-configuration"></a>虛擬網路設定

設定您的虛擬網路以符合下列需求： 

- 請確定在裝載 Azure SSIS IR 的虛擬網路子網訂用帳戶下，`Microsoft.Batch` 是已註冊的提供者。 如果您使用傳統虛擬網路，也請將 `MicrosoftAzureBatch` 加入該虛擬網路的傳統虛擬機器參與者角色。 

- 請確定您擁有必要權限。 如需詳細資訊，請參閱[設定許可權](#perms)。

- 選取要裝載 Azure-SSIS IR 的適當子網路。 如需詳細資訊，請參閱[選取子網](#subnet)。 

- 如果您為 Azure SSIS IR 帶入自己的公用 IP 位址，請參閱[選取靜態公用 ip 位址](#publicIP)

- 如果您在虛擬網路上使用自己的網域名稱系統（DNS）伺服器，請參閱[設定 DNS 伺服器](#dns_server)。 

- 如果您在子網上使用網路安全性群組（NSG），請參閱[設定 NSG](#nsg)。 

- 如果您使用 Azure ExpressRoute 或使用者定義的路由（UDR），請參閱[使用 Azure expressroute 或 UDR](#route)。 

- 請確定虛擬網路的資源群組（如果您攜帶自己的公用 IP 位址，則為公用 IP 位址的資源群組）可以建立和刪除特定的 Azure 網路資源。 如需詳細資訊，請參閱[設定資源群組](#resource-group)。 

- 如果您依照[自訂 AZURE SSIS ir 的設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)中所述自訂您的 AZURE ssis ir，您的 AZURE ssis ir 節點將會從預先定義的172.16.0.0 範圍取得私人 IP 位址到172.31.255.255。 因此，請確定您虛擬或內部部署網路的私人 IP 位址範圍不會與此範圍衝突。

下圖顯示您的 Azure SSIS IR 所需的連線：

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>設定許可權

建立 Azure SSIS IR 的使用者必須具有下列許可權：

- 如果您正要將 SSIS IR 加入 Azure Resource Manager 虛擬網路，您有兩個選項：

  - 使用內建網路參與者角色。 此角色隨附 _microsoft.network /\*_ 權限，它的權限範圍大於必要的範圍。

  - 建立包含只有必要 _Microsoft.Network/virtualNetworks/\*/join/action_ 權限的自訂角色。 如果您也想要將 Azure SSIS IR 的公用 IP 位址加入 Azure Resource Manager 的虛擬網路，請同時在角色中包含_publicIPAddresses/*/join/action_許可權。

- 如果您要將 SSIS IR 加入傳統虛擬網路，建議您使用內建的傳統虛擬機器參與者角色。 否則，您必須定義自訂角色，以包含可加入虛擬網路的權限。

### <a name="subnet"></a> 選取子網路

當您選擇子網時： 

- 請勿選取 GatewaySubnet 以部署 Azure SSIS IR。 它專用於虛擬網路閘道。 

- 請確定您選取的子網具有足夠的可用位址空間，以供 Azure SSIS IR 使用。 將可用的 IP 位址保留至少兩倍的 IR 節點編號。 Azure 會在每個子網路中保留一些 IP 位址。 無法使用這些位址。 子網的第一個和最後一個 IP 位址會保留給通訊協定一致性，而其他三個位址則用於 Azure 服務。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- 請勿使用由其他 Azure 服務（例如，SQL Database 受控實例、App Service 等等）專門佔用的子網。 

### <a name="publicIP"></a>選取靜態公用 IP 位址

如果您想要在將 Azure SSIS IR 加入虛擬網路時攜帶自己的靜態公用 IP 位址，請確定它們符合下列需求：

- 您應該只提供兩個尚未與其他 Azure 資源相關聯的未使用範本。 當我們定期升級您的 Azure SSIS IR 時，將會使用額外的一項。

- 它們應該是標準類型的靜態。 如需詳細資訊，請參閱[公用 IP 位址的 sku](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) 。

- 它們應該都有 DNS 名稱。 如果您在建立時未提供 DNS 名稱，您可以在 Azure 入口網站上執行此動作。

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- 它們和虛擬網路應位於相同的訂用帳戶和相同區域中。

### <a name="dns_server"></a>設定 DNS 伺服器 
如果您需要在您的 Azure SSIS IR 所加入的虛擬網路中使用自己的 DNS 伺服器來解析您的私人主機名稱，請確定它也可以解析全域 Azure 主機名稱（例如，名為 `<your storage account>.blob.core.windows.net`的 Azure 儲存體 blob）。 

其中一個建議的方法如下： 

-   設定自訂 DNS 將要求轉送至 Azure DNS。 您可以將未解析的 DNS 記錄轉送至您自己的 DNS 伺服器上的 Azure 遞迴解析程式的 IP 位址（168.63.129.16）。 

如需詳細資訊，請參閱[使用您自己的 DNS 伺服器的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

> [!NOTE]
> 請為您的私用主機名稱使用完整功能變數名稱（FQDN），例如使用 `<your_private_server>.contoso.com` 而不是 `<your_private_server>`，因為 Azure SSIS IR 不會自動附加您自己的 DNS 尾碼。

### <a name="nsg"></a>設定 NSG
如果您需要為您的 Azure SSIS IR 所使用的子網執行 NSG，請允許透過下列埠的輸入和輸出流量： 

-   **Azure SSIS IR 的輸入需求**

| 方向 | 傳輸通訊協定 | 來源 | 來源埠範圍 | 目的地 | 目的地埠範圍 | 註解 |
|---|---|---|---|---|---|---|
| 輸入 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877（如果您將 IR 加入 Resource Manager 虛擬網路） <br/><br/>10100、20100、30100 (如果您將 IR 加入傳統虛擬網路)| Data Factory 服務會使用這些埠與虛擬網路中的 Azure SSIS IR 節點進行通訊。 <br/><br/> 無論您是否建立子網層級 NSG，Data Factory 一律會在連接至裝載 Azure SSIS IR 之虛擬機器的網路介面卡（Nic）層級上設定 NSG。 該 NIC 層級的 NSG 僅允許來自指定連接埠上 Data Factory IP 位址的輸入流量。 即使您對子網層級的網際網路流量開啟這些埠，來自不 Data Factory IP 位址的 IP 位址流量也會在 NIC 層級遭到封鎖。 |
| 輸入 | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | 選擇性只有在 Microsoft supporter 要求客戶開啟以進行先進的疑難排解時，才需要此規則，並可在進行疑難排解後立即關閉。 **CorpNetSaw**服務標籤只允許 Microsoft 公司網路上的安全存取工作站使用遠端桌面。 此服務標記無法從入口網站選取，而且只能透過 Azure PowerShell 或 Azure CLI 使用。 <br/><br/> 在 NIC 層級 NSG，埠3389預設為開啟，且我們可讓您控制子網層級 NSG 的埠3389，而 Azure SSIS IR 則預設會在每個 IR 節點上的 windows 防火牆規則上不允許埠3389輸出，以供保護。 |
||||||||

-   **Azure SSIS IR 的輸出需求**

| 方向 | 傳輸通訊協定 | 來源 | 來源埠範圍 | 目的地 | 目的地埠範圍 | 註解 |
|---|---|---|---|---|---|---|
| 輸出 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虛擬網路中的 Azure SSIS IR 節點會使用此埠來存取 Azure 服務，例如 Azure 儲存體和 Azure 事件中樞。 |
| 輸出 | TCP | VirtualNetwork | * | Internet | 80 | 選擇性虛擬網路中 Azure SSIS IR 的節點會使用此埠，從網際網路下載憑證撤銷清單。 如果您封鎖此流量，在啟動 IR 並失去檢查憑證使用的憑證撤銷清單功能時，可能會遇到效能降級的情況。 如果您想要進一步將目的地縮小到特定 Fqdn，請參閱**使用 Azure ExpressRoute 或 UDR**一節|
| 輸出 | TCP | VirtualNetwork | * | Sql | 1433、11000-11999 | 選擇性只有當虛擬網路中的 Azure SSIS IR 節點存取由您的 SQL Database 伺服器主控的 SSISDB 時，才需要此規則。 如果您的 SQL Database 伺服器連線原則設定為 [ **Proxy** ] 而不是 [重新**導向**]，則只需要端口1433。 <br/><br/> 此輸出安全性規則不適用於虛擬網路或設定了私用端點之 Azure 資料庫伺服器中的受控實例所裝載的 SSISDB。 |
| 輸出 | TCP | VirtualNetwork | * | VirtualNetwork | 1433、11000-11999 | 選擇性只有當虛擬網路中的 Azure SSIS IR 節點在虛擬網路或設定了私用端點的 Azure 資料庫伺服器中，存取您的受控實例所裝載的 SSISDB 時，才需要此規則。 如果您的 SQL Database 伺服器連線原則設定為 [ **Proxy** ] 而不是 [重新**導向**]，則只需要端口1433。 |
| 輸出 | TCP | VirtualNetwork | * | 儲存體 | 445 | 選擇性只有當您想要執行儲存在 Azure 檔案儲存體中的 SSIS 封裝時，才需要此規則。 |
||||||||

### <a name="route"></a>使用 Azure ExpressRoute 或 UDR
如果您想要檢查來自 Azure SSIS IR 的輸出流量，您可以將從 Azure SSIS IR 起始的流量路由傳送至內部部署防火牆應用裝置，透過[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)強制通道（將 BGP 路由、0.0.0.0/0 通知到虛擬網路），或透過[Udr](../virtual-network/virtual-networks-udr-overview.md)將網路虛擬裝置（NVA）作為防火牆或[Azure 防火牆](https://docs.microsoft.com/azure/firewall/)。 

![Azure SSIS IR 的 NVA 案例](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

您必須執行下列動作，才能讓整個案例運作
   -   Azure Batch 管理服務與 Azure SSIS IR 之間的輸入流量無法透過防火牆設備路由傳送。
   -   防火牆設備應允許 Azure SSIS IR 所需的輸出流量。

Azure Batch 管理服務與 Azure SSIS IR 之間的輸入流量無法路由傳送至防火牆設備，否則流量會因為非對稱式路由問題而中斷。 您必須針對輸入流量定義路由，讓流量可以回復其傳入的方式。 您可以定義特定 Udr，以使用下一個躍點類型作為**網際網路**，將 Azure Batch 管理服務和 AZURE SSIS IR 之間的流量路由傳送。

例如，如果您的 Azure SSIS IR 位於 `UK South`，而您想要檢查透過 Azure 防火牆的輸出流量，您可以先從服務標籤[ip 範圍下載連結](https://www.microsoft.com/download/details.aspx?id=56519)或透過服務標籤[探索 API](https://aka.ms/discoveryapi)取得服務標籤 `BatchNodeManagement.UKSouth` 的 ip 範圍清單。 然後套用下列相關 IP 範圍路由的 Udr，並將下一個躍點類型設為**網際網路**，以及使用下一個躍點類型做為**虛擬裝置**的 0.0.0.0/0 路由。

![Azure Batch UDR 設定](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 這種方法會產生額外的維護成本。 定期檢查 IP 範圍，並將新的 IP 範圍新增至您的 UDR，以避免中斷 Azure SSIS IR。 我們建議您每月檢查 IP 範圍，因為新 IP 出現在服務標籤時，IP 會花費另一個月生效。 

若要讓防火牆設備允許輸出流量，您必須允許輸出到下列埠，如同 NSG 輸出規則中的需求。
-   使用目的地作為 Azure 雲端服務的埠443。

    如果您使用 Azure 防火牆，您可以指定具有 AzureCloud 服務標籤的網路規則，否則您可能會允許在防火牆設備中使用 [目的地]。

-   目的地為 CRL 下載網站的埠80。

    您應允許以下的 Fqdn 作為 CRL （憑證撤銷清單）下載適用于 Azure SSIS IR 管理用途的憑證網站：
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    如果您使用具有不同 CRL 的憑證，建議您也將它們包含在其中。 您可以閱讀此資訊，以深入瞭解[憑證撤銷清單](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)。

    如果您不允許此流量，則在啟動 Azure SSIS IR 並失去檢查憑證撤銷清單的功能時，您可能會遇到效能降級，但不建議從安全性的觀點來看。

-   埠1433、11000-11999 與目的地為 Azure SQL （只有當虛擬網路中的 Azure SSIS IR 節點存取您的 SQL Database 伺服器所裝載的 SSISDB 時才需要）。

    如果您使用 Azure 防火牆，您可以使用 Azure SQL 服務標籤來指定網路規則，否則您可能會允許目的地作為防火牆應用裝置中的特定 Azure sql url。

-   目的地為 Azure 儲存體的埠445（只有在執行儲存于 Azure 檔案儲存體的 SSIS 套件時才需要）。

    如果您使用 Azure 防火牆，您可以指定具有儲存體服務標籤的網路規則，否則您可能會允許目的地作為防火牆應用裝置中的特定 Azure 檔案儲存體 url。

> [!NOTE]
> 針對 Azure SQL 和儲存體，如果您在子網上設定虛擬網路服務端點，則相同區域或配對區域中的 Azure SSIS IR 和 Azure Azure 儲存體 SQL 之間的流量，將會直接路由至 Microsoft Azure 骨幹網路而不是您的防火牆設備。

如果您不需要檢查 Azure SSIS IR 輸出流量的功能，您可以直接套用路由來強制所有流量到下一個躍點類型的**網際網路**：

-   在 Azure ExpressRoute 案例中，您可以在裝載 Azure SSIS IR 的子網上，將具有下一個躍點類型的 0.0.0.0/0 路由套用為「**網際網路**」。 
-   在 NVA 案例中，您可以修改在裝載 Azure SSIS IR 的子網上套用的現有 0.0.0.0/0 路由，從下一個躍點類型到**虛擬應用裝置**到**網際網路**。

![新增路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> 指定具有下一個躍點類型**網際網路**的路由，並不表示所有流量都會透過網際網路傳送。 只要目的地位址適用于 Azure 的其中一個服務，Azure 就會將流量直接路由傳送到 Azure 骨幹網路上的服務，而不是將流量路由傳送到網際網路。

### <a name="resource-group"></a>設定資源群組

Azure-SSIS IR 需要在與虛擬網路相同的資源群組下，建立特定的網路資源。 這些資源包括：
- Azure 負載平衡器，其名稱 *\<Guid >-azurebatch-cloudserviceloadbalancer*。
- Azure 公用 IP 位址，其名稱 *\<Guid >-azurebatch-cloudservicepublicip*。
- 網路工作安全性群組，其名稱 *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*。 

> [!NOTE]
> 您現在可以為 Azure SSIS IR 帶入自己的靜態公用 IP 位址。 在此案例中，我們只會在與您的靜態公用 IP 位址相同的資源群組下建立 Azure 負載平衡器和網路安全性群組，而不是虛擬網路。

當您的 Azure SSIS IR 啟動時，將會建立這些資源。 當您的 Azure SSIS IR 停止時，就會將它們刪除。 如果您為 Azure SSIS IR 攜帶自己的靜態公用 IP 位址，當您的 Azure SSIS IR 停止時，將不會刪除它們。 若要避免封鎖您的 Azure SSIS IR 停止，請勿在您的其他資源中重複使用這些網路資源。 

請確定在虛擬網路/靜態公用 IP 位址所屬的資源群組/訂用帳戶上，沒有資源鎖定。 如果您設定唯讀/刪除鎖定，啟動和停止您的 Azure SSIS IR 將會失敗，否則它將會停止回應。

請確定您沒有 Azure 原則可防止在虛擬網路/靜態公用 IP 位址所屬的資源群組/訂用帳戶底下建立下列資源： 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>常見問題集

- 如何保護我的 Azure SSIS IR 上針對輸入連接公開的公用 IP 位址？ 是否可以移除公用 IP 位址？
 
  現在，當您的 Azure SSIS IR 加入虛擬網路時，將會自動建立公用 IP 位址。 我們有 NIC 層級的 NSG，僅允許 Azure Batch 管理服務對您的 Azure SSIS IR 進行輸入連線。 您也可以指定輸入保護的子網層級 NSG。

  如果您不想要公開任何公用 IP 位址，請考慮將[自我裝載 IR 設定為 AZURE SSIS ir 的 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) ，而不是將您的 AZURE ssis ir 加入虛擬網路（如果適用于您的案例）。
 
- 我可以將我的 Azure SSIS IR 的公用 IP 位址新增至我的資料來源的防火牆允許清單嗎？

  您現在可以為 Azure SSIS IR 帶入自己的靜態公用 IP 位址。 在此情況下，您可以將您的 IP 位址新增至資料來源的防火牆允許清單。 您也可以考慮下列其他選項，以根據您的案例來保護從 Azure SSIS IR 進行的資料存取：

  - 如果您的資料來源是內部部署，將虛擬網路連線到內部部署網路，並將您的 Azure SSIS IR 加入至虛擬網路子網之後，您就可以將該子網的私人 IP 位址範圍新增至資料來源的防火牆允許清單.
  - 如果您的資料來源是支援虛擬網路服務端點的 Azure 服務，您可以在虛擬網路子網上設定虛擬網路服務端點，並將您的 Azure SSIS IR 加入該子網。 接著，您可以將具有該子網的虛擬網路規則新增至資料來源的防火牆。
  - 如果您的資料來源是非 Azure 雲端服務，您可以使用 UDR，透過靜態公用 IP 位址，將來自 Azure SSIS IR 的輸出流量路由傳送至 NVA/Azure 防火牆。 接著，您可以將 NVA/Azure 防火牆的靜態公用 IP 位址新增至資料來源的防火牆允許清單。
  - 如果上述選項都不符合您的需求，請考慮為[您的 AZURE SSIS ir 設定自我裝載 IR 作為 proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 接著，您可以將裝載自我裝載 IR 之電腦的靜態公用 IP 位址新增至資料來源的防火牆允許清單。

- 如果我想要攜帶自己的 Azure SSIS IR，為什麼需要提供兩個靜態公用位址？

  Azure-SSIS IR 會定期自動更新。 在升級期間會建立新的節點，而舊的節點將會被刪除。 不過，為了避免停機，舊的節點必須等到新的節點準備就緒後才會刪除。 因此，舊節點所使用的第一個靜態公用 IP 位址無法立即釋放，而我們需要您的第二個靜態公用 IP 位址，才能建立新的節點。

- 我已經為 Azure SSIS IR 帶入自己的靜態公用 IP 位址，但為什麼它仍然無法存取我的資料來源？

  - 確認兩個靜態公用 IP 位址都已新增至您的資料來源的防火牆允許清單。 每次您的 Azure SSIS IR 升級時，其靜態公用 IP 位址會在您所帶來的兩個位置之間切換。 如果您只將其中一個專案新增至允許清單，則 Azure SSIS IR 的資料存取會在其升級之後中斷。
  - 如果您的資料來源是 Azure 服務，請檢查您是否已使用虛擬網路服務端點來設定它。 如果是這種情況，從 Azure SSIS IR 到您資料來源的流量會切換為使用 Azure 服務所管理的私人 IP 位址，並將您自己的靜態公用 IP 位址新增至防火牆的允許清單，讓您的資料來源不會生效。

## <a name="azure-portal-data-factory-ui"></a>Azure 入口網站 (Data Factory UI)

本節說明如何使用 Azure 入口網站和 Data Factory UI，將現有的 Azure SSIS IR 加入虛擬網路（傳統或 Azure Resource Manager）。 

將您的 Azure SSIS IR 加入虛擬網路之前，您必須適當地設定虛擬網路。 請遵循適用于您的虛擬網路類型（傳統或 Azure Resource Manager）一節中的步驟。 然後依照第三節中的步驟，將您的 Azure SSIS IR 加入虛擬網路。 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>設定 Azure Resource Manager 的虛擬網路

請先使用入口網站來設定 Azure Resource Manager 虛擬網路，然後再嘗試將 Azure SSIS IR 加入其中。

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選取 [更多服務]。 篩選並選取 [虛擬網路]。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路] 頁面上，選取 [屬性]。 

1. 選取 [資源識別碼] 的 [複製] 按鈕，將虛擬網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 在左側功能表上，選取 [**子網**]。 請確定可用的位址數目大於您的 Azure SSIS IR 中的節點。 

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，您的訂閱會註冊 Azure Batch。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   1. 在 [Azure 入口網站] 的左側功能表上，選取 [**訂閱**]。 

   1. 選取您的訂用帳戶。 

   1. 在左側選取 [**資源提供者**]，並確認 [ **Microsoft Batch** ] 是已註冊的提供者。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="configure-a-classic-virtual-network"></a>設定傳統虛擬網路

請先使用入口網站來設定傳統虛擬網路，再嘗試將 Azure SSIS IR 加入其中。 

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

1. 選取 [更多服務]。 篩選並選取 [虛擬網路 (傳統)]。 

1. 篩選並選取清單中的 [虛擬網路]。 

1. 在 [虛擬網路 (傳統)] 頁面上，選取 [屬性]。 

   ![傳統虛擬網路資源識別碼](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. 選取 [資源識別碼] 的 [複製] 按鈕，將傳統網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。 

1. 在左側功能表上，選取 [**子網**]。 請確定可用的位址數目大於您的 Azure SSIS IR 中的節點。 

   ![虛擬網路中的可用位址數目](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. 將 **MicrosoftAzureBatch** 加入虛擬網路的**傳統虛擬機器參與者**角色。 

   1. 在左側功能表上，選取 [**存取控制（IAM）** ]，然後選取 [**角色指派**] 索引標籤。 

       ![[存取控制] 和 [新增] 按鈕](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. 選取 [新增角色指派]。

   1. 在 [**新增角色指派**] 頁面上，針對 [**角色**] 選取 [**傳統虛擬機器參與者**]。 在 [**選取**] 方塊中，貼上 [ **ddbf3205-c6bd-46ae-8127-60eb93363864**]，然後從搜尋結果清單中選取 [ **Microsoft Azure Batch** ]。 

       ![[新增角色指派] 頁面上的搜尋結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. 選取 [**儲存**] 以儲存設定並關閉頁面。 

       ![儲存存取設定](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. 確認您在參與者清單中看到 **Microsoft Azure Batch**。 

       ![確認 Azure Batch 存取](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，您的訂閱會註冊 Azure Batch。 (若在 Data Factory 入口網站中建立 Azure-SSIS IR，便會自動註冊 Azure Batch 提供者)。 

   1. 在 [Azure 入口網站] 的左側功能表上，選取 [**訂閱**]。 

   1. 選取您的訂用帳戶。 

   1. 在左側選取 [**資源提供者**]，並確認 [ **Microsoft Batch** ] 是已註冊的提供者。 

   ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>將 Azure-SSIS IR 加入虛擬網路

設定 Azure Resource Manager 虛擬網路或傳統虛擬網路之後，您可以將 Azure SSIS IR 加入至虛擬網路：

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。 

1. 在[Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [ **Data**factory]。 如果您在功能表上看不到 [ **Data** factory]，請選取 [**更多服務**]，然後在 [**智慧 + 分析**] 區段中選取 [ **Data**factory]。 

   ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在清單中選取具有 Azure SSIS IR 的 data factory。 您會看到資料處理站的首頁。 選取 [**作者 & 監視器**] 圖格。 您會在個別的索引標籤上看到 Data Factory UI。 

   ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在 Data Factory UI 中，切換至 [編輯] 索引標籤，選取 [連線]，然後切換至 [整合執行階段] 索引標籤。 

   ![[整合執行階段] 索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果您的 Azure SSIS IR 正在執行，請在 [**整合運行**時間] 清單的 [**動作**] 欄中，選取您的 azure Ssis IR 的 [**停止**] 按鈕。 您無法編輯您的 Azure SSIS IR，直到您將它停止為止。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在 [**整合運行**時間] 清單的 [**動作**] 欄中，選取您的 Azure SSIS IR 的 [**編輯**] 按鈕。 

   ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在 [整合執行時間設定] 面板上，選取 [**下一步]** 按鈕，前進到 [**一般設定**] 和 [ **SQL 設定**] 區段。 

1. 在 [ **Advanced Settings** ] （設定）區段上： 

   1. 選取 [**選取要加入的 AZURE SSIS Integration Runtime 的 VNet]、[允許 ADF 建立特定的網路資源] 和 [選擇性地攜帶您自己的靜態公用 IP 位址**] 核取方塊。 

   1. 針對 [訂用帳戶]，選取具有您的虛擬網路的 Azure 訂用帳戶。

   1. 針對 [位置]，選取整合執行階段的相同位置。

   1. 針對 [**類型**]，選取您的虛擬網路類型： [傳統] 或 [Azure Resource Manager]。 我們建議您選取 Azure Resource Manager 虛擬網路，因為傳統虛擬網路即將淘汰。

   1. 針對 **[VNet 名稱]** ，選取您虛擬網路的名稱。 它應該與用於具有虛擬網路服務端點或受控實例（具有私人端點）來裝載 SSISDB 的 Azure SQL Database 伺服器相同。 或者，它應該與連線到內部部署網路的同一個相同。 否則，它可以是任何虛擬網路，以將您自己的靜態公用 IP 位址帶入 Azure SSIS IR。

   1. 針對 [子網路名稱]，選取虛擬網路的子網路名稱。 它應該與您的 Azure SQL Database 伺服器用來裝載 SSISDB 的虛擬網路服務端點相同。 或者，它應該與您的受控實例所使用的子網不同，而私人端點會裝載 SSISDB。 否則，它可以是任何子網，以將您自己的靜態公用 IP 位址帶入 Azure SSIS IR。

   1. 選取 [**為您的 AZURE ssis Integration Runtime 的靜態公用 ip 位址**] 核取方塊，選擇是否要為 AZURE ssis IR 攜帶自己的靜態公用 ip 位址，以便在您的資料來源的防火牆上允許它們。

      如果您選取此核取方塊，請完成下列步驟。

      1. 針對 [**第一個靜態公用 ip 位址**]，選取符合您的 AZURE SSIS IR[需求](#publicIP)的第一個靜態公用 ip 位址。 如果您沒有任何專案，請按一下 [**建立新**連結]，在 Azure 入口網站上建立靜態公用 IP 位址，然後按一下這裡的 [重新整理] 按鈕，即可加以選取。
      
      1. 針對 [**第二個靜態公用 ip 位址**]，選取符合您的 AZURE SSIS IR[需求](#publicIP)的第二個靜態公用 ip 位址。 如果您沒有任何專案，請按一下 [**建立新**連結]，在 Azure 入口網站上建立靜態公用 IP 位址，然後按一下這裡的 [重新整理] 按鈕，即可加以選取。

   1. 選取 [ **VNet 驗證**]。 如果驗證成功，請選取 [**繼續**]。 

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. 在 [**摘要**] 區段上，檢查您的 AZURE SSIS IR 的所有設定。 然後選取 [**更新**]。

1. 在您的 Azure SSIS IR 的 [**動作**] 資料行中選取 [**啟動**] 按鈕，以啟動您的 azure ssis ir。 啟動加入虛擬網路的 Azure SSIS IR 需要約20到30分鐘的時間。 

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

您必須先設定虛擬網路，才能將 Azure SSIS IR 加入虛擬網路。 若要為您的 Azure SSIS IR 自動設定虛擬網路許可權和設定，以加入虛擬網路，請新增下列腳本：

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

您可以建立 Azure-SSIS IR，同時將其加入虛擬網路。 如需完整的腳本和指示，請參閱[建立 AZURE SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>將現有的 Azure-SSIS IR 加入虛擬網路

[建立 AZURE SSIS ir](create-azure-ssis-integration-runtime.md)一文會說明如何建立 AZURE ssis ir，並在相同的腳本中將其加入虛擬網路。 如果您已經有 Azure SSIS IR，請遵循下列步驟將其聯結至虛擬網路： 
1. 停止 Azure-SSIS IR。 
1. 設定要加入虛擬網路的 Azure-SSIS IR。 
1. 啟動 Azure-SSIS IR。 

### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR

您必須先停止 Azure SSIS IR，才能將它加入虛擬網路。 此命令會釋出其所有節點並停止計費：

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>進行虛擬網路設定讓 Azure-SSIS IR 可以加入

若要設定 Azure SSIS 將加入之虛擬網路的設定，請使用此腳本： 

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

若要將您的 Azure SSIS IR 加入虛擬網路，請執行 `Set-AzDataFactoryV2IntegrationRuntime` 命令： 

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

若要啟動 Azure SSIS IR，請執行下列命令： 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

此命令約需要 20 到 30 分鐘才能完成。

## <a name="next-steps"></a>後續步驟

如需有關 Azure SSIS IR 的詳細資訊，請參閱下列文章： 
- [AZURE SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供有關 IRs 的一般概念性資訊，包括 Azure SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本教學課程提供逐步指示，說明如何建立您的 Azure SSIS IR。 它會使用 Azure SQL Database 來裝載 SSIS 目錄。 
- [建立 AZURE SSIS IR](create-azure-ssis-integration-runtime.md)。 本文會針對教學課程進行擴充。 其中提供有關在虛擬網路中 Azure SQL Database 使用虛擬網路服務端點或受控實例來裝載 SSIS 目錄的指示。 說明如何將您的 Azure SSIS IR 加入虛擬網路。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文說明如何取得您的 Azure SSIS IR 的相關資訊。 它會提供所傳回信息的狀態原因。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文說明如何停止、啟動或刪除您的 Azure SSIS IR。 此外也會說明如何藉由新增節點來擴增 Azure-SSIS IR。