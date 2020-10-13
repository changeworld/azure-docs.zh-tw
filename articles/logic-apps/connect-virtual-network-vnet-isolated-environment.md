---
title: 使用 ISE 連線到 Azure 虛擬網路
description: 建立可從 Azure Logic Apps 存取 Azure 虛擬網路 (VNET) 的整合服務環境 (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 49248575cb10f3df746b9ba484244e4702fb5d72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369003"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>透過使用整合服務環境 (ISE) 從 Azure Logic Apps 連線至 Azure 虛擬網路

針對您邏輯應用程式與整合帳戶需存取 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的案例，建立[整合服務的環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是一種專用環境，會使用與「全域」多租用戶 Logic Apps 服務分開的專用儲存體和其他資源。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。 ISE 也會為您提供專用的靜態 IP 位址。 這些 IP 位址與邏輯應用程式在公用多租用戶服務中所共用的靜態 IP 位址不同。

當您建立 ISE 時，Azure 會將該 ISE「插入」Azure 虛擬網路，然後將 Logic Apps 服務部署到您的虛擬網路。 當您建立邏輯應用程式或整合帳戶時，請選取此 ISE 作為其位置。 然後，您的邏輯應用程式或整合帳戶就可以直接存取虛擬網路中的資源，例如：虛擬機器 (VM)、伺服器、系統及服務。

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 若要讓邏輯應用程式和整合帳戶可在 ISE 中一起運作，兩者都必須使用「相同的 ISE」作為其位置。

ISE 已增加執行期間、儲存體保留期、輸送量、HTTP 要求和回應逾時、訊息大小及自訂連接器要求的限制。 如需詳細資訊，請參閱 [Azure Logic Apps 的限制和設定](../logic-apps/logic-apps-limits-and-config.md)。 若要深入了解 ISE，請參閱[從 Azure Logic Apps 存取 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

本文說明如何使用 Azure 入口網站完成這些工作：

* 啟用 ISE 的存取。
* 建立您的 ISE。
* 將額外的容量新增至您的 ISE。

您也可以使用[範例 Azure Resource Manager 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)，或使用 Logic Apps REST API 來建立 ISE，包括設定客戶管理的金鑰：

* [使用 Logic Apps REST API 建立整合服務環境 (ISE)](../logic-apps/create-integration-service-environment-rest-api.md)
* [設定客戶管理的金鑰來加密 ISE 的待用資料](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

  > [!IMPORTANT]
  > Logic Apps、內建動作，以及在您 ISE 中執行的連接器，其使用的定價方案不同於耗用量式定價方案。 若要了解適用於 ISE 的定價和計費方式，請參閱 [Logic Apps 定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率，請參閱 [Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。

* [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。 您的虛擬網路必須有四個 *空* 的子網，這是在 ISE 中建立和部署資源所需的子網，並且由這些內部和隱藏元件使用：

  * Logic Apps 計算
  * 內部 App Service 環境 (連接器) 
  * 內部 API 管理 (連接器) 
  * 快取和效能的內部 Redis
  
  您可以事先建立子網，也可以等到您建立 ISE 之後，才能同時建立子網。 不過，在您建立子網之前，請先檢查 [子網需求](#create-subnet)。

  > [!IMPORTANT]
  >
  > 請勿將下列 IP 位址空間用於您的虛擬網路或子網，因為 Azure Logic Apps 無法解析這些 IP 位址空間：<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * 確定您的虛擬網路[啟用 ISE 的存取](#enable-access)，讓您的 ISE 能夠正常運作並保持可存取狀態。

  * 如果您使用或想要使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 搭配 [強制通道](../firewall/forced-tunneling.md)，您必須建立具有下列特定路由的 [路由表](../virtual-network/manage-route-table.md) ，並將路由表連結至 ISE 所使用的每個子網：

    **名稱**：<*route-name*><br>
    **位址首碼**：0.0.0.0/0<br>
    **下一個躍點**：Internet
    
    這是必要的路由表，因此 Logic Apps 元件可以與其他相依的 Azure 服務（例如 Azure 儲存體和 Azure SQL DB）進行通訊。 如需此路由的詳細資訊，請參閱 [0.0.0.0/0 位址首碼](../virtual-network/virtual-networks-udr-overview.md#default-route)。 如果您未搭配 ExpressRoute 使用強制通道，則不需要此特定的路由表。
    
    ExpressRoute 可讓您將內部部署網路延伸至 Microsoft 雲端，並透過連線提供者所提供的私人連線來連接到 Microsoft 雲端服務。 具體而言，ExpressRoute 是一種虛擬私人網路，可透過私人網路路由傳送流量，而不是透過公用網際網路。 當您的邏輯應用程式透過 ExpressRoute 或虛擬私人網路連線時，您的邏輯應用程式可以連線至位於相同虛擬網路中的內部部署資源。
   
  * 如果您使用 [網路虛擬裝置 (NVA) ](../virtual-network/virtual-networks-udr-overview.md#user-defined)，請確定您未啟用 TLS/ssl 終止或變更輸出 TLS/ssl 流量。 此外，請確定您不會對源自 ISE 子網的流量啟用檢查。 如需詳細資訊，請參閱 [虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。

  * 如果您想要將自訂 DNS 伺服器用於您的 Azure 虛擬網路，請[遵循下列步驟設定這些伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，然後再將您的 ISE 部署至您的虛擬網路。 如需管理 DNS 伺服器設定的詳細資訊，請參閱[建立、變更或刪除虛擬網路](../virtual-network/manage-virtual-network.md#change-dns-servers)。

    > [!NOTE]
    > 如果您變更 DNS 伺服器或 DNS 伺服器設定，您必須重新開機 ISE，讓 ISE 可以挑選這些變更。 如需相關資訊，請參閱[重新啟動您的 ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)。

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>啟用 ISE 的存取

當您搭配 Azure 虛擬網路使用 ISE 時，常見的設定問題是有一或多個封鎖的連接埠。 用於在您 ISE 與目的地系統之間建立連線的連接器，可能也有其自己的連接埠需求。 例如，如果您使用 FTP 連接器與 FTP 系統進行通訊，則您在 FTP 系統上使用的連接埠需要可供使用，例如，傳送命令的連接埠 21。

若要確保您的 ISE 可供存取，且該 ISE 中的邏輯應用程式可以跨虛擬網路中的每個子網路進行通訊，請[針對每個子網路開啟此資料表中所述的連接埠](#network-ports-for-ise)。 如果有任何必要的連接埠無法使用，您的 ISE 將無法正常運作。

* 如果您有多個 ISE 執行個體需要存取具有 IP 限制的其他端點，請將 [Azure 防火牆](../firewall/overview.md)或[網路虛擬設備](../virtual-network/virtual-networks-overview.md#filter-network-traffic) 部署到虛擬網路，並透過該防火牆或網路虛擬裝置來路由輸出流量。 接著，您可以[設定單一、輸出、公用、靜態和可預測的 IP 位址，](connect-virtual-network-vnet-set-up-single-ip-address.md) 虛擬網路中的所有 ISE 執行個體都可以將其用來與目的地系統進行通訊。 如此一來，您就不需要在每個 ISE 的目的地系統上設定額外的開放式防火牆。

   > [!NOTE]
   > 當您的案例需要限制需要存取的 IP 位址數目時，您可以將此方法用於單一 ISE。 請考慮防火牆或虛擬網路設備的額外成本對您的案例是否有意義。 深入了解 [Azure 防火牆定價](https://azure.microsoft.com/pricing/details/azure-firewall/)。

* 如果您已建立新的 Azure 虛擬網路和子網路，但沒有任何條件約束，則您不需要在虛擬網路中設定[網路安全性群組 (NSG)](../virtual-network/security-overview.md#network-security-groups) 來控制子網路之間的流量。

* 針對現有的虛擬網路，您可以「選擇性地」設定[網路安全性群組 (NSG)](../virtual-network/security-overview.md#network-security-groups) 來[篩選跨子網路的網路流量](../virtual-network/tutorial-filter-network-traffic.md)。 如果您想要前往此路由，或如果您已在使用 NSG，請確定您已針對這些 NSG [開啟此資料表中所述的連接埠](#network-ports-for-ise)。

  當設定 [NSG 安全性規則](../virtual-network/security-overview.md#security-rules)時，您必須「同時」使用 **TCP** 和 **UDP** 通訊協定，或者您可以改為選取 [任何]，讓您不必為每個通訊協定建立個別規則。 NSG 安全性規則描述您必須針對需要存取這些連接埠的 IP 位址開啟哪些埠。 確定在這些端點之間存在的任何防火牆、路由器或其他項目，也會讓這些連接埠可供這些 IP 位址存取。

* 如果您透過防火牆設定強制通道來重新導向網際網路系結流量，請參閱 [其他強制通道需求](#forced-tunneling)。

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE 使用的網路連接埠

下表描述 ISE 需要存取的連接埠，以及這些連接埠的用途。 為了協助降低您設定安全性規則時的複雜性，資料表會使用[服務標籤](../virtual-network/service-tags-overview.md)，代表特定 Azure 服務的 IP 位址首碼群組。 請注意，「內部 ISE」和「外部 ISE」指的是[在 ISE 建立期間選取的存取端點](connect-virtual-network-vnet-isolated-environment.md#create-environment)。 如需詳細資訊，請參閱[端點存取](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。

> [!IMPORTANT]
> 針對所有規則，請務必將來源連接埠設定為 `*`，因為來源連接埠是暫時的。

#### <a name="inbound-security-rules"></a>輸入安全性規則

| 目的 | 來源服務標籤或 IP 位址 | 來源連接埠 | 目的地服務標籤或 IP 位址 | 目的地連接埠 | 注意 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 虛擬網路內的子網路間通訊 | ISE 子網路內虛擬網路的位址空間 | * | ISE 子網路內虛擬網路的位址空間 | * | 對於要在虛擬網路中子網路「之間」流動的流量，這是必要的。 <p><p>**重要**：如需流量在每個子網路中的「元件」之間流動，請確定您已開啟每個子網路內的所有連接埠。 |
| Both (兩者)： <p>與您的邏輯應用程式進行通訊 <p><p>邏輯應用程式的執行歷程記錄| 內部 ISE： <br>**VirtualNetwork** <p><p>外部 ISE：**網際網路**或參閱**附註** | * | **VirtualNetwork** | 443 | 您可以指定這些項目的來源 IP 位址，而不是使用**網際網路**服務標籤： <p><p>- 呼叫邏輯應用程式中任何要求觸發程序或 Webhook 的電腦或服務 <p>- 您想要從中存取邏輯應用程式執行歷程記錄的電腦或服務 <p><p>**重要**：關閉或封鎖此連接埠可防止呼叫具有要求觸發程序或 Webhook 的邏輯應用程式。 也會防止您在執行歷程記錄中存取每個步驟的輸入和輸出。 不過，不會防止您存取邏輯應用程式執行歷程記錄。|
| Logic Apps 設計工具 - 動態屬性 | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | 對於該區域，要求來自於 Logic Apps 存取端點的[輸入 IP 位址](../logic-apps/logic-apps-limits-and-config.md#inbound)。 |
| 連接器部署 | **AzureConnectors** | * | **VirtualNetwork** | 454 | 需要部署和更新連接器。 關閉或封鎖此連接埠會導致 ISE 部署失敗，並阻止連接器更新和修正。 |
| 網路健康狀態檢查 | **LogicApps** | * | **VirtualNetwork** | 454 | 對於該區域，要求來自於 Logic Apps 存取端點的[輸入 IP 位址](../logic-apps/logic-apps-limits-and-config.md#inbound)和[輸出 IP 位址](../logic-apps/logic-apps-limits-and-config.md#outbound)。 |
| App Service 管理相依性 | **AppServiceManagement** | * | **VirtualNetwork** | 454、455 ||
| 來自 Azure 流量管理員的通訊 | **AzureTrafficManager** | * | **VirtualNetwork** | 內部 ISE：454 <p><p>外部 ISE：443 ||
| Both (兩者)： <p>連接器原則部署 <p>API 管理 - 管理端點 | **APIManagement** | * | **VirtualNetwork** | 3443 | 如需部署連接器原則，需要存取連接埠來部署和更新連接器。 關閉或封鎖此連接埠會導致 ISE 部署失敗，並阻止連接器更新和修正。 |
| 針對角色執行個體之間的 Redis 執行個體存取 Azure 快取 | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383，請參閱**附註**| 為了讓 ISE 能夠使用 Azure Cache for Redis，您必須開啟這些[由 Azure Cache for Redis 描述的輸出和輸入連接埠](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
|||||||

#### <a name="outbound-security-rules"></a>輸出安全性規則

| 目的 | 來源服務標籤或 IP 位址 | 來源連接埠 | 目的地服務標籤或 IP 位址 | 目的地連接埠 | 注意 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 虛擬網路內的子網路間通訊 | ISE 子網路內虛擬網路的位址空間 | * | ISE 子網路內虛擬網路的位址空間 | * | 對於要在虛擬網路中子網路「之間」流動的流量，這是必要的。 <p><p>**重要**：如需流量在每個子網路中的「元件」之間流動，請確定您已開啟每個子網路內的所有連接埠。 |
| 來自邏輯應用程式的通訊 | **VirtualNetwork** | * | 根據目的地而有所不同 | 80、443 | 目的地會根據外部服務的端點而有所不同，而您的邏輯應用程式需要與此服務進行通訊。 |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80、443 ||
| Azure 儲存體相依性 | **VirtualNetwork** | * | **Storage** | 80、443、445 ||
| 連線管理 | **VirtualNetwork** | * | **AppService** | 443 ||
| 發佈診斷記錄和計量 | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL 相依性 | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure 資源健康狀態 | **VirtualNetwork** | * | **AzureMonitor** | 1886 | 需要將健康狀態發佈至資源健康狀態。 |
| 「記錄到事件中樞」原則和監視代理程式的相依性 | **VirtualNetwork** | * | **EventHub** | 5672 ||
| 針對角色執行個體之間的 Redis 執行個體存取 Azure 快取 | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383，請參閱**附註**| 為了讓 ISE 能夠使用 Azure Cache for Redis，您必須開啟這些[由 Azure Cache for Redis 描述的輸出和輸入連接埠](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
| DNS 名稱解析 | **VirtualNetwork** | * | 任何自訂網域名稱系統的 IP 位址 (您虛擬網路上的 DNS) 伺服器 | 53 | 只有當您在虛擬網路上使用自訂 DNS 伺服器時才需要 |
|||||||

此外，您還需要新增 [App Service 環境 (ASE) ](../app-service/environment/intro.md)的輸出規則：

* 如果您使用 Azure 防火牆，您需要使用 App Service 環境 (ASE) [完整功能變數名稱 (FQDN) 標記](../firewall/fqdn-tags.md#current-fqdn-tags)來設定您的防火牆，以允許對 ASE 平臺流量進行輸出存取。

* 如果您使用 Azure 防火牆以外的防火牆應用裝置，您必須使用 App Service 環境所需的[防火牆整合](../app-service/environment/firewall-integration.md#dependencies)相依性中列出的*所有*規則來設定您的防火牆。

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>強制通道需求

如果您透過防火牆設定或使用 [強制通道](../firewall/forced-tunneling.md) ，您必須允許 ISE 的其他外部相依性。 強制通道可讓您將網際網路系結流量重新導向至指定的下一個躍點，例如虛擬私人網路 (VPN) 或虛擬裝置（而不是網際網路），如此您就可以檢查和審核輸出網路流量。

通常，所有 ISE 輸出相依性流量都會透過您 ISE 所布建的虛擬 IP 位址 (VIP) 來傳輸。 但是，如果您將流量路由變更至 ISE 或從 ISE 變更流量路由，則必須將下一個躍點設定為，以允許防火牆上的下列輸出相依性 `Internet` 。 如果您使用 Azure 防火牆，請遵循 [指示來設定您的防火牆，並使用您的 App Service 環境](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)。

如果您不允許存取這些相依性，您的 ISE 部署將會失敗，且您部署的 ISE 會停止運作：

* [App Service Environment 管理位址](../app-service/environment/management-addresses.md)

* [Azure API 管理位址](../api-management/api-management-using-with-vnet.md#control-plane-ips)

* [Azure 流量管理員管理位址](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)

* [ISE 區域 Logic Apps 輸入和輸出位址](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)

* [ISE 區域中連接器的 Azure IP 位址，位於此下載檔案](https://www.microsoft.com/download/details.aspx?id=56519)

* 您必須啟用 Azure SQL、儲存體、服務匯流排和事件中樞的服務端點，因為您無法透過防火牆將流量傳送至這些服務。

<a name="create-environment"></a>

## <a name="create-your-ise"></a>建立您的 ISE

1. 在 [Azure 入口網站](https://portal.azure.com)的主要 Azure 搜尋服務方塊中，輸入 `integration service environments` 作為篩選條件，然後選取 [整合服務環境]。

   ![尋找並選取 [整合服務環境]](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在 [整合服務環境] 窗格上，選取 [新增]。

   ![選取 [新增] 以建立整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 為您的環境提供這些詳細資料，然後選取 [檢閱 + 建立]，例如：

   ![提供環境詳細資料](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **訂用帳戶** | 是 | <*Azure-subscription-name*> | 要用於環境的 Azure 訂用帳戶 |
   | **資源群組** | 是 | <*Azure-resource-group-name*> | 新的或現有的 Azure 資源群組，您想要在其中建立您的環境 |
   | **整合服務環境名稱** | 是 | <*environment-name*> | 您的 ISE 名稱，其中只能包含字母、數字、連字號 (`-`)、底線 (`_`) 和 (`.`)。 |
   | **位置** | 是 | <*Azure-datacenter-region*> | 要用來部署環境的 Azure 資料中心區域 |
   | **SKU** | 是 | **進階**或**開發人員 (沒有 SLA)** | 要建立和使用的 ISE SKU。 如需這些 SKU 之間的差異，請參閱 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 <p><p>**重要**：只有在建立 ISE 時才可使用此選項，而且稍後無法變更。 |
   | **額外容量** | 進階： <br>是 <p><p>開發人員： <br>不適用 | 進階： <br>0 到 10 <p><p>開發人員： <br>不適用 | 此 ISE 資源要額外使用的處理單位數。 若要在建立後新增容量，請參閱[新增 ISE 容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 |
   | **存取端點** | 是 | **內部**或**外部** | 要用於 ISE 的存取端點類型。 這些端點會判斷 ISE 中邏輯應用程式上的要求或 Webhook 觸發程序是否可以接收來自您虛擬網路外部的呼叫。 <p><p>您的選擇也會影響您可以在邏輯應用程式執行歷程記錄中檢視和存取輸入和輸出的方式。 如需詳細資訊，請參閱 [ISE 端點存取](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 <p><p>**重要**：您只能在 ISE 建立期間選取存取端點，而且稍後無法變更此選項。 |
   | **虛擬網路** | 是 | <*Azure-virtual-network-name*> | 要插入環境的 Azure 虛擬網路，讓該環境中的邏輯應用程式可以存取虛擬網路。 如果您沒有網路，請[先建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。 <p><p>**重要**：您「只」可以在建立您的 ISE 時執行此插入作業。 |
   | **子網路** | 是 | <*subnet-resource-list*> | ISE 需要四個 *空白* 子網，這是在您的 ISE 中建立和部署資源所需的子網，供內部 Logic Apps 元件使用，例如連接器和快取效能。 <p>**重要**事項：請務必 [先檢查子網需求，再繼續進行這些步驟來建立您的子網](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **建立子網**

   您的 ISE 需要四個 *空白* 子網，這是在 ISE 中建立和部署資源所需的子網，供內部 Logic Apps 元件使用，例如連接器和快取效能。 您在建立環境之後「無法」變更這些子網路位址。 如果您透過 Azure 入口網站建立並部署 ISE，請確定您不會將這些子網委派給任何 Azure 服務。 但是，如果您透過 REST API、Azure PowerShell 或 Azure Resource Manager 範本來建立及部署 ISE，則需要將一個空的子網 [委派](../virtual-network/manage-subnet-delegation.md) 給 `Microsoft.integrationServiceEnvironment` 。 如需詳細資訊，請參閱 [新增子網委派](../virtual-network/manage-subnet-delegation.md)。

   每個子網路都必須符合下列需求：

   * 使用以字母字元或底線 (開頭的名稱，不) 數位，且不會使用這些字元： `<` 、 `>` 、 `%` 、 `&` 、 `\\` 、 `?` 、 `/` 。

   * 使用[無類別網域間路由選擇 (CIDR) 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)及類別 B 位址空間。
   
     > [!IMPORTANT]
     >
     > 請勿將下列 IP 位址空間用於您的虛擬網路或子網，因為 Azure Logic Apps 無法解析這些 IP 位址空間：<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * 使用位址空間中的 `/27`，因為每個子網路都需要 32 個位址。 例如，`10.0.0.0/27` 有 32 個位址，因為 2<sup>(32-27)</sup> 是 2<sup>5</sup> 或 32。 有更多的位址不會提供額外的權益。 若要深入了解如何計算位址，請參閱 [IPv4 CIDR 區塊](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果使用 [ExpressRoute](../expressroute/expressroute-introduction.md)，您必須[建立具有下列路由的路由表](../virtual-network/manage-route-table.md)，並將該路由表與 ISE 所使用的每個子網路連結在一起：

     **名稱**：<*route-name*><br>
     **位址首碼**：0.0.0.0/0<br>
     **下一個躍點**：Internet

   1. 在 [子網路] 清單底下，選取 [管理子網路設定]。

      ![管理子網路設定](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. 在 [子網路] 窗格中，選取 [子網路]。

      ![新增四個空白子網路](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. 在 [新增子網路] 窗格中，提供這項資訊。

      * **Name**：您的子網路名稱
      * **位址範圍 (CIDR 區塊)** ：在您虛擬網路中且使用 CIDR 格式的子網路範圍

      ![新增子網路詳細資料](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 完成後，選取 [確定]。

   1. 針對其他三個子網路重複這些步驟。

      > [!NOTE]
      > 如果您嘗試建立的子網路無效，Azure 入口網站會顯示一則訊息，但不會封鎖您的進度。

   如需有關建立子網路的詳細資訊，請參閱[新增虛擬網路子網路](../virtual-network/virtual-network-manage-subnet.md)。

1. 在 Azure 成功驗證您的 ISE 資訊之後，選取 [建立]，例如：

   ![驗證成功之後，選取 [建立]](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 會開始部署您的環境，這通常會在兩個小時內完成。 有時候，部署需要的時間可能高達四小時。 若要檢查部署狀態，請在 Azure 工具列上選取通知圖示，這會開啟 [通知] 窗格。

   ![檢查部署狀態](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   成功完成部署時，Azure 就會顯示此通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   否則，請遵循 Azure 入口網站指示，針對部署進行疑難排解。

   > [!NOTE]
   > 如果部署失敗或您刪除 ISE，則在釋出子網之前，Azure 可能需要最多一小時的時間，或可能較長的時間。 因此，您可能必須等待，才能在另一個 ISE 中重複使用這些子網。
   >
   > 如果您刪除虛擬網路，Azure 通常最多需要兩小時的時間，才能釋出您的子網路，但此作業可能需要更長的時間。 
   > 刪除虛擬網路時，請確定沒有任何資源仍處於連線狀態。 
   > 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 如果 Azure 在部署完成之後不會自動移至您的環境，且若要檢視您的環境，請選取 [移至資源]。

1. 若要檢查 ISE 的網路健康情況，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)。

1. 若要開始在 ISE 中建立邏輯應用程式和其他成品，請參閱[將資源新增到整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

   > [!IMPORTANT]
   > 在您建立 ISE 之後變成可用的受控 ISE 連接器不會自動出現在邏輯應用程式設計工具上的連接器選擇器中。 在您可以使用這些 ISE 連接器之前，您必須手動[將這些連接器新增至您的 ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)，讓其可以出現在邏輯應用程式設計工具中。

   > [!IMPORTANT]
   > 受控 ISE 連接器目前不支援 [標記](../azure-resource-manager/management/tag-support.md)。 如果您設定了強制標記的原則，請嘗試新增 ISE 連接器  
   > 可能會失敗，並出現類似以下範例的錯誤： 
   > 
   > ```json
   > {
   >    "error": { 
   >       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
   >       "message": "The tags are not supported in the managed API 'azureblob'."
   >    }
   > }
   > ```
   > 若要新增 ISE 連接器，您必須停用或移除原則。
   > 

## <a name="next-steps"></a>後續步驟

* [將資源新增到整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
