---
title: 使用私人端點進行 Azure 應用程式組態
description: 使用私人端點保護您的應用程式設定存放區
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: a29c8c02093f47807ec71ffcc01e26514976ce79
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071705"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>使用私人端點進行 Azure 應用程式組態

您可以使用 [私人端點](../private-link/private-endpoint-overview.md) 進行 Azure 應用程式組態，讓虛擬網路上的用戶端 (VNet) 安全地透過 [私人連結](../private-link/private-link-overview.md)存取資料。 私人端點會針對您的應用程式設定存放區使用 VNet 位址空間中的 IP 位址。 VNet 上的用戶端與應用程式設定存放區之間的網路流量，會透過 Microsoft 骨幹網路上的私人連結在 VNet 上進行交易，以消除公開網際網路的風險。

針對您的應用程式設定存放區使用私人端點，可讓您：
- 藉由設定防火牆來封鎖公用端點上的所有應用程式設定連線，以保護您的應用程式設定詳細資料。
- 提高虛擬網路 (VNet) 的安全性，以確保資料不會從 VNet 中退出。
- 使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [expressroute](../expressroute/expressroute-locations.md) 搭配私人對等互連，安全地從連線到 VNet 的內部部署網路連線到應用程式設定存放區。

## <a name="conceptual-overview"></a>概念概觀

私人端點是您 [虛擬網路](../virtual-network/virtual-networks-overview.md) 中的 Azure 服務 (VNet) 的特殊網路介面。 當您為應用程式設定存放區建立私人端點時，它會在 VNet 和您的設定存放區的用戶端之間提供安全的連線能力。 私人端點會從 VNet 的 IP 位址範圍中指派 IP 位址。 私人端點與設定存放區之間的連接會使用安全的私用連結。

VNet 中的應用程式可以 **使用相同的連接字串和授權機制，** 透過私人端點連線到設定存放區，否則會使用它們。 私人端點可用於應用程式設定存放區所支援的所有通訊協定。

當應用程式設定不支援服務端點時，可以在使用 [服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)的子網中建立私人端點。 子網中的用戶端可以使用私人端點安全地連接至應用程式設定存放區，並使用服務端點來存取其他人。  

當您在 VNet 中建立服務的私人端點時，會傳送同意要求以供核准給服務帳戶擁有者。 如果要求建立私人端點的使用者也是此帳戶的擁有者，則會自動核准此同意要求。

服務帳戶擁有者可以透過 `Private Endpoints` [Azure 入口網站](https://portal.azure.com)中設定存放區的索引標籤來管理同意要求和私人端點。

### <a name="private-endpoints-for-app-configuration"></a>應用程式設定的私人端點 

建立私人端點時，您必須指定它所連接的應用程式設定存放區。 如果您的帳戶中有多個應用程式設定實例，每個存放區都需要個別的私人端點。

### <a name="connecting-to-private-endpoints"></a>連接至私人端點

Azure 依賴 DNS 解析，透過私人連結將 VNet 的連線路由至設定存放區。 您可以藉由選取應用程式設定存放區，然後選取 [**設定**  >  **存取金鑰**]，在 Azure 入口網站中快速尋找連接字串。  

> [!IMPORTANT]
> 使用相同的連接字串，以使用私人端點連線到您的應用程式設定存放區，就像使用公用端點一樣。 請勿使用其子域 URL 連接到存放區 `privatelink` 。

## <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更

當您建立私人端點時，設定存放區的 DNS CNAME 資源記錄會更新為具有前置詞之子域中的別名 `privatelink` 。 Azure 也會建立對應至子域的 [私人 dns 區域](../dns/private-dns-overview.md) `privatelink` ，dns 是私人端點的資源記錄。

當您從裝載私人端點的 VNet 內解析端點 URL 時，它會解析為存放區的私人端點。 從 VNet 外部解析時，端點 URL 會解析為公用端點。 當您建立私人端點時，公用端點會停用。

如果您在網路上使用自訂 DNS 伺服器，用戶端必須能夠將服務端點的完整功能變數名稱 (FQDN) 解析為私人端點 IP 位址。 設定您的 DNS 伺服器，以將私人連結子域委派給 VNet 的私人 DNS 區域，或設定 `AppConfigInstanceA.privatelink.azconfig.io` 具有私人端點 IP 位址的 A 記錄。

> [!TIP]
> 使用自訂或內部部署 DNS 伺服器時，您應該將 DNS 伺服器設定為將子域中的存放區名稱解析 `privatelink` 為私人端點 IP 位址。 若要這麼做，您可以將 `privatelink` 子域委派給 VNet 的私人 DNS 區域，或在 dns 伺服器上設定 dns 區域，然後新增 Dns A 記錄。

## <a name="pricing"></a>定價

啟用私人端點需要 [標準層](https://azure.microsoft.com/pricing/details/app-configuration/) 應用程式設定存放區。  若要深入瞭解 private link 定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>後續步驟

深入瞭解如何為您的應用程式設定存放區建立私人端點，請參閱下列文章：

- [使用 Azure 入口網站中的 Private Link 中心建立私人端點](../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 建立私人端點](../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 建立私人端點](../private-link/create-private-endpoint-powershell.md)

瞭解如何使用私人端點設定您的 DNS 伺服器：

- [Azure 虛擬網路中的資源名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [私人端點的 DNS 設定](../private-link/private-endpoint-overview.md#dns-configuration)