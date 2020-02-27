---
title: 整合應用程式與 Azure 虛擬網路
description: 將 Azure App Service 中的應用程式與 Azure 虛擬網路整合。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648983"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合
本檔說明 Azure App Service 虛擬網路整合功能，以及如何使用[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)中的應用程式進行設定。 [Azure 虛擬網路][VNETOverview]（vnet）可讓您將許多 Azure 資源放在非網際網路可路由網路中。  

Azure App Service 有兩種變化。 

1. 支援全系列價格方案 (除隔離式方案以外) 的多租用戶系統
2. App Service 環境（ASE），它會部署到您的 VNet 並支援隔離的定價方案應用程式

本檔會逐步解說 VNet 整合功能，以供在多租使用者 App Service 中使用。 如果您的應用程式在[App Service 環境][ASEintro]中，則它已在 vnet 中，不需要使用 vnet 整合功能來觸達相同 vnet 中的資源。 如需所有 App Service 網路功能的詳細資訊，請參閱[App Service 網路功能](networking-features.md)

VNet 整合可讓您的 web 應用程式存取虛擬網路中的資源，但不會從 VNet 授與 web 應用程式的輸入私用存取權。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行輸出呼叫。 當搭配相同區域中的 Vnet 和其他區域中的 Vnet 使用時，VNet 整合功能的行為會有所不同。 VNet 整合功能有兩種變化。

1. 區域 VNet 整合-連線至相同區域中的 Resource Manager Vnet 時，您必須在要整合的 VNet 中具有專用的子網。 
2. 閘道所需的 VNet 整合-連線到位於相同區域的其他區域或傳統 VNet 的 Vnet 時，您需要在目標 VNet 中布建虛擬網路閘道。

VNet 整合功能：

* 需要標準、Premium、PremiumV2 或彈性 Premium 定價方案 
* 支援 TCP 和 UDP
* 使用 App Service 應用程式和函數應用程式

VNet 整合不支援的事項包括：

* 掛接磁碟機
* AD 整合 
* NetBios

閘道所需的 VNet 整合只提供存取目標 VNet 中的資源，或使用對等互連或 Vpn 連線到目標 VNet 的網路。 閘道所需的 VNet 整合無法存取 ExpressRoute 連線可用的資源，或與服務端點搭配運作。 

不論使用的版本為何，VNet 整合都會讓您的 web 應用程式存取虛擬網路中的資源，但不會授與從虛擬網路對 web 應用程式的輸入私用存取權。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行輸出呼叫。 

## <a name="enable-vnet-integration"></a>啟用 VNet 整合 

1. 在 App Service 入口網站中，移至網路 UI。 在 [VNet 整合] 底下，選取 *[按一下這裡設定]* 。 

1. 選取 [新增 VNet]。  

   ![選取 VNet 整合][1]

1. 下拉式清單會包含您的訂用帳戶中相同區域內的所有 Resource Manager Vnet，且如下所示，這是所有其他區域中所有 Resource Manager Vnet 的清單。 選取您想要整合的 VNet。

   ![選取 VNet][2]

   * 如果 VNet 位於相同的區域中，則請建立新的子網，或選擇空白的既有子網。 

   * 若要選取另一個區域中的 VNet，您必須在已啟用點對站的情況下布建虛擬網路閘道。

   * 若要與傳統 VNet 整合，請選取 [**按一下這裡以連線到傳統 vnet]** ，而不是按一下 [vnet] 下拉式選。 選取所需的傳統 VNet。 目標 VNet 必須已經布建虛擬網路閘道，並啟用點對站。

    ![選取傳統 VNet][3]
    
整合期間，會重新啟動您的應用程式。  當整合完成時，您會看到與您整合之 VNet 的詳細資料。 

當您的應用程式與 VNet 整合之後，它將會使用您的 VNet 設定所在的相同 DNS 伺服器，除非 Azure DNS 私人區域。 您目前無法搭配 Azure DNS 私人區域使用 VNet 整合。

## <a name="regional-vnet-integration"></a>區域 VNet 整合

使用區域 VNet 整合可讓您的應用程式存取：

* VNet 中與您整合的相同區域中的資源 
* Vnet 中的資源對等互連至位於相同區域的 VNet
* 服務端點保護的服務
* 跨 ExpressRoute 連線的資源
* 您所連線之 VNet 中的資源
* 跨對等互連連接的資源，包括 ExpressRoute 連線
* 私人端點 

搭配相同區域中的 Vnet 使用 VNet 整合時，您可以使用下列 Azure 網路功能：

* 網路安全性群組（Nsg）-您可以使用放在整合子網上的網路安全性群組來封鎖輸出流量。 輸入規則不適用，因為您無法使用 VNet 整合來提供 web 應用程式的輸入存取。
* 路由表（Udr）-您可以將路由表放在整合子網上，以在您想要的地方傳送輸出流量。 

根據預設，您的應用程式只會將 RFC1918 流量路由傳送至您的 VNet。 如果您想要將所有輸出流量路由傳送至 VNet，請將應用程式設定 WEBSITE_VNET_ROUTE_ALL 套用至您的應用程式。 若要設定應用程式設定：

1. 在您的應用程式入口網站中，移至 [設定] UI。 選取**新的應用程式設定**
1. 在 [名稱] 欄位中輸入**WEBSITE_VNET_ROUTE_ALL** ，然後在 [值] 欄位中鍵入**1**

   ![提供應用程式設定][4]

1. 選取 [確定]
1. 選取 [儲存]。

如果您將所有輸出流量路由傳送至 VNet，則會受限於套用至您的整合子網的 Nsg 和 Udr。 當您將所有輸出流量路由傳送至 VNet 時，您的輸出位址仍會是應用程式屬性中所列的輸出位址，除非您提供路由來傳送其他位置的流量。 

在相同的區域中使用 VNet 與 Vnet 的整合有一些限制：

* 您無法跨全球對等互連連線連線到資源
* 此功能僅適用于支援 PremiumV2 App Service 方案的較新 App Service 縮放單位。
* 整合子網只能由一個 App Service 方案使用
* App Service 環境中的隔離式方案應用程式無法使用此功能
* 此功能需要在 Resource Manager VNet 中有一個/27 的未使用子網，且其32位址或更大
* 應用程式和 VNet 必須位於相同的區域
* 您無法刪除具有整合式應用程式的 VNet。 請先移除整合，再刪除 VNet。 
* 您只能將與 web 應用程式相同的訂用帳戶中的 Vnet 整合
* 根據 App Service 方案，您只能有一個區域 VNet 整合。 相同 App Service 方案中的多個應用程式可以使用相同的 VNet。 
* 當有使用區域 VNet 整合的應用程式時，您無法變更應用程式或 App Service 方案的訂用帳戶

一個位址用於一個 App Service 方案執行個體。 如果您將應用程式調整為五個實例，則會使用五個位址。 因為子網大小在指派之後無法變更，所以您必須使用夠大的子網，以容納您的應用程式可能會達到的任何規模。 包含64位址的/26 是建議的大小。 包含64位址的/26 將可配合具有30個實例的 Premium App Service 方案。 當您相應增加或減少 App Service 方案時，您需要一小段時間才會有兩個位址。 

如果您想要讓應用程式在另一個 App Service 計畫連接到另一個 App Service 方案中已由應用程式連線的 VNet，您必須選取與既有的 VNet 整合所使用的子網不同的子網。  

適用于 Linux 的功能處於預覽狀態。 這項功能的 Linux 形式僅支援呼叫 RFC 1918 位址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16）。

### <a name="web-app-for-containers"></a>適用於容器的 Web 應用程式

如果您使用 Linux 上的 App Service 搭配內建映射，區域 VNet 整合就能運作，而不需要進行其他變更。 如果您使用用於容器的 Web App，您需要修改 docker 映射，才能使用 VNet 整合。 在您的 docker 映射中，使用埠環境變數作為主要 web 伺服器的接聽埠，而不是使用硬式編碼的埠號碼。 埠環境變數會在容器啟動時，由 App Service 平臺自動設定。 如果您使用 SSH，則在使用區域 VNet 整合時，SSH 背景程式必須設定為接聽 SSH_PORT 環境變數所指定的埠號碼。  Linux 上不支援閘道所需的 VNet 整合。 

### <a name="service-endpoints"></a>服務端點

區域 VNet 整合可讓您使用服務端點。  若要將服務端點與您的應用程式搭配使用，請使用區域 VNet 整合來連線到選取的 VNet，然後在您用於整合的子網上設定服務端點。 

### <a name="network-security-groups"></a>網路安全性群組

網路安全性群組可讓您封鎖 VNet 中資源的輸入和輸出流量。 使用區域 VNet 整合的 web 應用程式可以使用[網路安全性群組][VNETnsg]來封鎖對 VNet 或網際網路中資源的輸出流量。 若要封鎖公用位址的流量，您必須將應用程式設定 WEBSITE_VNET_ROUTE_ALL 設為1。 NSG 中的輸入規則並不適用于您的應用程式，因為 VNet 整合只會影響來自您應用程式的輸出流量。 若要控制 web 應用程式的輸入流量，請使用「存取限制」功能。 無論套用至您的整合子網的任何路由為何，套用至您的整合子網的 NSG 都會生效。 如果 WEBSITE_VNET_ROUTE_ALL 設定為1，而且您沒有任何路由影響到您的整合子網上的公用位址流量，則您的所有輸出流量仍會受限於指派給您的整合子網的 Nsg。 如果未設定 WEBSITE_VNET_ROUTE_ALL，Nsg 只會套用至 RFC1918 流量。

### <a name="routes"></a>路由

路由表可讓您將來自您應用程式的輸出流量路由傳送到您想要的任何位置。 根據預設，路由表只會影響您的 RFC1918 目的地流量。  如果您將 WEBSITE_VNET_ROUTE_ALL 設定為1，則所有的輸出呼叫都會受到影響。 在您的整合子網上設定的路由，不會影響對輸入應用程式要求的回復。 一般目的地可以包含防火牆裝置或閘道。 如果您想要路由傳送內部部署的所有輸出流量，您可以使用路由表將所有輸出流量傳送到您的 ExpressRoute 閘道。 如果您將流量路由傳送至閘道，請務必設定外部網路中的路由，以傳送任何回復。

邊界閘道協定（BGP）路由也會影響您的應用程式流量。 如果您有來自類似 ExpressRoute 閘道的 BGP 路由，您的應用程式輸出流量會受到影響。 根據預設，BGP 路由只會影響您的 RFC1918 目的地流量。 如果 WEBSITE_VNET_ROUTE_ALL 設定為1，則您的 BGP 路由可能會影響所有輸出流量。 

### <a name="how-regional-vnet-integration-works"></a>區域 VNet 整合的運作方式

App Service 中的應用程式會裝載于背景工作角色上。 基本和較高的定價方案是專用的主控方案，在同一背景工作角色上不會有其他客戶工作負載正在執行。 區域 VNet 整合的運作方式是使用委派子網中的位址掛接虛擬介面。 由於「來源」位址位於您的 VNet 中，因此它可以存取您 vnet 中的大部分內容，或透過您 vnet 中的 VM。 網路功能與在您的 VNet 中執行 VM 不同，這也是為什麼使用這項功能時無法使用某些網路功能的原因。

![區域 VNet 整合的運作方式][5]

啟用區域 VNet 整合時，您的應用程式仍會透過正常的相同通道，對網際網路進行輸出呼叫。 應用程式屬性入口網站中所列的輸出位址仍然是您的應用程式所使用的位址。 針對您的應用程式所做的變更，對服務端點保護服務或 RFC 1918 位址的呼叫會進入您的 VNet。 如果 WEBSITE_VNET_ROUTE_ALL 設定為1，則可以將所有輸出流量傳送至您的 VNet。 

此功能僅針對每個背景工作支援一個虛擬介面。  每個工作者一個虛擬介面，表示每個 App Service 方案一個區域 VNet 整合。 相同 App Service 方案中的所有應用程式都可以使用相同的 VNet 整合，但如果您需要應用程式來連線至其他 VNet，則需要建立另一個 App Service 方案。 使用的虛擬介面不是客戶可直接存取的資源。

由於這項技術的運作方式，與 VNet 整合搭配使用的流量不會顯示在網路監看員或 NSG 流量記錄中。  

## <a name="gateway-required-vnet-integration"></a>閘道所需的 VNet 整合

閘道所需的 VNet 整合支援連線至另一個區域中的 VNet，或連接至傳統 VNet。 閘道所需的 VNet 整合： 

* 讓應用程式一次只連接到1個 VNet
* 最多可在 App Service 方案內整合五個 Vnet 
* 可讓 App Service 方案中的多個應用程式使用相同的 VNet，而不會影響 App Service 方案所能使用的總次數。  如果您在相同的 App Service 方案中，有六個使用相同 VNet 的應用程式，則會計算為使用1個 VNet。 
* 因閘道上的 SLA 而支援99.9% 的 SLA
* 讓您的應用程式能夠使用已設定 VNet 的 DNS
* 需要以 SSTP 點對站 VPN 設定虛擬網路路由式閘道，才能將它連線到應用程式。 

您無法使用閘道所需的 VNet 整合：

* 使用 Linux 應用程式
* 使用與 ExpressRoute 連線的 VNet 
* 存取服務端點受保護的資源
* 具有同時支援 ExpressRoute 和點對站/站對站 Vpn 的共存閘道

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中設定閘道 ###

若要建立閘道：

1. 在您的 VNet 中[建立閘道子網][creategatewaysubnet]。  

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [設定點對站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站設定中停用 IKEV2，而且必須選取 SSTP。 點對站位址空間必須在 RFC 1918 位址區塊中，10.0.0.0/8、172.16.0.0/12、192.168.0.0/16

如果您只是建立要與 App Service VNet 整合搭配使用的閘道，則不需要上傳憑證。 建立閘道可能需要 30 分鐘。 在佈建閘道之前，您將無法將應用程式與 VNet 整合。 

### <a name="how-gateway-required-vnet-integration-works"></a>閘道所需的 VNet 整合運作方式

閘道所需的 VNet 整合是以點對站 VPN 技術為基礎。 點對站 Vpn 會將網路存取限制為僅裝載應用程式的虛擬機器。 應用程式會限制為僅透過混合式連線或透過 VNet 整合，將流量傳送至網際網路。 當您使用入口網站將應用程式設定為使用閘道所需的 VNet 整合時，會代表您管理複雜的協商，以在閘道和應用程式端建立及指派憑證。 最終結果是，用來裝載應用程式的工作者可以直接連線到所選 VNet 中的虛擬網路閘道。 

![閘道所需的 VNet 整合運作方式][6]

### <a name="accessing-on-premises-resources"></a>存取內部部署資源

應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果您使用閘道所需的 VNet 整合，您需要使用點對站位址區塊來更新內部部署 VPN 閘道路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 此做法的詳細資料會根據閘道而有所不同，不在這裡詳述。 您不能使用站對站 VPN 連線來設定 BGP。

區域 VNet 整合功能不需要額外的設定，即可透過您的 VNet 和內部部署進行。 您只需要使用 ExpressRoute 或站對站 VPN，將您的 VNet 連線到內部部署。 

> [!NOTE]
> 閘道所需的 VNet 整合功能不會將應用程式與具有 ExpressRoute 閘道的 VNet 整合。 即使在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法正常執行。 如果您需要透過 ExpressRoute 連線存取資源，您可以使用區域 VNet 整合功能，或在您的 VNet 中執行的[App Service 環境][ASE]。 
> 
> 

### <a name="peering"></a>對等互連

如果您使用與區域 VNet 整合的對等互連，則不需要執行任何額外的設定。 

如果您使用閘道所需的 VNet 與對等互連整合，您必須設定一些額外的專案。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許閘道器傳輸]。
1. 在 VNet 上新增對等互連連線，該 VNet 與您連線的 VNet 對等互連。 在目的地 VNet 上新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許遠端閘道器]。
1. 移至入口網站中的 [App Service 方案] > [網路] > [VNet 整合 UI]。  選取您的應用程式連線到的 VNet。 在路由區段中，新增與您的應用程式所連線到的 VNet 對等互連之 VNet 的位址範圍。  

## <a name="managing-vnet-integration"></a>管理 VNet 整合 

連接和中斷與 VNet 的連線是在應用層級。 可在多個應用程式之間影響 VNet 整合的作業是在 App Service 方案層級上。 從應用程式 > 網路 > VNet 整合入口網站中，您可以取得 VNet 的詳細資料。 您可以在 ASP > 網路 > VNet 整合入口網站中查看 ASP 層級的類似資訊。

您可以在 VNet 整合的應用程式檢視中採取的唯一作業，就是中斷連接目前連接到 VNet 的應用程式。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]。 當您從 VNet 中斷連線時，您的應用程式將重新啟動。 中斷連線不會變更您的 VNet。 不會移除子網或閘道。 如果您想要刪除 VNet，則需要先中斷應用程式與 VNet 的連線，並刪除其中的資源，例如閘道。 

ASP VNet 整合 UI 會顯示 ASP 中的應用程式所使用的所有 VNet 整合。 若要查看每個 VNet 的詳細資料，按一下您感興趣的 VNet 即可。 您可以在這裡針對閘道所需的 VNet 整合執行兩個動作。

* **同步網路**。 同步網路作業僅適用于與閘道相關的 VNet 整合功能。 執行同步網路作業可確保您的憑證和網路資訊同步。如果您新增或變更 VNet 的 DNS，則需要執行**同步網路**作業。 這項作業將重新啟動任何使用此 VNet 的應用程式。
* **新增路由** 新增路由將驅動輸出流量進入 VNet。 

**閘道所需的 VNet 整合路由**您的 VNet 中定義的路由會用來將流量從您的應用程式導向至您的 VNet。 如果需要將額外的輸出流量傳送到 VNet，則可以在此處新增這些位址區塊。 這項功能僅適用于閘道所需的 VNet 整合。 使用閘道所需的 VNet 整合時，路由表不會影響您的應用程式流量，其方式與區域 VNet 整合相同。

**閘道所需的 VNet 整合憑證**當閘道所需的 VNet 整合啟用時，會有必要的憑證交換，以確保連線的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。
如果憑證或網路資訊已變更，您需要按一下 [同步網路]。 按一下 [同步處理網路] 時，會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。 

## <a name="pricing-details"></a>價格詳細資料
除了 ASP 定價層費用以外，區域 VNet 整合功能不需額外付費。

使用閘道所需的 VNet 整合功能有三個相關費用：

* ASP 定價層費用-您的應用程式必須在標準、Premium 或 PremiumV2 App Service 方案中。 您可以在這裡看到這些成本的詳細資料： [App Service 定價][ASPricing]。 
* 資料傳輸成本-資料輸出需要付費，即使 VNet 位於相同的資料中心也一樣。 這些費用會在[資料傳輸定價詳細資料][DataPricing]中說明。 
* VPN 閘道成本-點對站 VPN 需要 VNet 閘道的成本。 詳細資料位於[VPN 閘道定價][VNETPricing]頁面。

## <a name="troubleshooting"></a>疑難排解
儘管功能易於設定，這不表示您的體驗不會遇到問題。 如果您在存取所需端點時遇到問題，有一些公用程式，您可以用來從應用程式主控台測試連線功能。 有兩個您可以使用的主控台。 一個是 Kudu 主控台，另一個則是 Azure 入口網站中的主控台。 若要從您的應用程式觸達 Kudu 主控台，請移至 [工具] -> [Kudu]。 您也可以連線到位於 [sitename]. azurewebsites. net 的 Kudo 主控台。 網站載入後，請移至 調試主控台 索引標籤。若要進入 Azure 入口網站裝載的主控台，請從您的應用程式移至 工具-> 主控台。 

#### <a name="tools"></a>工具
由於安全性限制，工具**ping**、 **nslookup**和**tracert**無法透過主控台來處理。 為了填滿 void，已加入兩個不同的工具。 為了測試 DNS 功能，已加入名為 nameresolver.exe的工具。 語法為：

    nameresolver.exe hostname [optional: DNS Server]

您可以使用 **nameresolver** 來檢查應用程式依賴的主機名稱。 如此一來您可以測試是否有任何項目未正確設定，而無法與 DNS 搭配，或可能對 DNS 伺服器沒有存取權。 您可以查看您的應用程式將在主控台中使用的 DNS 伺服器，方法是查看環境變數 WEBSITE_DNS_SERVER 並 WEBSITE_DNS_ALT_SERVER。

下一個工具可讓您測試主機是否可與 TCP 連線，以及連接埠組合。 此工具稱為 **tcpping**，語法如下：

    tcpping.exe hostname [optional: port]

**tcpping** 公用程式會讓您知道是否可以連接特定主機和連接埠。 只有在下列情況才會顯示成功：有一個應用程式監聽主機和連接埠組合，並且有網路從您的應用程式存取到指定的主機和連接埠。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>偵錯 VNet 裝載資源的存取
有一些事物，可以阻止您的應用程式觸達特定的主機和連接埠。 大部分的情況下，會是下列三個情況的其中一種︰

* **路線中有防火牆。** 如果路線中有防火牆，則將會達到 TCP 逾時。 此案例中 TCP 逾時值就是 21 秒。 使用 **tcpping** 工具來測試連線能力。 TCP 逾時可能是因為許多項目越過防火牆並啟動。 
* **DNS 無法存取。** DNS 逾時值為三秒 (每部 DNS 伺服器)。 如果您有兩部 DNS 伺服器，逾時為 6 秒。 使用 nameresolver 來檢查 DNS 是否運作。 請記住，您不能使用 nslookup，因為它不會使用您的 VNet 設定使用的 DNS。 如果無法存取，您可能會有防火牆或 NSG 封鎖對 DNS 的存取，或可能已關閉。

如果這些專案不會回答您的問題，請先查看如下所示的內容： 

**區域 VNet 整合**
* 您的目的地為非 RFC1918 位址，而且您沒有 WEBSITE_VNET_ROUTE_ALL 設為1
* 是否有 NSG 封鎖來自您整合子網的輸出
* 如果跨 ExpressRoute 或 VPN 進行，您的內部部署閘道是否已設定為將流量備份至 Azure？ 如果您可以觸達 VNet 中的端點，但不能連線到內部部署，請檢查您的路由。
* 您有足夠的許可權可設定整合子網上的委派嗎？ 在區域 VNet 整合設定期間，您的整合子網將會委派給 Microsoft Web。 VNet 整合 UI 會自動將子網委派給 Microsoft Web。 如果您的帳戶沒有足夠的網路許可權來設定委派，您將需要可在整合子網上設定屬性以委派子網的人員。 若要手動委派整合子網，請移至 Azure 虛擬網路子網 UI，並設定適用于 Microsoft Web 的委派。 

**閘道所需的 VNet 整合**
* RFC 1918 範圍中的點對站位址範圍（10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255）？
* 閘道是否顯示為正在入口網站中啟動？ 如果您的閘道已關閉，請重新啟動。
* 憑證是否顯示為同步，或您是否懷疑網路設定已變更？  如果您的憑證未同步，或您懷疑 VNet 設定已變更，但未與您的 Asp 同步，則點擊 [同步網路]。
* 如果跨 VPN 進行，內部部署閘道是否已設定為將流量備份至 Azure？ 如果您可以觸達 VNet 中的端點，但不能連線到內部部署，請檢查您的路由。
* 您嘗試使用同時支援點對站和 ExpressRoute 的共存閘道嗎？ VNet 整合不支援共存閘道 

調試網路問題是一項挑戰，因為您無法看到封鎖存取特定主機：埠組合的原因。 部分原因包括：

* 您已在主機上啟動防火牆，防止從點對站 IP 範圍存取應用程式連接埠。 跨子網路通常需要公用存取權。
* 目標主機已關閉
* 應用程式已關閉
* IP 或主機名稱錯誤
* 應用程式不是接聽您預期的連接埠。 您可以在端點主機上使用 "netstat -aon"，將處理序識別碼與接聽連接埠進行比對。 
* 您配置網路安全性群組的方式，讓這些群組防止從點對站 IP 範圍存取應用程式主機和連接埠

請記住，您不知道應用程式會實際使用的位址。 它可以是整合子網或點對站位址範圍中的任何位址，因此您需要允許來自整個位址範圍的存取。 

其他偵錯步驟包括：

* 連線至 VNet 中的 VM，並嘗試從該處連接資源 host:port。 若要測試 TCP 存取，請使用 PowerShell 命令 **test-netconnection**。 語法為：

      test-netconnection hostname [optional: -Port]

* 在 VM 上啟動應用程式，並使用**tcpping**從您的應用程式中的主控台測試該主機和埠的存取權

#### <a name="on-premises-resources"></a>內部部署資源 ####

如果您的應用程式無法連接內部部署資源，則請檢查您是否可以從 VNet 連接資源。 使用 **test-netconnection** PowerShell 命令來檢查是否有 TCP 存取權。 如果您的 VM 無法連線到您的內部部署資源，您的 VPN 或 ExpressRoute 連線可能未正確設定。

如果 VNet 裝載的 VM 可以連接您的內部部署系統，但您的應用程式無法連接此系統，則很可能是下列其中一個原因：

* 您的路由未設定您的子網或內部部署閘道中的點對站位址範圍
* 網路安全性群組將封鎖點對站 IP 範圍的存取
* 內部部署防火牆將封鎖來自點對站 IP 範圍的流量
* 您正嘗試使用區域 VNet 整合功能來觸及非 RFC 1918 位址


## <a name="automation"></a>自動化

有適用于區域 VNet 整合的 CLI 支援。 若要存取下列命令，請[安裝 Azure CLI][installCLI]。 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

針對閘道所需的 VNet 整合，您可以使用 PowerShell 將 App Service 與 Azure 虛擬網路整合。 如需隨時可執行的指令碼，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) \(英文\)。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
