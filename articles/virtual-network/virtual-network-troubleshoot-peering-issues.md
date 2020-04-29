---
title: 針對虛擬網路對等互連問題進行疑難排解
description: 協助解決大部分虛擬網路對等互連問題的步驟。
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
ms.openlocfilehash: 662619e101b45d1dd8b34ea97e31f214b254124a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80521876"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>針對虛擬網路對等互連問題進行疑難排解

本疑難排解指南提供的步驟可協助您解決大部分的[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)問題。

![虛擬網路對等互連的圖表](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>設定兩個虛擬網路之間的虛擬網路對等互連

虛擬網路位於相同的訂用帳戶或不同的訂閱中嗎？

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>虛擬網路位於相同的訂用帳戶中

若要為相同訂用帳戶中的虛擬網路設定虛擬網路對等互連，請使用下列文章中的方法：

* 如果虛擬網路位於*相同的區域*中，請參閱[建立對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)。
* 如果虛擬網路位於*不同的區域*，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 

> [!Note]
> 連線能力無法針對下列資源，針對全域虛擬網路對等互連進行操作： 
>
> * 基本內部負載平衡器（ILB） SKU 後方的虛擬機器（Vm）
> * Redis cache （使用基本 ILB SKU）
> * 應用程式閘道（使用基本 ILB SKU）
> * 虛擬機器擴展集（使用基本 ILB SKU）
> * Azure Service Fabric 叢集（使用基本 ILB SKU）
> * SQL Server Always On （使用基本 ILB SKU）
> * PowerApps 的 Azure App Service 環境（使用基本 ILB SKU）
> * Azure API 管理（使用基本 ILB SKU）
> * Azure Active Directory Domain Services （Azure AD DS）（使用基本 ILB SKU）

如需詳細資訊，請參閱全域對等互連的[需求和限制](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>虛擬網路位於不同的訂用帳戶或 Active Directory 租使用者中

若要為不同訂用帳戶或 Active Directory 租使用者中的虛擬網路設定虛擬網路對等互連，請參閱[Azure CLI 的不同訂用帳戶中建立對等互連](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)

> [!Note]
> 若要設定網路對等互連，您必須在這兩個訂用帳戶中具有**網路參與者**許可權。 如需詳細資訊，請參閱對[等互連許可權](virtual-network-manage-peering.md#permissions)。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>以使用內部部署資源的中樞輪輻拓撲來設定虛擬網路對等互連

![與內部部署輪輻虛擬網路對等互連的圖表](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>適用于站對站連線或 ExpressRoute 連線

遵循：[設定虛擬網路對等互連的 VPN 閘道傳輸](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)中的步驟。

### <a name="for-point-to-site-connections"></a>針對點對站連線

1. 遵循：[設定虛擬網路對等互連的 VPN 閘道傳輸](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)中的步驟。
2. 建立或變更虛擬網路對等互連之後，請下載並重新安裝點對站套件，讓點對站用戶端取得已更新的輪輻虛擬網路路由。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>設定與中樞輪輻拓朴虛擬網路的虛擬網路對等互連

![虛擬網路對等互連與虛擬網路輪輻的圖表](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>虛擬網路位於相同的區域


1. 在中樞虛擬網路中，設定網路虛擬裝置（NVA）。
1. 在輪輻虛擬網路中，已套用下一個躍點類型為「網路虛擬裝置」的使用者定義路由。

如需詳細資訊，請參閱[服務鏈](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)。

> [!Note]
> 如果您需要協助來設定 NVA，請[洽詢 NVA 廠商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

如需針對 NVA 裝置設定和路由進行疑難排解的協助，請參閱[Azure 中的網路虛擬裝置問題](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

### <a name="the-virtual-networks-are-in-different-regions"></a>虛擬網路位於不同的區域

現在支援透過全域虛擬網路對等互連的傳輸。 針對下列資源，連線能力無法透過全域虛擬網路對等互連運作：

* 基本 ILB SKU 背後的 Vm
* Redis cache （使用基本 ILB SKU）
* 應用程式閘道（使用基本 ILB SKU）
* 擴展集（使用基本 ILB SKU）
* Service Fabric 叢集（使用基本 ILB SKU）
* SQL Server Always On （使用基本 ILB SKU）
* App Service 環境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS （使用基本 ILB SKU）

若要深入瞭解全域對等互連需求和限制，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>針對兩個對等互連虛擬網路之間的連線問題進行疑難排解

使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登入[Azure 入口網站](https://portal.azure.com/)。 選取虛擬網路，選取 [對**等**]，然後檢查 [**狀態**] 欄位。 狀態為何？

### <a name="the-peering-status-is-connected"></a>對等互連狀態為「已連線」

若要疑難排解此問題：

1. 檢查網路流量流程：

   使用連線[疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)和從來源 vm 到目的地 Vm 的[IP 流量驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)，以判斷是否有 NSG 或 UDR 造成流量干擾。

   如果您使用的是防火牆或 NVA： 
   1. 記錄 UDR 參數，讓您可以在完成此步驟之後加以還原。
   2. 從來源 VM 子網或 NIC 中，移除指向 NVA 作為下一個躍點的 UDR。 確認從來源 VM 直接連線到略過 NVA 的目的地。 如果此步驟無法解決問題，請參閱[NVA 疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)員。

2. 進行網路追蹤： 
   1. 在目的地 VM 上啟動網路追蹤。 針對 Windows，您可以使用**Netsh**。 針對 Linux，請使用**TCPDump**。
   2. 從來源執行**TcpPing**或**PSPING**至目的地 IP。

      這是**TcpPing**命令的範例：`tcping64.exe -t <destination VM address> 3389`

   3. **TcpPing**完成之後，請停止目的地上的網路追蹤。
   4. 如果封包抵達來源，就不會有網路問題。 檢查 VM 防火牆和接聽該埠的應用程式，找出設定問題。

   > [!Note]
   > 您無法透過全域虛擬網路對等互連（不同區域中的虛擬網路）連接到下列資源類型：
   >
   > * 基本 ILB SKU 背後的 Vm
   > * Redis cache （使用基本 ILB SKU）
   > * 應用程式閘道（使用基本 ILB SKU）
   > * 擴展集（使用基本 ILB SKU）
   > * Service Fabric 叢集（使用基本 ILB SKU）
   > * SQL Server Always On （使用基本 ILB SKU）
   > * App Service 環境（使用基本 ILB SKU）
   > * API 管理（使用基本 ILB SKU）
   > * Azure AD DS （使用基本 ILB SKU）

如需詳細資訊，請參閱全域對等互連的[需求和限制](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-peering-status-is-disconnected"></a>對等互連狀態為「已中斷連線」

若要解決此問題，請刪除這兩個虛擬網路的對等互連，然後重新建立它們。

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>針對中樞輪輻虛擬網路與內部部署資源之間的連線問題進行疑難排解

您的網路是否使用協力廠商 NVA 或 VPN 閘道？

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>我的網路使用協力廠商 NVA 或 VPN 閘道

若要針對影響協力廠商 NVA 或 VPN 閘道的連線問題進行疑難排解，請參閱下列文章：

* [NVA 疑難排解員](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [服務鏈結](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>我的網路未使用協力廠商 NVA 或 VPN 閘道

中樞虛擬網路和輪輻虛擬網路是否有 VPN 閘道？

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>中樞虛擬網路和輪輻虛擬網路都有 VPN 閘道

不支援使用遠端閘道。

如果輪輻虛擬網路已有 VPN 閘道，則輪輻虛擬網路不支援 [**使用遠端閘道**] 選項。 這是因為虛擬網路對等互連的限制。

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>中樞虛擬網路和輪輻虛擬網路都沒有 VPN 閘道

針對站對站或 Azure ExpressRoute 連線，請檢查下列從內部部署遠端虛擬網路連線問題的主要原因：

* 在具有閘道的虛擬網路上，確認已選取 [**允許轉送的流量**] 核取方塊。
* 在沒有閘道的虛擬網路上，確認已選取 [**使用遠端閘道**] 核取方塊。
* 請讓您的網路系統管理員檢查您的內部部署裝置，確認它們都已新增遠端虛擬網路位址空間。

針對點對站連線：

* 在具有閘道的虛擬網路上，確認已選取 [**允許轉送的流量**] 核取方塊。
* 在沒有閘道的虛擬網路上，確認已選取 [**使用遠端閘道**] 核取方塊。
* 下載並重新安裝點對站用戶端套件。 新對等互連的虛擬網路路由並不會自動將路由新增到點對站用戶端。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>針對相同區域中的輪輻虛擬網路之間的中樞輪輻網路連線問題進行疑難排解

中樞網路必須包含 NVA。 在輪輻中設定 Udr，將 NVA 設為下一個躍點，並啟用中樞虛擬網路中的「**允許轉送的流量**」。

如需詳細資訊，請參閱[服務鏈](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)，並與您選擇的[NVA 廠商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)討論這些需求。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>針對不同區域中輪輻虛擬網路之間的中樞輪輻網路連線問題進行疑難排解

現在支援透過全域虛擬網路對等互連的傳輸。 連線能力無法針對下列資源，針對全域虛擬網路對等互連進行操作：

* 基本 ILB SKU 背後的 Vm
* Redis cache （使用基本 ILB SKU）
* 應用程式閘道（使用基本 ILB SKU）
* 擴展集（使用基本 ILB SKU）
* Service Fabric 叢集（使用基本 ILB SKU）
* SQL Server Always On （使用基本 ILB SKU）
* App Service 環境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS （使用基本 ILB SKU）

如需詳細資訊，請參閱全域對等互連和[不同 VPN 拓撲](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)的[需求和限制](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>針對 web 應用程式與輪輻虛擬網路之間的中樞輪輻網路連線問題進行疑難排解

若要疑難排解此問題：

1. 登入 Azure 入口網站。 
1. 在 web 應用程式中，選取 [**網路**]，然後選取 [ **VNet 整合**]。
1. 檢查是否可以看到遠端虛擬網路。 手動輸入遠端虛擬網路位址空間（[**同步網路**] 和 [**新增路由**]）。

如需詳細資訊，請參閱下列文章：

* [將您的應用程式與 Azure 虛擬網路整合](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [關於點對站 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>針對虛擬網路對等互連設定錯誤訊息進行疑難排解 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>目前的`<TENANT ID>`租使用者未獲授權，無法存取連結的訂用帳戶

若要解決此問題，請參閱[建立對等互連-Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

### <a name="not-connected"></a>Not connected

若要解決此問題，請刪除這兩個虛擬網路的對等互連，然後重新建立它們。

### <a name="failed-to-peer-a-databricks-virtual-network"></a>無法對等互連 Databricks 的虛擬網路

若要解決此問題，請在 [ **Azure Databricks**] 底下設定虛擬網路對等互連，然後使用 [**資源識別碼**] 指定目標虛擬網路。 如需詳細資訊，請參閱將[Databricks 虛擬網路對等互連至遠端虛擬網路](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)。

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>遠端虛擬網路缺少閘道

當您將來自不同租使用者的虛擬網路和之後想要設定`Use Remote Gateways`的對等互連時，會發生此問題。 Azure 入口網站的限制是它無法驗證另一個租使用者之虛擬網路中的虛擬網路閘道是否存在。

有兩種方式可解決此問題：

 * 當您建立新的對`Use Remote Gateways`等互連時，請刪除對等互連並啟動選項。
 * 使用 PowerShell 或 CLI，而不是 Azure 入口網站，以啟用`Use Remote Gateways`。

## <a name="next-steps"></a>後續步驟

* [為 Azure VM 之間的連線問題疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
