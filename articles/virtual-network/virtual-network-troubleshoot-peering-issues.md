---
title: 針對虛擬網路對等互連問題進行疑難排解
description: 可協助解決大部分虛擬網路對等互連問題的步驟。
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
ms.openlocfilehash: 9685c1739a00788a974c200ddabb8cc975696b62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83587726"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>針對虛擬網路對等互連問題進行疑難排解

本疑難排解指南會提供可協助您解決大部分[虛擬網路對等互連](virtual-network-peering-overview.md)問題的步驟。

![虛擬網路對等互連示意圖](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>設定兩個虛擬網路之間的虛擬網路對等互連

虛擬網路位於相同的訂用帳戶還是不同的訂用帳戶？

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>虛擬網路位於相同的訂用帳戶

若要針對位於相同訂用帳戶中的虛擬網路來設定虛擬網路對等互連，請使用下列文章中的方法：

* 如果虛擬網路位於*相同區域*，請參閱[建立對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)。
* 如果虛擬網路位於*不同區域*，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 

> [!Note]
> 下列資源無法跨全球虛擬網路對等互連來連線： 
>
> * 基本內部負載平衡器 (ILB) SKU 後方的虛擬機器 (VM)
> * Redis 快取 (使用基本 ILB SKU)
> * 應用程式閘道 (使用基本 ILB SKU)
> * 虛擬機器擴展集 (使用基本 ILB SKU)
> * Azure Service Fabric 叢集 (使用基本 ILB SKU)
> * SQL Server Always On (使用基本 ILB SKU)
> * 適用於 PowerApps 的 Azure App Service 環境 (使用基本 ILB SKU)
> * Azure API 管理 (使用基本 ILB SKU)
> * Azure Active Directory Domain Services (Azure AD DS) (使用基本 ILB SKU)

如需詳細資訊，請參閱全球對等互連的[需求和限制](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>虛擬網路位於不同的訂用帳戶或 Active Directory 租用戶

若要針對不同訂用帳戶或 Active Directory 租用戶中的虛擬網路來設定虛擬網路對等互連，請參閱[針對 Azure CLI 在不同訂用帳戶中建立對等互連](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

> [!Note]
> 若要設定網路對等互連，您必須同時擁有這兩個訂用帳戶中的**網路參與者**權限。 如需詳細資訊，請參閱[對等互連權限](virtual-network-manage-peering.md#permissions)。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>以使用內部部署資源的中樞輪輻拓撲來設定虛擬網路對等互連

![使用內部部署輪輻的虛擬網路對等互連圖表](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>針對站對站連線或 ExpressRoute 連線

遵循下列文章中的步驟：[為虛擬網路對等互連設定 VPN 閘道傳輸](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。

### <a name="for-point-to-site-connections"></a>針對點對站連線

1. 遵循下列文章中的步驟：[為虛擬網路對等互連設定 VPN 閘道傳輸](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。
2. 在建立或變更虛擬網路對等互連之後，請下載並重新安裝點對站套件，讓點對站用戶端獲得已更新的輪輻虛擬網路路由。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>以中樞輪輻拓撲虛擬網路設定虛擬網路對等互連

![使用虛擬網路輪輻的虛擬網路對等互連圖表](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>虛擬網路位於相同區域


1. 在中樞虛擬網路中，設定網路虛擬設備 (NVA)。
1. 在輪輻虛擬網路中，套用下一個躍點類型為「網路虛擬設備」的使用者定義路由。

如需詳細資訊，請參閱[服務鏈結](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)。

> [!Note]
> 如果您需要如何設定 NVA 的協助，請[連絡 NVA 廠商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

如需如何針對 NVA 裝置設定和路由進行疑難排解的協助，請參閱 [Azure 中的網路虛擬設備問題](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

### <a name="the-virtual-networks-are-in-different-regions"></a>虛擬網路位於不同區域

現在支援跨全球虛擬網路對等互連的傳輸。 下列資源無法跨全球虛擬網路對等互連來連線：

* 位於基本 ILB SKU 後方的 VM
* Redis 快取 (使用基本 ILB SKU)
* 應用程式閘道 (使用基本 ILB SKU)
* 擴展集 (使用基本 ILB SKU)
* Service Fabric 叢集 (使用基本 ILB SKU)
* SQL Server Always On (使用基本 ILB SKU)
* App Service 環境 (使用基本 ILB SKU)
* API 管理 (使用基本 ILB SKU)
* Azure AD DS (使用基本 ILB SKU)

若要深入了解全球對等互連的需求和限制，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>針對兩個對等互連虛擬網路之間的連線問題進行疑難排解

使用具有必要[角色和權限](virtual-network-manage-peering.md#permissions)的帳戶登入 [Azure 入口網站](https://portal.azure.com/)。 選取該虛擬網路、選取 [對等互連]，然後核取 [狀態] 欄位。 狀態為何？

### <a name="the-peering-status-is-connected"></a>對等互連狀態為「已連線」

若要對此問題進行疑難排解：

1. 檢查網路流量的流動情形：

   從來源 VM 到目的地 VM 使用[連線疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)和 [IP 流量驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)，以判斷是否有 NSG 或 UDR 造成流量的流動受到干擾。

   如果您使用防火牆或 NVA： 
   1. 記錄 UDR 參數，以便能夠在完成此步驟之後將這些參數還原。
   2. 從指向 NVA 以作為下一個躍點的來源 VM 子網路或 NIC 中移除 UDR。 確認能否從來源 VM 直接連線到略過 NVA 的目的地。 如果此步驟無效，請參閱 [NVA 疑難排解員](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

2. 進行網路追蹤： 
   1. 在目的地 VM 上啟動網路追蹤。 針對 Windows，您可以使用 **Netsh**。 針對 Linux，請使用 **TCPDump**。
   2. 從來源到目的地 IP 執行 **TcpPing** 或 **PsPing**。

      這是 **TcpPing** 命令的範例：`tcping64.exe -t <destination VM address> 3389`

   3. **TcpPing** 完成後，請停止目的地上的網路追蹤。
   4. 如果封包從來源抵達，就表示沒有網路問題。 檢查 VM 防火牆和在該連接埠上接聽的應用程式，以找出設定問題。

   > [!Note]
   > 您無法透過全球虛擬網路對等互連 (不同區域中的虛擬網路) 來連線到下列資源類型：
   >
   > * 位於基本 ILB SKU 後方的 VM
   > * Redis 快取 (使用基本 ILB SKU)
   > * 應用程式閘道 (使用基本 ILB SKU)
   > * 擴展集 (使用基本 ILB SKU)
   > * Service Fabric 叢集 (使用基本 ILB SKU)
   > * SQL Server Always On (使用基本 ILB SKU)
   > * App Service 環境 (使用基本 ILB SKU)
   > * API 管理 (使用基本 ILB SKU)
   > * Azure AD DS (使用基本 ILB SKU)

如需詳細資訊，請參閱全球對等互連的[需求和限制](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-peering-status-is-disconnected"></a>對等互連狀態為「已中斷連線」

若要解決此問題，請刪除這兩個虛擬網路中的對等互連，再加以重新建立。

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>針對中樞輪輻虛擬網路與內部部署資源之間的連線問題進行疑難排解

您的網路是否使用第三方 NVA 或 VPN 閘道？

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>我的網路使用第三方 NVA 或 VPN 閘道

若要針對會影響第三方 NVA 或 VPN 閘道的連線問題進行疑難排解，請參閱下列文章：

* [VPN 疑難排解員](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [服務鏈結](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>我的網路未使用第三方 NVA 或 VPN 閘道

中樞虛擬網路和輪輻虛擬網路是否有 VPN 閘道？

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>中樞虛擬網路和輪輻虛擬網路都有 VPN 閘道

不支援使用遠端閘道。

如果輪輻虛擬網路已有 VPN 閘道，則輪輻虛擬網路不支援 [使用遠端閘道] 選項。 這是因為有虛擬網路對等互連限制。

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>中樞虛擬網路和輪輻虛擬網路都沒有 VPN 閘道

針對站對站或 Azure ExpressRoute 連線，請檢查下列會導致從內部部署資源到遠端虛擬網路發生連線問題的主要原因：

* 在具有閘道的虛擬網路上，確認已選取 [允許轉送的流量] 核取方塊。
* 在沒有閘道的虛擬網路上，確認已選取 [使用遠端閘道] 核取方塊。
* 請要求您的網路系統管理員檢查內部部署裝置，以確認這些裝置都已新增遠端虛擬網路位址空間。

針對點對站連線：

* 在具有閘道的虛擬網路上，確認已選取 [允許轉送的流量] 核取方塊。
* 在沒有閘道的虛擬網路上，確認已選取 [使用遠端閘道] 核取方塊。
* 下載並重新安裝點對站用戶端套件。 新建立的對等互連虛擬網路路由並不會自動新增點對站用戶端的路由。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>針對相同區域中輪輻虛擬網路之間的中樞輪輻網路連線問題進行疑難排解

中樞網路必須包含 NVA。 在已將 NVA 設定為下一個躍點的輪輻中設定 UDR，並在中樞虛擬網路中啟用 [允許轉送的流量]。

如需詳細資訊，請參閱[服務鏈結](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)，並與您選擇的 [NVA 廠商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)討論這些需求。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>針對不同區域中輪輻虛擬網路之間的中樞輪輻網路連線問題進行疑難排解

現在支援跨全球虛擬網路對等互連的傳輸。 下列資源無法跨全球虛擬網路對等互連來連線：

* 位於基本 ILB SKU 後方的 VM
* Redis 快取 (使用基本 ILB SKU)
* 應用程式閘道 (使用基本 ILB SKU)
* 擴展集 (使用基本 ILB SKU)
* Service Fabric 叢集 (使用基本 ILB SKU)
* SQL Server Always On (使用基本 ILB SKU)
* App Service 環境 (使用基本 ILB SKU)
* API 管理 (使用基本 ILB SKU)
* Azure AD DS (使用基本 ILB SKU)

如需詳細資訊，請參閱全球對等互連的[需求和限制](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)以及[不同的 VPN 拓撲](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>針對 Web 應用程式與輪輻虛擬網路之間的中樞輪輻網路連線問題進行疑難排解

若要對此問題進行疑難排解：

1. 登入 Azure 入口網站。 
1. 在 Web 應用程式中，依序選取 [網路] 和 [VNet 整合]。
1. 檢查是否可以看到遠端虛擬網路。 手動輸入遠端虛擬網路位址空間 (**同步網路**和**新增路由**)。

如需詳細資訊，請參閱下列文章：

* [將您的應用程式與 Azure 虛擬網路整合](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [關於點對站 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>針對虛擬網路對等互連組態錯誤訊息進行疑難排解 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>目前的租用戶 `<TENANT ID>` 未獲授權存取連結的訂用帳戶

若要解決此問題，請參閱[建立對等互連 - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

### <a name="not-connected"></a>Not connected

若要解決此問題，請刪除這兩個虛擬網路中的對等互連，再加以重新建立。

### <a name="failed-to-peer-a-databricks-virtual-network"></a>無法將 Databricks 虛擬網路對等互連

若要解決此問題，請在 [Azure Databricks] 底下設定虛擬網路對等互連，然後使用 [資源識別碼] 指定目標虛擬網路。 如需詳細資訊，請參閱[將 Databricks 虛擬網路對等互連至遠端虛擬網路](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)。

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>遠端虛擬網路缺少閘道

當您將來自不同租用戶的虛擬網路對等互連，並且稍後想要設定 `Use Remote Gateways` 時，就會發生此問題。 Azure 入口網站有一項限制，那就是無法驗證虛擬網路閘道是否存在於另一個租用戶的虛擬網路中。

有兩種方法可以解決此問題：

 * 刪除對等互連，並於建立新的對等互連時啟動 `Use Remote Gateways` 選項。
 * 使用 PowerShell 或 CLI (而不是 Azure 入口網站) 來啟用 `Use Remote Gateways`。

## <a name="next-steps"></a>後續步驟

* [為 Azure VM 之間的連線問題疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
