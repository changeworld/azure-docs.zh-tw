---
title: 使用私人端點
titleSuffix: Azure SignalR Service
description: 概述私人端點，以安全地存取虛擬網路中的 Azure SignalR Service。
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 645b2c643c1c1d4fe82eb5998a35ccc48536603e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84302140"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>使用私人端點進行 Azure SignalR Service

您可以使用 [私人端點](../private-link/private-endpoint-overview.md) 進行 Azure SignalR Service，讓虛擬網路上的用戶端 (VNet) 安全地透過 [Private Link](../private-link/private-link-overview.md)存取資料。 私人端點會針對您的 Azure SignalR Service 使用 VNet 位址空間中的 IP 位址。 VNet 上的用戶端與 Azure SignalR Service 之間的網路流量會透過 Microsoft 骨幹網路上的私人連結來進行，而不會暴露在公用網際網路上。

針對您的 Azure SignalR Service 使用私用端點，可讓您：

- 使用網路存取控制來保護您的 Azure SignalR Service，以封鎖公用端點上的所有連線以進行 Azure SignalR Service。
- 藉由讓您封鎖從 VNet 遭到外泄的資料，提高虛擬網路 (VNet) 的安全性。
- 使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [expressroute](../expressroute/expressroute-locations.md) 搭配私人對等互連，安全地從連線到 VNet 的內部部署網路連線到 Azure SignalR Services。

## <a name="conceptual-overview"></a>概念概觀

![Azure SignalR Service 的私人端點總覽](media/howto-private-endpoints/private-endpoint-overview.png)

私人端點是您 [虛擬網路](../virtual-network/virtual-networks-overview.md) 中的 Azure 服務 (VNet) 的特殊網路介面。 當您為 Azure SignalR Service 建立私人端點時，它會在您的 VNet 和服務的用戶端之間提供安全的連線能力。 私人端點會從 VNet 的 IP 位址範圍中指派 IP 位址。 私人端點與 Azure SignalR Service 之間的連接會使用安全的私用連結。

VNet 中的應用程式可以 **使用相同的連接字串和其使用的授權機制**，順暢地透過私人端點連接到 Azure SignalR Service。 私人端點可用於 Azure SignalR Service 所支援的所有通訊協定，包括 REST API。

當您為 VNet 中的 Azure SignalR Service 建立私人端點時，會將同意要求傳送給 Azure SignalR Service 擁有者的核准。 如果要求建立私人端點的使用者也是 Azure SignalR Service 的擁有者，則會自動核准此同意要求。

Azure SignalR Service 擁有者可以透過[Azure 入口網站](https://portal.azure.com)中 Azure SignalR Service 的 [*私人端點*] 索引標籤，來管理同意要求和私人端點。

> [!TIP]
> 如果您想要限制只能透過私人端點存取您的 Azure SignalR Service，請 [設定網路存取控制](howto-network-access-control.md#managing-network-access-control) 以拒絕或控制透過公用端點的存取。

### <a name="connecting-to-private-endpoints"></a>連接至私人端點

使用私人端點的 VNet 上的用戶端應該針對 Azure SignalR Service 使用相同的連接字串，因為用戶端會連線至公用端點。 我們依賴 DNS 解析，以透過私人連結自動將 VNet 的連線路由至 Azure SignalR Service。

> [!IMPORTANT]
> 使用相同的連接字串連接到使用私人端點的 Azure SignalR Service，如您所使用的一樣。 請勿使用其子域 URL 連接到 Azure SignalR Service `privatelink` 。

根據預設，我們會建立附加至 VNet 的 [私人 DNS 區域](../dns/private-dns-overview.md) ，並具有私人端點的必要更新。 但是，如果您使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行額外的變更。 下列 [DNS 變更](#dns-changes-for-private-endpoints) 一節說明私人端點所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更

當您建立私人端點時，您 Azure SignalR Service 的 DNS CNAME 資源記錄會更新為具有前置詞之子域中的別名 `privatelink` 。 根據預設，我們也會建立對應至子域的 [私人 dns 區域](../dns/private-dns-overview.md)， `privatelink` 並以 DNS 為私人端點的資源記錄。

當您透過私人端點從 VNet 外部解析 Azure SignalR Service 功能變數名稱時，它會解析為 Azure SignalR Service 的公用端點。 從裝載私人端點的 VNet 解析時，功能變數名稱會解析為私人端點的 IP 位址。

在上述說明的範例中，從裝載私人端點的 VNet 外部解析時，Azure SignalR Service ' foobar ' 的 DNS 資源記錄會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

如先前所述，您可以透過使用網路存取控制的公用端點，拒絕或控制 VNet 外部用戶端的存取。

當裝載私人端點之 VNet 中的用戶端解析時，' foobar ' 的 DNS 資源記錄將會是：

| 名稱                                                  | 類型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

這種方法可讓您存取在裝載私人端點的 VNet 上，以及 VNet 外部用戶端的用戶端 **使用相同的連接字串** 來存取 Azure SignalR Service。

如果您在網路上使用自訂 DNS 伺服器，用戶端必須能夠將 Azure SignalR Service 端點的 FQDN 解析為私人端點 IP 位址。 您應該將您的 DNS 伺服器設定為將私人連結子域委派給 VNet 的私人 DNS 區域，或設定 `foobar.privatelink.service.signalr.net` 具有私人端點 IP 位址的 A 記錄。

> [!TIP]
> 使用自訂或內部部署 DNS 伺服器時，您應該將 DNS 伺服器設定為將子域中的 Azure SignalR Service 名稱解析 `privatelink` 為私人端點 IP 位址。 若要這麼做，您可以將 `privatelink` 子域委派給 VNet 的私人 DNS 區域，或在 dns 伺服器上設定 dns 區域，然後新增 Dns A 記錄。

Azure SignalR Service 私人端點的建議 DNS 區功能變數名稱稱為： `privatelink.service.signalr.net` 。

如需有關設定您自己的 DNS 伺服器以支援私人端點的詳細資訊，請參閱下列文章：

- [Azure 虛擬網路中的資源名稱解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [私人端點的 DNS 設定](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="create-a-private-endpoint"></a>建立私人端點

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>在 Azure 入口網站中建立私人端點以及新的 Azure SignalR Service

1. 建立新的 Azure SignalR Service 時，請選取 [ **網路** 功能] 索引標籤。選擇 [ **私人端點** ] 作為連接方法。

    ![建立 Azure SignalR Service-[網路] 索引標籤](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. 按一下 [新增] 。 填入新私人端點的訂用帳戶、資源群組、位置和名稱。 選擇虛擬網路和子網。

    ![建立 Azure SignalR Service-新增私人端點](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. 按一下 [檢閱 + 建立]。

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>在 Azure 入口網站中建立現有 Azure SignalR Service 的私人端點

1. 移至 Azure SignalR Service。

1. 按一下 [設定] 功能表，稱為 [ **私人端點**連線]。

1. 按一下頂端的按鈕 **+ 私人端點** 。

    ![私人端點連接分頁](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. 填入新私人端點的訂用帳戶、資源群組、資源名稱和區域。
    
    ![建立私人端點-基本](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. 選擇 [目標 Azure SignalR Service 資源]。

    ![建立私人端點-資源](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

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

請注意下列有關私人端點的已知問題 Azure SignalR Service

### <a name="free-tier"></a>免費層

您無法建立免費層 Azure SignalR Service 的任何私人端點。

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有私人端點的 Vnet 中用戶端的存取限制

在 Vnet 中，如果用戶端具有具有私人端點的其他 Azure SignalR Service 實例，則具有現有私用端點的用戶端就會面臨 例如，假設 VNet N1 有 Azure SignalR Service 實例 S1 的私用端點。 如果 Azure SignalR Service S2 在 VNet N2 中有私人端點，則 VNet N1 中的用戶端也必須使用私人端點來存取 Azure SignalR Service S2。 如果 Azure SignalR Service S2 沒有任何私人端點，則 VNet N1 中的用戶端可以存取該帳戶中的 Azure SignalR Service，而不需要私人端點。

此條件約束是 Azure SignalR Service S2 建立私人端點時，所做的 DNS 變更結果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>具有私人端點的子網路所適用的網路安全性群組規則

您目前無法設定 [網路安全性群組](../virtual-network/security-overview.md) (NSG) 規則和私人端點的使用者定義路由。 套用至裝載私人端點之子網的 NSG 規則會套用至私人端點。 此問題的有限因應措施是針對來源子網上的私人端點實行存取規則，但是這種方法可能需要較高的管理負荷。

## <a name="next-steps"></a>接下來的步驟

- [設定網路存取控制](howto-network-access-control.md)
