---
title: 針對虛擬網路對等互連問題進行疑難排解
description: 説明解決大多數虛擬網路對等問題的步驟。
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796244"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>針對虛擬網路對等互連問題進行疑難排解

此故障排除指南提供了説明您解決大多數[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)問題的步驟。

![虛擬網路對等圖](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>配置兩個虛擬網路之間的虛擬網路對等互連

虛擬網路是在同一訂閱中還是處於不同的訂閱中？

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>虛擬網路位於同一訂閱中

要為同一訂閱中的虛擬網路配置虛擬網路對等互連，請使用以下文章中的方法：

* 如果虛擬網路位於*同一區域*中，請參閱[創建對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)。
* 如果虛擬網路位於*不同的區域*，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 

> [!Note]
> 通過全域虛擬網路對等互連，連接無法用於以下資源： 
>
> * 基本內部負載等化器 （ILB） SKU 後面的虛擬機器 （VM）
> * 雷迪斯緩存（使用基本 ILB SKU）
> * 應用程式閘道（使用基本 ILB SKU）
> * 虛擬機器規模集（使用基本 ILB SKU）
> * Azure 服務結構群集（使用基本 ILB SKU）
> * SQL 伺服器始終打開（使用基本 ILB SKU）
> * 適用于電源應用的 Azure 應用服務環境（使用基本 ILB SKU）
> * Azure API 管理（使用基本 ILB SKU）
> * Azure 活動目錄域服務（Azure AD DS）（使用基本 ILB SKU）

有關詳細資訊，請參閱全域對等互連[的要求和約束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>虛擬網路位於不同的訂閱或活動目錄租戶中

要為不同訂閱或活動目錄租戶中的虛擬網路配置虛擬網路對等互連，請參閱[為 Azure CLI 在不同的訂閱中創建對等互連](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

> [!Note]
> 要配置網路對等互連，必須在兩個訂閱中具有**網路參與者**許可權。 有關詳細資訊，請參閱[對等許可權](virtual-network-manage-peering.md#permissions)。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>使用使用本地資源的中心輻條拓撲配置虛擬網路對等互連

![虛擬網路與本地分支對等圖](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>用於網站到網站的連接或快速路由連接

按照以下步驟操作：[為虛擬網路對等互連配置 VPN 閘道傳輸](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。

### <a name="for-point-to-site-connections"></a>用於點到網站連接

1. 按照以下步驟操作：[為虛擬網路對等互連配置 VPN 閘道傳輸](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。
2. 建立或更改虛擬網路對等互連後，下載並重新安裝點對點包，以便點對點用戶端獲取到分支虛擬網路的更新路由。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>使用中心輻條拓撲虛擬網路配置虛擬網路對等互連

![虛擬網路與虛擬網路分支對等圖](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>虛擬網路位於同一區域


1. 在中心虛擬網路中，配置網路虛擬裝置 （NVA）。
1. 在分支虛擬網路中，應用應用了應用下一個躍點類型的使用者定義的路由。

有關詳細資訊，請參閱[服務連結](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)。

> [!Note]
> 如果您需要幫助來設置 NVA，[請與 NVA 供應商聯繫](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

有關對 NVA 設備設置和路由進行故障排除的説明，請參閱[Azure 中的網路虛擬裝置問題](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

### <a name="the-virtual-networks-are-in-different-regions"></a>虛擬網路位於不同的區域

現在支援通過全域虛擬網路對等互連進行傳輸。 通過全域虛擬網路對等互連，連接不適用於以下資源：

* 基本 ILB SKU 背後的 VM
* 雷迪斯緩存（使用基本 ILB SKU）
* 應用程式閘道（使用基本 ILB SKU）
* 縮放集（使用基本 ILB SKU）
* 服務交換矩陣群集（使用基本 ILB SKU）
* SQL 伺服器始終打開（使用基本 ILB SKU）
* 應用服務環境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS（使用基本 ILB SKU）

要瞭解有關全域對等要求和約束的更多資訊，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>解決兩個對等虛擬網路之間的連接問題

使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登錄到[Azure 門戶](https://portal.azure.com/)。 選擇虛擬網路，選擇**對等，** 然後檢查**狀態**欄位。 狀態如何？

### <a name="the-peering-status-is-connected"></a>對等互連狀態為"已連接"

若要疑難排解此問題：

1. 檢查網路流量流量：

   使用[連接疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)和[IP 流驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)從源 VM 到目標 VM 以確定是否存在導致流量流量干擾的 NSG 或 UDR。

   如果您使用的是防火牆或 NVA： 
   1. 記錄 UDR 參數，以便在此步驟完成後還原它們。
   2. 從指向 NVA 的源 VM 子網或 NIC 中刪除 UDR 作為下一個躍點。 直接從源 VM 驗證與繞過 NVA 的目標的連接。 如果此步驟不起作用，請參閱[NVA 疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

2. 獲取網路跟蹤： 
   1. 在目標 VM 上啟動網路跟蹤。 對於 Windows，可以使用**Netsh**。 對於 Linux，請使用**TCPDump**。
   2. 從源運行**TcpPing**或**PsPing**到目標 IP。

      這是**TcpPing**命令的示例：`tcping64.exe -t <destination VM address> 3389`

   3. **TcpPing**完成後，停止目標上的網路跟蹤。
   4. 如果資料包從源到達，則不存在網路問題。 檢查 VM 防火牆和偵聽該埠上的應用程式以查找配置問題。

   > [!Note]
   > 無法通過全域虛擬網路對等互連（不同區域中的虛擬網路）連接到以下資源類型：
   >
   > * 基本 ILB SKU 背後的 VM
   > * 雷迪斯緩存（使用基本 ILB SKU）
   > * 應用程式閘道（使用基本 ILB SKU）
   > * 縮放集（使用基本 ILB SKU）
   > * 服務交換矩陣群集（使用基本 ILB SKU）
   > * SQL 伺服器始終打開（使用基本 ILB SKU）
   > * 應用服務環境（使用基本 ILB SKU）
   > * API 管理（使用基本 ILB SKU）
   > * Azure AD DS（使用基本 ILB SKU）

有關詳細資訊，請參閱全域對等互連[的要求和約束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-peering-status-is-disconnected"></a>對等互連狀態為"已中斷連線"

要解決此問題，請從兩個虛擬網路中刪除對等互連，然後重新創建它們。

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>解決中心輻條虛擬網路和本地資源之間的連接問題

您的網路是否使用協力廠商 NVA 或 VPN 閘道？

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>我的網路使用協力廠商 NVA 或 VPN 閘道

要解決影響協力廠商 NVA 或 VPN 閘道的連接問題，請參閱以下文章：

* [NVA 疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [服務鏈結](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>我的網路不使用協力廠商 NVA 或 VPN 閘道

中心虛擬網路和分支虛擬網路有 VPN 閘道嗎？

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>中心虛擬網路和分支虛擬網路都有 VPN 閘道

不支援使用遠端閘道。

如果分支虛擬網路已具有 VPN 閘道，則分支虛擬網路不支援**使用遠端閘道**選項。 這是因為虛擬網路對等互連限制。

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>中心虛擬網路和分支虛擬網路都沒有 VPN 閘道

對於網站到網站或 Azure ExpressRoute 連接，請從本地檢查與遠端虛擬網路連接問題的以下主要原因：

* 在具有閘道的虛擬網路上，驗證是否選擇了 **"允許轉發流量**"核取方塊。
* 在沒有閘道的虛擬網路上，驗證是否選擇了 **"使用遠端閘道**"核取方塊。
* 讓網路系統管理員檢查本地設備，以驗證它們是否都添加了遠端虛擬網路位址空間。

對於點到網站的連接：

* 在具有閘道的虛擬網路上，驗證是否選擇了 **"允許轉發流量**"核取方塊。
* 在沒有閘道的虛擬網路上，驗證是否選擇了 **"使用遠端閘道**"核取方塊。
* 下載並重新安裝點對點用戶端包。 新對等的虛擬網路路由不會自動向點對點用戶端添加路由。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>解決同一區域中分支虛擬網路之間的中心輻網路連接問題

集線器網路必須包括 NVA。 在具有 NVA 集的分支中配置 UDR 作為下一個躍點，並在集線器虛擬網路中啟用 **"允許轉發流量**"。

有關詳細資訊，請參閱[服務連結](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)，並與您選擇的[NVA 供應商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)討論這些要求。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>解決不同區域分支虛擬網路之間的中心輻網路連接問題

現在支援通過全域虛擬網路對等互連進行傳輸。 通過全域虛擬網路對等互連，連接無法用於以下資源：

* 基本 ILB SKU 背後的 VM
* 雷迪斯緩存（使用基本 ILB SKU）
* 應用程式閘道（使用基本 ILB SKU）
* 縮放集（使用基本 ILB SKU）
* 服務交換矩陣群集（使用基本 ILB SKU）
* SQL 伺服器始終打開（使用基本 ILB SKU）
* 應用服務環境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS（使用基本 ILB SKU）

有關詳細資訊，請參閱全域對等互連和[不同 VPN 拓撲](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)[的要求和約束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>解決 Web 應用和分支虛擬網路之間的中心分支網路連接問題

若要疑難排解此問題：

1. 登入 Azure 入口網站。 
1. 在 Web 應用中，選擇**網路**，然後選擇**VNet 集成**。
1. 檢查是否可以看到遠端虛擬網路。 手動輸入遠端虛擬網路位址空間 （**同步網路和****添加路由**）。

如需詳細資訊，請參閱下列文章：

* [將應用與 Azure 虛擬網路集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [關於點對站 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>排除虛擬網路對等互連配置錯誤訊息 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>當前租戶`<TENANT ID>`無權訪問連結的訂閱

要解決此問題，請參閱[創建對等互連 - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

### <a name="not-connected"></a>Not connected

要解決此問題，請從兩個虛擬網路中刪除對等互連，然後重新創建它們。

### <a name="failed-to-peer-a-databricks-virtual-network"></a>無法對資料磚虛擬網路進行對等

要解決此問題，請在**Azure 資料塊**下配置虛擬網路對等互連，然後使用**資源識別碼**指定目標虛擬網路。 有關詳細資訊，請參閱將[Databricks 虛擬網路對遠端虛擬網路進行對等](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)。

## <a name="next-steps"></a>後續步驟

* [為 Azure VM 之間的連線問題疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
