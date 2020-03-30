---
title: 使用專用終結點進行 Azure 應用配置
description: 使用專用終結點保護您的應用配置存儲
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366763"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>使用專用終結點進行 Azure 應用配置

可以使用 Azure 應用配置的[專用終結點](../private-link/private-endpoint-overview.md)允許虛擬網路 （VNet） 上的用戶端通過[專用鏈路](../private-link/private-link-overview.md)安全地訪問資料。 專用終結點使用應用配置存儲的 VNet 位址空間中的 IP 位址。 VNet 上的用戶端和 App 配置商店之間的網路流量使用 Microsoft 骨幹網路上的專用連結遍歷 VNet，從而消除了對公共 Internet 的暴露。

在應用配置存儲中使用專用終結點使您能夠：
- 通過配置防火牆來阻止在公共終結點上與應用配置的所有連接，保護您的應用程式佈建詳細資訊。
- 提高虛擬網路 （VNet） 的安全性，確保資料不會從 VNet 中逸出。
- 使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[快速路由](../expressroute/expressroute-locations.md)（使用專用對等）連接到 VNet 的本地網路安全地連接到應用配置存儲。

> [!NOTE]
> Azure 應用配置提供使用專用終結點作為公共預覽。 公開預覽版供應項目可讓客戶在其正式發行前，先試驗新功能。  公開預覽功能和服務不適用於生產環境。

## <a name="conceptual-overview"></a>概念概觀

專用終結點是[虛擬網路](../virtual-network/virtual-networks-overview.md)（VNet） 中 Azure 服務的特殊網路介面。 為 App Config 存儲創建專用終結點時，它將在 VNet 上的用戶端和配置存儲中的用戶端之間提供安全連線。 從 VNet 的 IP 位址範圍為專用終結點分配了 IP 位址。 專用終結點和配置存儲之間的連接使用安全的專用鏈路。

VNet 中的應用程式可以使用**它們將使用的相同連接字串和授權機制**通過專用終結點連接到配置存儲。 專用終結點可用於應用配置存儲支援的所有協定。

雖然應用配置不支援服務終結點，但可以在使用[服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)的子網中創建專用終結點。 子網中的用戶端可以使用專用終結點安全地連接到應用配置存儲，同時使用服務終結點訪問其他終結點。  

在 VNet 中為服務創建專用終結點時，將發送同意請求以向服務帳戶擁有者發送核准請求。 如果請求創建專用終結點的使用者也是帳戶的擁有者，則此同意請求將自動獲得批准。

服務帳戶擁有者可以通過`Private Endpoints`Azure[門戶](https://portal.azure.com)中的配置存儲選項卡管理同意請求和專用終結點。

### <a name="private-endpoints-for-app-configuration"></a>應用配置的專用終結點 

創建專用終結點時，必須指定它連接到的應用配置存儲。 如果帳戶中有多個應用配置實例，則需要為每個存儲單獨使用專用終結點。

### <a name="connecting-to-private-endpoints"></a>連接到專用終結點

Azure 依靠 DNS 解析通過專用鏈路將連接從 VNet 路由到配置存儲。 您可以通過選擇應用配置存儲，然後選擇 **"設置** > **訪問金鑰**"，在 Azure 門戶中快速查找連接字串。  

> [!IMPORTANT]
> 使用相同的連接字串使用專用終結點連接到應用配置存儲，就像用於公共終結點一樣。 不要使用其`privatelink`子域 URL 連接到存儲帳戶。

## <a name="dns-changes-for-private-endpoints"></a>專用終結點的 DNS 更改

創建專用終結點時，配置存儲的 DNS CNAME 資源記錄將更新為子域中的別名，該別名的首碼`privatelink`為 。 Azure 還會創建[private DNS zone](../dns/private-dns-overview.md)與子域對應的`privatelink`專用 DNS 區域，其中具有專用終結點的 DNS A 資源記錄。

從 VNet 外部解析終結點 URL 時，它將解析為存儲的公共終結點。 從承載專用終結點的 VNet 內部解析時，終結點 URL 解析為專用終結點。

您可以使用 Azure 防火牆服務通過公共終結點控制 VNet 外部用戶端的訪問。

此方法允許對承載專用終結點的 VNet 上的用戶端以及 VNet 外部的用戶端**使用相同的連接字串**訪問存儲。

如果您在網路上使用自訂 DNS 伺服器，則用戶端必須能夠將服務終結點的完全限定功能變數名稱 （FQDN） 解析為專用終結點 IP 位址。 配置 DNS 伺服器以將專用連結子域委派為 VNet 的專用 DNS 區域，或使用專用終結點`AppConfigInstanceA.privatelink.azconfig.io`IP 位址配置 A 記錄。

> [!TIP]
> 使用自訂或本地 DNS 伺服器時，應配置 DNS 伺服器以將子域中的`privatelink`存儲名稱解析為專用終結點 IP 位址。 為此，`privatelink`可以將子域委派到 VNet 的專用 DNS 區域，或者配置 DNS 伺服器上的 DNS 區域並添加 DNS A 記錄。

## <a name="pricing"></a>定價

啟用專用終結點需要[標準層](https://azure.microsoft.com/pricing/details/app-configuration/)應用配置存儲。  要瞭解專用連結定價詳細資訊，請參閱[Azure 專用連結定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>後續步驟

詳細瞭解如何為應用配置應用商店創建專用終結點，請參閱以下文章：

- [使用 Azure 門戶中的專用連結中心創建專用終結點](../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 建立私人端點](../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 創建專用終結點](../private-link/create-private-endpoint-powershell.md)

瞭解如何使用專用終結點配置 DNS 伺服器：

- [Azure 虛擬網路中的資源名稱解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [專用終結點的 DNS 配置](/azure/private-link/private-endpoint-overview#dns-configuration)
