---
title: 使用私人端點
titleSuffix: Azure SignalR Service
description: 針對從虛擬網路 Azure SignalR Service 的安全存取，提供私人端點的總覽。
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 645b2c643c1c1d4fe82eb5998a35ccc48536603e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302140"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Azure SignalR Service 使用私人端點

您可以為您的 Azure SignalR Service 使用[私人端點](../private-link/private-endpoint-overview.md)，讓虛擬網路（VNet）上的用戶端可以透過[私人連結](../private-link/private-link-overview.md)安全地存取資料。 私人端點會針對您的 Azure SignalR Service 使用 VNet 位址空間中的 IP 位址。 VNet 上的用戶端與 Azure SignalR Service 間的網路流量，會透過 Microsoft 骨幹網路上的私人連結進行，以消除公開網際網路的暴露。

您的 Azure SignalR Service 使用私人端點可讓您：

- 使用網路存取控制來保護您的 Azure SignalR Service，以封鎖 Azure SignalR Service 公用端點上的所有連線。
- 藉由讓您封鎖從 VNet 外泄的資料，提高虛擬網路（VNet）的安全性。
- 使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoutes](../expressroute/expressroute-locations.md)搭配私用對等互連，從連線到 VNet 的內部部署網路安全地連線到 Azure SignalR Services。

## <a name="conceptual-overview"></a>概念概觀

![Azure SignalR Service 的私用端點總覽](media/howto-private-endpoints/private-endpoint-overview.png)

私人端點是[虛擬網路](../virtual-network/virtual-networks-overview.md)（VNet）中 Azure 服務的特殊網路介面。 當您建立 Azure SignalR Service 的私人端點時，它會在您的 VNet 和您的服務上的用戶端之間提供安全的連線。 私人端點會從 VNet 的 IP 位址範圍指派 IP 位址。 私人端點和 Azure SignalR Service 之間的連接會使用安全的私用連結。

VNet 中的應用程式可以**使用相同的連接字串和授權機制**，順暢地透過私人端點連接到 Azure SignalR Service，否則會使用它們。 私人端點可以與 Azure SignalR Service 支援的所有通訊協定搭配使用，包括 REST API。

當您在 VNet 中建立 Azure SignalR Service 的私人端點時，會將同意要求傳送給 Azure SignalR Service 擁有者的核准。 如果要求建立私人端點的使用者也是 Azure SignalR Service 的擁有者，則會自動核准此同意要求。

Azure SignalR Service 擁有者可以透過[Azure 入口網站](https://portal.azure.com)中 Azure SignalR Service 的 [*私人端點*] 索引標籤，來管理同意要求和私人端點。

> [!TIP]
> 如果您想要限制只能透過私人端點存取您的 Azure SignalR Service，請[將網路存取控制設定](howto-network-access-control.md#managing-network-access-control)為拒絕或透過公用端點控制存取。

### <a name="connecting-to-private-endpoints"></a>連接到私人端點

使用私用端點之 VNet 上的用戶端，應使用與連線到公用端點的用戶端相同的 Azure SignalR Service 連接字串。 我們依賴 DNS 解析，透過私人連結自動將 VNet 的連線路由至 Azure SignalR Service。

> [!IMPORTANT]
> 使用相同的連接字串連接到使用私人端點的 Azure SignalR Service，如同您使用的其他方式。 請不要使用其子域 URL 連接到 Azure SignalR Service `privatelink` 。

根據預設，我們會建立連結至 VNet 的[私人 DNS 區域](../dns/private-dns-overview.md)，並具有私人端點的必要更新。 不過，如果您是使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行其他變更。 下列[DNS 變更](#dns-changes-for-private-endpoints)章節說明私人端點所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更

當您建立私人端點時，會將您 Azure SignalR Service 的 DNS CNAME 資源記錄更新為具有前置詞之子域中的別名 `privatelink` 。 根據預設，我們也會建立與子域對應的[私人 DNS 區域](../dns/private-dns-overview.md)， `privatelink` 並以 DNS 做為私人端點的資源記錄。

當您從具有私用端點的 VNet 外部解析 Azure SignalR Service 功能變數名稱時，它會解析為 Azure SignalR Service 的公用端點。 從裝載私用端點的 VNet 解析時，功能變數名稱會解析為私人端點的 IP 位址。

針對上述圖例，從裝載私用端點的 VNet 外部解析時，Azure SignalR Service ' foobar ' 的 DNS 資源記錄將會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

如先前所述，您可以透過使用網路存取控制的公用端點，拒絕或控制 VNet 外部用戶端的存取。

' Foobar ' 的 DNS 資源記錄（由裝載私用端點的 VNet 中的用戶端解析），將會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

這種方法可讓您存取 Azure SignalR Service 在裝載私人端點的 VNet 上，以及在 VNet 外部的用戶端上**使用相同的連接字串**。

如果您在網路上使用自訂 DNS 伺服器，用戶端必須能夠將 Azure SignalR Service 端點的 FQDN 解析為私人端點 IP 位址。 您應該將 DNS 伺服器設定為將私人連結子域委派給 VNet 的私人 DNS 區域，或設定 `foobar.privatelink.service.signalr.net` 具有私人端點 IP 位址的 A 記錄。

> [!TIP]
> 使用自訂或內部部署 DNS 伺服器時，您應該將 DNS 伺服器設定為將子域中的 Azure SignalR Service 名稱解析 `privatelink` 為私人端點 IP 位址。 若要這麼做，您可以將 `privatelink` 子域委派給 VNet 的私人 DNS 區域，或在 dns 伺服器上設定 dns 區域，並新增 Dns A 記錄。

Azure SignalR Service 之私人端點的建議 DNS 區功能變數名稱稱為： `privatelink.service.signalr.net` 。

如需有關設定您自己的 DNS 伺服器以支援私人端點的詳細資訊，請參閱下列文章：

- [Azure 虛擬網路中的資源名稱解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [私人端點的 DNS 設定](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="create-a-private-endpoint"></a>建立私人端點

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>建立私用端點，以及 Azure 入口網站中的新 Azure SignalR Service

1. 建立新 Azure SignalR Service 時，請選取 [**網路**功能] 索引標籤。選擇 [**私人端點**] 作為 [連線方法]。

    ![建立 Azure SignalR Service-網路功能索引標籤](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. 按一下 **[新增]** 。 填入新私人端點的 [訂用帳戶]、[資源群組]、[位置] 和 [名稱]。 選擇 [虛擬網路] 和 [子網]。

    ![建立 Azure SignalR Service-新增私人端點](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. 按一下 [檢閱 + 建立]。

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>在 Azure 入口網站中建立現有 Azure SignalR Service 的私用端點

1. 移至 [Azure SignalR Service]。

1. 按一下名為 [**私人端點**連線] 的 [設定] 功能表。

1. 按一下頂端的 [ **+ 私用端點**] 按鈕。

    ![私人端點連接分頁](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. 填入新私人端點的 [訂用帳戶]、[資源群組]、[資源名稱] 和 [區域]。
    
    ![建立私用端點-基本概念](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. 選擇 [目標 Azure SignalR Service 資源]。

    ![建立私用端點-資源](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. 選擇目標虛擬網路

    ![建立私人端點-設定](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. 按一下 [檢閱 + 建立]。

### <a name="create-a-private-endpoint-using-azure-cli"></a>使用 Azure CLI 建立私人端點

1. 登入 Azure CLI
    ```console
    az login
    ```
1. 選取您的 Azure 訂用帳戶
    ```console
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. 建立新的資源群組
    ```console
    az group create -n {RG} -l {AZURE REGION}
    ```
1. 將 Microsoft.signalrservice 註冊為提供者
    ```console
    az provider register -n Microsoft.SignalRService
    ```
1. 建立新的 Azure SignalR Service
    ```console
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. 建立虛擬網路
    ```console
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. 新增子網路
    ```console
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. 停用虛擬網路原則
    ```console
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. 建立私人 DNS 區域
    ```console
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. 將私人 DNS 區域連結至虛擬網路
    ```console
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. 建立私人端點 (自動核准)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. 建立私人端點 (手動要求核准)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. 顯示連線狀態
    ```console
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知問題

請記住下列關於 Azure SignalR Service 私人端點的已知問題

### <a name="free-tier"></a>免費層

您無法為免費層 Azure SignalR Service 建立任何私人端點。

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有私人端點的 Vnet 中用戶端的存取條件約束

Vnet 中具有現有私用端點的用戶端，在存取其他具有私用端點的 Azure SignalR Service 實例時，會面臨條件約束。 例如，假設 VNet N1 具有 Azure SignalR Service 實例 S1 的私用端點。 如果 Azure SignalR Service S2 在 VNet 中有私人端點 N2，則 VNet N1 中的用戶端也必須使用私用端點來存取 Azure SignalR Service S2。 如果 Azure SignalR Service S2 沒有任何私人端點，則 VNet N1 中的用戶端可以存取該帳戶中沒有私用端點的 Azure SignalR Service。

此條件約束是當 Azure SignalR Service S2 建立私用端點時，所做的 DNS 變更結果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>具有私人端點的子網路所適用的網路安全性群組規則

目前，您無法為私人端點設定[網路安全性群組](../virtual-network/security-overview.md)（NSG）規則和使用者定義的路由。 套用至裝載私用端點之子網的 NSG 規則會套用至私用端點。 此問題的有限因應措施是在來源子網上為私人端點執行存取規則，不過這種方法可能需要較高的管理負荷。

## <a name="next-steps"></a>後續步驟

- [設定網路存取控制](howto-network-access-control.md)
