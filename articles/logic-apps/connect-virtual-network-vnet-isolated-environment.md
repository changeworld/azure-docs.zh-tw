---
title: 使用 ISE 連接到 Azure 虛擬網路
description: 創建整合服務環境 （ISE），該環境可以從 Azure 邏輯應用訪問 Azure 虛擬網路 （VNET）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fedc1f6ce8fbaeaf0d2cae3a1b04169192868e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270689"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>透過使用整合服務環境 (ISE) 從 Azure Logic Apps 連線至 Azure 虛擬網路

對於邏輯應用和集成帳戶需要訪問[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的情況，請創建[*整合服務環境*（ISE）。](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ISE 是一個隔離的環境，它使用專用存儲和其他資源，這些資源與"全域"多租戶邏輯應用服務分開。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。 ISE 還為您提供自己的靜態 IP 位址。 這些 IP 位址與公共多租戶服務中的邏輯應用共用的靜態 IP 位址分開。

創建 ISE 時，Azure*會將*該 ISE 注入 Azure 虛擬網路，然後將邏輯應用服務部署到虛擬網路。 創建邏輯應用或集成帳戶時，選擇 ISE 作為其位置。 然後，您的邏輯應用程式或整合帳戶就可以直接存取虛擬網路中的資源，例如：虛擬機器 (VM)、伺服器、系統及服務。

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 對於邏輯應用和集成帳戶在 ISE 中協同工作，兩者必須使用*與其位置相同的 ISE。*

ISE 增加了對運行持續時間、存儲保留、輸送量、HTTP 要求和回應超時、消息大小和自訂連接器請求的限制。 有關詳細資訊，請參閱[Azure 邏輯應用的限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 要瞭解有關 ISEs 的更多資訊，請參閱[從 Azure 邏輯應用訪問 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

本文演示如何使用 Azure 門戶完成這些任務：

* 啟用 ISE 的訪問。
* 創建 ISE。
* 為您的 ISE 添加額外的容量。

您還可以使用邏輯應用 REST API 創建 ISE，包括設置客戶管理的金鑰：

* [使用邏輯應用 REST API 創建整合服務環境 （ISE）](../logic-apps/create-integration-service-environment-rest-api.md)
* [設置客戶管理的金鑰以加密 ISEs 靜態資料](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

  > [!IMPORTANT]
  > 在 ISE 中運行的邏輯應用、內置觸發器、內置操作和連接器使用不同于基於消耗的定價計畫的定價計畫。 要瞭解 ISEs 的定價和計費的工作原理，請參閱[邏輯應用定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有關定價率，請參閱[邏輯應用定價](../logic-apps/logic-apps-pricing.md)。

* [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。 如果您沒有虛擬網路，請了解如何[建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。

  * 虛擬網路需要四個*空*子網來創建和部署 ISE 中的資源。 每個子網都支援 ISE 中使用的不同邏輯應用元件。 您可以提前創建這些子網，也可以等到創建 ISE，以便可以同時創建子網。 瞭解有關[子網要求](#create-subnet)的更多。

  * 子網名稱需要以字母字元或底線開頭，`<`不能使用這些字元： `>`、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `%` `&` `\\` `?` `/`、 、 、 、 、 、 、 、 
  
  * 如果要通過 Azure 資源管理器範本部署 ISE，請確保將一個空子網委派給 Microsoft.Logic/整合服務環境。 通過 Azure 門戶進行部署時，不需要執行此委派。

  * 確保虛擬網路[支援對 ISE 的訪問](#enable-access)，以便 ISE 能夠正常工作並保持可訪問性。

  * 如果使用[ExpressRoute](../expressroute/expressroute-introduction.md)，它提供連接供應商提供的與 Microsoft 雲服務的專用連線，則必須[創建具有以下路由的路由表](../virtual-network/manage-route-table.md)，並將該錶鏈接到 ISE 使用的每個子網：

    **名稱**： <*路由名稱*><br>
    **位址首碼**： 0.0.0.0/0<br>
    **下一跳**： 互聯網

* 如果要為 Azure 虛擬網路使用自訂 DNS 伺服器，請[按照以下步驟設置這些伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，然後再將 ISE 部署到虛擬網路。 有關管理 DNS 伺服器設置的詳細資訊，請參閱[創建、更改或刪除虛擬網路](../virtual-network/manage-virtual-network.md#change-dns-servers)。

  > [!NOTE]
  > 如果更改 DNS 伺服器或 DNS 伺服器設置，必須重新開機 ISE，以便 ISE 可以選取這些更改。 有關詳細資訊，請參閱重新開機[ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)。

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>啟用 ISE 的存取

將 ISE 與 Azure 虛擬網路一起使用時，常見的設置問題是存在一個或多個被阻止的埠。 用於在 ISE 和目標系統之間創建連接的連接器也可能有自己的埠要求。 例如，如果使用 FTP 連接器與 FTP 系統通信，則需要在 FTP 系統上使用的埠可用，例如用於發送命令的埠 21。

為了確保 ISE 是可訪問的，並且 ISE 中的邏輯應用可以跨虛擬網路中的每個子網進行通信，[請為每個子網打開此表中描述的埠](#network-ports-for-ise)。 如果任何必需的埠不可用，則 ISE 將無法正常工作。

* 如果有多個 ISE 實例需要訪問具有 IP 限制的其他終結點，請將[Azure 防火牆](../firewall/overview.md)或[網路虛擬裝置](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署到虛擬網路，並通過該防火牆或網路虛擬裝置路由出站流量。 然後，您可以[設置單個、出站、公共、靜態和可預測的 IP 位址，](connect-virtual-network-vnet-set-up-single-ip-address.md)虛擬網路中的所有 ISE 實例都可以使用該位址與目標系統通信。 這樣，您就不必為每個 ISE 在這些目標系統設置額外的防火牆打開。

   > [!NOTE]
   > 當方案需要限制需要訪問的 IP 位址數時，可以將此方法用於單個 ISE。 考慮防火牆或虛擬網路設備的額外成本是否適合您的方案。 瞭解有關[Azure 防火牆定價](https://azure.microsoft.com/pricing/details/azure-firewall/)的資訊。

* 如果創建新的 Azure 虛擬網路和子網沒有任何約束，則無需在虛擬網路中設置[網路安全性群組 （NSG）](../virtual-network/security-overview.md#network-security-groups)即可控制跨子網的流量。

* 在現有虛擬網路上，您可以通過[篩選跨子網的網路流量](../virtual-network/tutorial-filter-network-traffic.md)*來選擇*設置 NSG。 如果要走此路線，或者如果您已經在使用 NSG，請確保[打開虛擬網路上的此表中的埠](#network-ports-for-ise)，其中具有 NSG 或想要設置 NSG。

  > [!NOTE]
  > 如果使用[NSG 安全規則](../virtual-network/security-overview.md#security-rules)，則需要*同時*使用 TCP 和 UDP 協定。 NSG 安全規則描述必須為需要訪問這些埠的 IP 位址打開的埠。 確保這些終結點之間存在的任何防火牆、路由器或其他專案也會使這些埠對這些 IP 位址具有可訪問性。

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE 使用的網路埠

此表介紹 ISE 使用的 Azure 虛擬網路中的埠以及這些埠的使用位置。 為了説明降低創建安全規則時的複雜性，表中[的服務標記](../virtual-network/service-tags-overview.md)表示特定 Azure 服務的 IP 位址首碼組。

> [!IMPORTANT]
> 源埠是短暫的，因此請確保`*`為所有規則設置它們。 如果注意到，內部 ISE 和外部 ISE 是指[在 ISE 創建時選擇的終結點](connect-virtual-network-vnet-isolated-environment.md#create-environment)。 有關詳細資訊，請參閱[終結點訪問](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 

| 目的 | 方向 | 目的地連接埠 | 來源服務標籤 | 目的地服務標記 | 注意 |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| 虛擬網路中的子網間通信 | 輸入和輸出 | * | 具有 ISE 子網的虛擬網路的位址空間 | 具有 ISE 子網的虛擬網路的位址空間 | 需要流量才能在虛擬網路中的子網*之間*流動。 <p><p>**重要提示**：要使流量在每個子網中的*元件*之間流動，請確保打開每個子網中的所有埠。 |
| 與邏輯應用的通信 | 輸入 | 443 | 內部 ISE： <br>VirtualNetwork <p><p>外部 ISE： <br>Internet <br>（請參閱**備註**列） | VirtualNetwork | 您可以指定邏輯應用中調用任何請求觸發器或 Webhook 的電腦或服務的源 IP 位址，而不是使用**Internet**服務標記。 <p><p>**重要提示**：關閉或阻止此埠可防止對具有請求觸發器的邏輯應用進行 HTTP 調用。 |
| 邏輯應用執行歷程記錄 | 輸入 | 443 | 內部 ISE： <br>VirtualNetwork <p><p>外部 ISE： <br>Internet <br>（請參閱**備註**列） | VirtualNetwork | 您可以指定電腦或服務的源 IP 位址，而不是使用**Internet**服務標記，從中查看邏輯應用的執行歷程記錄。 <p><p>**重要提示**：雖然關閉或阻止此埠不會阻止您查看執行歷程記錄，但無法查看該執行歷程記錄中每個步驟的輸入和輸出。 |
| Logic Apps 設計工具 - 動態屬性 | 輸入 | 454 | 邏輯應用管理 | VirtualNetwork | 請求來自邏輯應用訪問該區域[入站](../logic-apps/logic-apps-limits-and-config.md#inbound)IP 位址。 |
| 連接器部署 | 輸入 | 454 | Azure 連接器 | VirtualNetwork | 部署和更新連接器所需的。 關閉或阻止此埠會導致 ISE 部署失敗，並阻止連接器更新或修復。 |
| 網路運行狀況檢查 | 輸入 | 454 | LogicApps | VirtualNetwork | 請求來自該區域[入站](../logic-apps/logic-apps-limits-and-config.md#inbound)和[出站](../logic-apps/logic-apps-limits-and-config.md#outbound)IP 位址的邏輯應用訪問終結點。 |
| App Service 管理相依性 | 輸入 | 454、455 | AppServiceManagement | VirtualNetwork | |
| 來自 Azure 流量管理器的通信 | 輸入 | 內部 ISE： 454 <p><p>外部 ISE： 443 | AzureTrafficManager | VirtualNetwork | |
| API 管理 - 管理端點 | 輸入 | 3443 | APIManagement | VirtualNetwork | |
| 連接器策略部署 | 輸入 | 3443 | APIManagement | VirtualNetwork | 部署和更新連接器所需的。 關閉或阻止此埠會導致 ISE 部署失敗，並阻止連接器更新或修復。 |
| 來自邏輯應用的通信 | 輸出 | 80、443 | VirtualNetwork | 因目的地而異 | 邏輯應用需要與其通信的外部服務的終結點。 |
| Azure Active Directory | 輸出 | 80、443 | VirtualNetwork | AzureActiveDirectory | |
| 連線管理 | 輸出 | 443 | VirtualNetwork  | AppService | |
| 發佈診斷記錄和計量 | 輸出 | 443 | VirtualNetwork  | AzureMonitor | |
| Azure 儲存體相依性 | 輸出 | 80, 443, 445 | VirtualNetwork | 存放裝置 | |
| Azure SQL 依賴項 | 輸出 | 1433 | VirtualNetwork | SQL | |
| Azure 資源健康狀態 | 輸出 | 1886 | VirtualNetwork | AzureMonitor | 將運行狀況發佈到資源運行狀況所需的 |
| 「記錄到事件中樞」原則和監視代理程式的相依性 | 輸出 | 5672 | VirtualNetwork | EventHub | |
| 針對角色執行個體之間的 Redis 執行個體存取 Azure 快取 | 輸入 <br>輸出 | 6379 - 6383 | VirtualNetwork | VirtualNetwork | 此外，要使 ISE 與 Redis 的 Azure 緩存配合使用，必須打開[雷迪斯常見問題 解答的 Azure 緩存中描述的這些出站和入站埠](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>建立您的 ISE

1. 在[Azure 門戶](https://portal.azure.com)中，在主 Azure`integration service environments`搜索框中，輸入為篩選器，然後選擇**整合服務環境**。

   ![查找並選擇"整合服務環境"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在 **"整合服務環境**"窗格中，選擇 **"添加**"。

   ![查找並選擇"整合服務環境"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 為您的環境提供這些詳細資訊，然後選擇 **"審閱 + 創建**"，例如：

   ![提供環境詳細資料](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **訂閱** | 是 | <*Azure 訂閱名稱*> | 要用於環境的 Azure 訂用帳戶 |
   | **資源組** | 是 | <*Azure 資源組名稱*> | 要在其中創建環境的新或現有 Azure 資源組 |
   | **整合服務環境名稱** | 是 | <*環境名稱*> | ISE 名稱，只能包含字母、數位、連字號 （）、`-`底線 （`_`和句點`.`（ ）。 |
   | **位置** | 是 | <*Azure-資料中心區域*> | 要用來部署環境的 Azure 資料中心區域 |
   | **Sku** | 是 | **高級**或**開發人員（無 SLA）** | 要創建和使用 ISE SKU。 有關這些 SKU 之間的差異，請參閱[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 <p><p>**重要提示**：此選項僅在 ISE 創建時可用，以後無法更改。 |
   | **額外容量** | 進階： <br>是 <p><p>開發 人員： <br>不適用 | 進階： <br>0 到 10 <p><p>開發 人員： <br>不適用 | 用於此 ISE 資源的其他處理單元數。 要在創建後添加容量，請參閱[添加 ISE 容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 |
   | **訪問終結點** | 是 | **內部**或**外部** | 用於 ISE 的訪問終結點的類型。 這些終結點確定 ISE 中邏輯應用上的請求或 Webhook 觸發器是否可以從虛擬網路外部接收呼叫。 <p><p>您的選擇還會影響在邏輯應用中查看和訪問輸入和輸出的方式執行歷程記錄。 有關詳細資訊，請參閱[ISE 終結點訪問](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 <p><p>**重要提示**：此選項僅在 ISE 創建時可用，以後無法更改。 |
   | **虛擬網路** | 是 | <*Azure 虛擬網路名稱*> | 要插入環境的 Azure 虛擬網路，讓該環境中的邏輯應用程式可以存取虛擬網路。 如果沒有網路，[請先創建 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。 <p><p>**重要提示**：*您只能在*創建 ISE 時執行此注入。 |
   | **子網** | 是 | <*子網資源清單*> | ISE 需要四個*空*子網來創建和部署環境中的資源。 若要建立每個子網路，[請遵循此表格底下的步驟](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **建立子網路**

   要在環境中創建和部署資源，ISE 需要四個未委派給任何服務的*空*子網。 每個子網都支援 ISE 中使用的不同邏輯應用元件。 創建環境後 *，無法*更改這些子網位址。 每個子網都需要滿足這些要求：

   * 名稱以字母字元或底線開頭（無數位），`<`並且不使用這些字元： `>`、 `%`、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `&` `\\` `?` `/`

   * 使用[無類域間路由 （CIDR） 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和 B 類位址空間。

   * 在位址空間中`/27`至少使用 a，因為每個子網*至少需要*32 個位址。 例如：

     * `10.0.0.0/28`只有 16 個位址，並且太小，因為 2<sup>（32-28）</sup>是 2<sup>4</sup>或 16。

     * `10.0.0.0/27` 有 32 個位址，因為 2<sup>(32-27)</sup>是 2<sup>5</sup> 或 32。

     * `10.0.0.0/24` 有 256 個位址，因為 2<sup>(32-24)</sup>是 2<sup>8</sup> 或 256。 但是，更多的位址不提供任何其他好處。

     要瞭解有關計算位址的更多詳細資訊，請參閱[IPv4 CIDR 塊](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果使用[ExpressRoute](../expressroute/expressroute-introduction.md)，則必須[創建具有以下路由的路由表](../virtual-network/manage-route-table.md)，並將該錶鏈接到 ISE 使用的每個子網：

     **名稱**： <*路由名稱*><br>
     **位址首碼**： 0.0.0.0/0<br>
     **下一跳**： 互聯網

   1. 在 **"子網**"清單中，選擇 **"管理子網配置**"。

      ![管理子網路設定](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. 在 **"子網"** 窗格中，選擇**子網**。

      ![添加四個空子網](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. 在 [新增子網路]**** 窗格中，提供這項資訊。

      * **名稱**： 子網的名稱
      * **位址範圍 （CIDR 塊）：** 您的子網範圍在虛擬網路和 CIDR 格式

      ![新增子網路詳細資料](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 完成後，選取 [確定]****。

   1. 針對其他三個子網路重複這些步驟。

      > [!NOTE]
      > 如果嘗試創建的子網無效，Azure 門戶將顯示一條消息，但不會阻止進度。

   有關創建子網的詳細資訊，請參閱[添加虛擬網路子網](../virtual-network/virtual-network-manage-subnet.md)。

1. Azure 成功驗證 ISE 資訊後，選擇 **"創建**"，例如：

   ![成功驗證後，選擇"創建"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 開始部署環境，通常需要兩個小時才能完成。 有時，部署可能需要長達四個小時。 要檢查部署狀態，請在 Azure 工具列上選擇通知圖示，該圖示將打開通知窗格。

   ![檢查部署狀態](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   成功完成部署時，Azure 就會顯示此通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   否則，請按照 Azure 門戶說明進行故障排除部署。

   > [!NOTE]
   > 如果部署失敗或您刪除了 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 此延遲意味著您可能需要等待，然後再在另一個 ISE 中重用這些子網。
   >
   > 如果刪除虛擬網路，Azure 通常會在釋放子網之前最多需要兩個小時，但此操作可能需要更長時間。 
   > 刪除虛擬網路時，請確保沒有資源仍然連接。 
   > 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 要查看環境，如果 Azure 在部署完成後未自動轉到環境，請選擇 **"轉到資源**"。

1. 要檢查 ISE 的網路運行狀況，請參閱[管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)。

1. 要開始在 ISE 中創建邏輯應用和其他專案，請參閱[向整合服務環境添加資源](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

   > [!IMPORTANT]
   > 創建 ISE 後可用的託管 ISE 連接器不會自動顯示在邏輯應用設計器上的連接器選取器中。 在使用這些 ISE 連接器之前，必須手動將這些[連接器添加到 ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)中，以便它們顯示在邏輯應用設計器中。

## <a name="next-steps"></a>後續步驟

* [向整合服務環境添加資源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
