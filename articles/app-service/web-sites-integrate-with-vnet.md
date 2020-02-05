---
title: 整合應用程式與 Azure 虛擬網路
description: 瞭解 Azure App Service 如何與 Azure 虛擬網路整合，以及如何將應用程式連線到虛擬網路。
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: 472fe621fc7a95317f143ef96a1d7f8b5adfe581
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016964"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>將您的應用程式與 Azure 虛擬網路整合
本檔說明 Azure App Service 虛擬網路整合功能，以及如何使用[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)中的應用程式進行設定。 [Azure 虛擬網路][VNETOverview]（vnet）可讓您將許多 Azure 資源放在非網際網路可路由網路中。  

Azure App Service 有兩種變化。 

1. 支援全系列價格方案 (除隔離式方案以外) 的多租用戶系統
2. App Service 環境（ASE），它會部署到您的 VNet 並支援隔離的定價方案應用程式

本檔會逐步解說兩個 VNet 整合功能，以用於多租使用者 App Service。 如果您的應用程式在[App Service 環境][ASEintro]中，則它已在 vnet 中，不需要使用 vnet 整合功能來觸達相同 vnet 中的資源。 如需所有 App Service 網路功能的詳細資訊，請參閱[App Service 網路功能](networking-features.md)

VNet 整合功能有兩種形式

1. 一個版本可讓您與相同區域中的 Vnet 整合。 這種形式的功能需要相同區域中 VNet 內的子網。 這項功能仍處於預覽狀態，但對於 Windows 應用程式生產工作負載而言，其支援如下所述的一些注意事項。
2. 另一個版本可讓您與其他區域中的 Vnet 或傳統 Vnet 整合。 此版本的功能需要將虛擬網路閘道部署至您的 VNet。 這是點對站 VPN 功能，只支援 Windows 應用程式。

應用程式一次只能使用一種形式的 VNet 整合功能。 問題是，您應該使用哪一項功能。 您可以使用其中一種來進行許多事項。 但明顯的差異如下：

| 問題  | 解決方案 | 
|----------|----------|
| 想要在相同的區域中達到 RFC 1918 位址（10.0.0.0/8、172.16.0.0/12、192.168.0.0/16） | 區域 VNet 整合 |
| 想要觸及傳統 VNet 或另一個區域中的 VNet 中的資源 | 閘道所需的 VNet 整合 |
| 想要透過 ExpressRoute 觸及 RFC 1918 端點 | 區域 VNet 整合 |
| 想要跨服務端點存取資源 | 區域 VNet 整合 |

這兩項功能都不能讓您跨 ExpressRoute 達到非 RFC 1918 位址。 若要這麼做，您現在必須使用 ASE。

使用區域 VNet 整合並不會將您的 VNet 連線到內部部署或設定服務端點。 這是不同的網路設定。 區域 VNet 整合可讓您的應用程式在這些連線類型之間進行呼叫。

不論使用的版本為何，VNet 整合都會讓您的 web 應用程式存取虛擬網路中的資源，但不會授與從虛擬網路對 web 應用程式的輸入私用存取權。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行輸出呼叫。 

VNet 整合功能：

* 需要「標準」、「進階」或「進階 V2」價格方案 
* 支援 TCP 和 UDP
* 適用于 App Service 應用程式和函數應用程式

VNet 整合不支援的事項包括：

* 掛接磁碟機
* AD 整合 
* NetBios

## <a name="regional-vnet-integration"></a>區域 VNet 整合 

> [!NOTE]
> 對等互連尚未提供以 Linux 為基礎的 App Service。
>

當 VNet 整合與您的應用程式位於相同區域中的 Vnet 使用時，它需要使用至少具有32個位址的委派子網。 子網不能用於其他任何專案。 從您的應用程式發出的輸出呼叫將會從委派子網中的位址進行。 當您使用此版本的 VNet 整合時，會從您 VNet 中的位址進行呼叫。 使用 VNet 中的位址可讓您的應用程式執行下列動作：

* 對服務端點安全服務進行呼叫
* 跨 ExpressRoute 連線存取資源
* 存取您所連線之 VNet 中的資源
* 跨對等互連連接（包括 ExpressRoute 連線）存取資源

這項功能目前為預覽狀態，但支援 Windows 應用程式生產工作負載，但有下列限制：

* 您只能到達 RFC 1918 範圍內的位址。 這些是 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16 位址區塊中的位址。
* 您無法跨全球對等互連連線連線到資源
* 您不能將來自您應用程式的流量路由傳送到您的 VNet
* 此功能僅適用于支援 PremiumV2 App Service 方案的較新 App Service 縮放單位。 請注意，這並不表示您的應用程式必須在 PremiumV2 SKU 上執行，而且必須在可使用 PremiumV2 選項的 App Service 方案上執行（這表示它是較新的縮放單位，其中也提供此 VNet 整合功能）。
* 整合子網只能由一個 App Service 方案使用
* App Service 環境中的隔離式方案應用程式無法使用此功能
* 此功能需要在您的 Resource Manager VNet 中有一個/27 的未使用子網，且其32位址或更大
* 應用程式和 VNet 必須位於相同的區域
* 您無法刪除具有整合式應用程式的 VNet。 您必須先移除整合 
* 根據 App Service 方案，您只能有一個區域 VNet 整合。 相同 App Service 方案中的多個應用程式可以使用相同的 VNet。 
* 當有使用區域 VNet 整合的應用程式時，您無法變更應用程式或 App Service 方案的訂用帳戶

一個位址用於一個 App Service 方案執行個體。 如果您將應用程式調整為5個實例，則會使用5個位址。 因為子網大小在指派之後無法變更，所以您必須使用夠大的子網，以容納您的應用程式可能會達到的任何規模。 包含64位址的/26 是建議的大小。 如果您未變更 App Service 計畫的大小，則包含32位址的/27 可配合高階 App Service 規劃20個實例。 當您相應增加或減少 App Service 方案時，您需要一小段時間才會有兩個位址。 

如果您想要讓應用程式在另一個 App Service 計畫連接到另一個 App Service 方案中已由應用程式連線的 VNet，您必須選取與既有的 VNet 整合所使用的子網不同的子網。  

針對 Linux，此功能也處於預覽狀態。 搭配相同區域中的 Resource Manager VNet 使用 VNet 整合功能：

1. 移至入口網站中的 [網路 UI]。 如果您的應用程式可以使用新功能，您會看到新增 VNet （預覽）的選項。  

   ![選取 VNet 整合][6]

1. 選取 [新增 VNet (預覽)]。  

1. 選取您想要與其整合的 Resource Manager VNet，然後建立新的子網路，或選擇一個空白的現有子網路。 整合只需不到一分鐘即可完成。 整合期間，會重新啟動您的應用程式。  完成整合之後，您將在整合的 VNet 上看到詳細資料，頂端有一個橫幅，告訴您此功能處於預覽狀態。

   ![選取 [VNet 和子網路]][7]

當您的應用程式與 VNet 整合之後，它會使用您的 VNet 所設定的相同 DNS 伺服器。 

區域 VNet 整合需要將您的整合子網委派給 Microsoft Web。  VNet 整合 UI 會自動將子網委派給 Microsoft Web。 如果您的帳戶沒有足夠的網路許可權來設定此功能，您將需要可在整合子網上設定屬性的人員，才能委派子網。 若要手動委派整合子網，請移至 Azure 虛擬網路子網 UI，並設定適用于 Microsoft Web 的委派。

若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]。 這會重新啟動 Web 應用程式。 


#### <a name="web-app-for-containers"></a>Web App for Containers

如果您將 Linux 上的 App Service 與內建映射搭配使用，區域 VNet 整合功能就能運作，而不需要進行其他變更。 如果您使用用於容器的 Web App，您需要修改 docker 映射，才能使用 VNet 整合。 在您的 docker 映射中，使用埠環境變數作為主要 web 伺服器的接聽埠，而不是使用硬式編碼的埠號碼。 埠環境變數會在容器啟動時，由 App Service 平臺自動設定。 如果您使用 SSH，則在使用區域 VNet 整合時，SSH 背景程式必須設定為接聽 SSH_PORT 環境變數所指定的埠號碼。

### <a name="service-endpoints"></a>服務端點

新的 VNet 整合功能可讓您使用服務端點。  若要在應用程式中使用服務端點，請使用新的 VNet 整合連線到選取的 VNet，然後在您用於整合的子網路上設定服務端點。 


### <a name="how-vnet-integration-works"></a>VNet 整合的運作方式

App Service 中的應用程式會裝載于背景工作角色上。 基本和較高的定價方案是專用的主控方案，在同一背景工作角色上不會有其他客戶工作負載正在執行。 VNet 整合的運作方式是使用委派子網中的位址掛接虛擬介面。 由於「來源」位址位於您的 VNet 中，因此它可以存取您的 VNet 中的大部分專案，就像您 VNet 中的 VM 一樣。 網路功能與在您的 VNet 中執行 VM 不同，這也是為什麼使用這項功能時無法使用某些網路功能的原因。

![VNet 整合](media/web-sites-integrate-with-vnet/vnet-integration.png)

啟用 VNet 整合時，您的應用程式仍會透過與正常相同的通道，對網際網路進行輸出呼叫。 應用程式屬性入口網站中所列的輸出位址仍然是您的應用程式所使用的位址。 針對您的應用程式所做的變更，對服務端點保護服務或 RFC 1918 位址的呼叫會進入您的 VNet。 

此功能僅針對每個背景工作支援一個虛擬介面。  每個工作者一個虛擬介面，表示每個 App Service 方案一個區域 VNet 整合。 相同 App Service 方案中的所有應用程式都可以使用相同的 VNet 整合，但如果您需要應用程式來連線至其他 VNet，則需要建立另一個 App Service 方案。 使用的虛擬介面不是客戶可直接存取的資源。

由於這項技術的運作方式，與 VNet 整合搭配使用的流量不會顯示在網路監看員或 NSG 流量記錄中。  

## <a name="gateway-required-vnet-integration"></a>閘道所需的 VNet 整合 

閘道所需的 VNet 整合功能：

* 可以用來連接到 Resource Manager 或傳統 Vnet 的任何區域中的 Vnet
* 讓應用程式一次只連接到1個 VNet
* 最多可在 App Service 方案中與五個 Vnet 整合 
* 可讓 App Service 方案中的多個應用程式使用相同的 VNet，而不會影響 App Service 方案所能使用的總次數。  如果您有6個應用程式在相同的 App Service 方案中使用相同的 VNet，則會計算為使用1個 VNet。 
* 需要使用點對站 VPN 設定的虛擬網路閘道
* 因閘道上的 SLA 而支援99.9% 的 SLA

此功能不支援：
* 搭配 Linux 應用程式使用
* 透過 ExpressRoute 存取資源 
* 跨服務端點存取資源 

### <a name="getting-started"></a>開始使用

在將 Web 應用程式連接至虛擬網路之前，請留意以下事項：

* 目標虛擬網路必須先以路由式閘道啟用點對站 VPN，才能使該虛擬網路與應用程式連接。 
* VNet 與您的 App Service 方案 (ASP) 必須位於同一個訂用帳戶中。
* 與 VNet 整合的應用程式將使用指定給該 VNet 的 DNS。

### <a name="set-up-a-gateway-in-your-vnet"></a>在 VNet 中設定閘道 ###

如果您已設定點對站位址的閘道，則可以略過設定與應用程序的 VNet 整合。  
若要建立閘道：

1. 在您的 VNet 中[建立閘道子網][creategatewaysubnet]。  

1. [建立 VPN 閘道][creategateway]。 選取路由式 VPN 類型。

1. [設定點對站位址][setp2saddresses]。 如果閘道不在基本 SKU 中，則必須在點對站組態中停用 IKEV2，而且必須選取 SSTP。 位址空間必須在 RFC 1918 位址區塊中，10.0.0.0/8、172.16.0.0/12、192.168.0.0/16

如果您只是建立要與 App Service VNet 整合搭配使用的閘道，則不需要上傳憑證。 建立閘道可能需要 30 分鐘。 在佈建閘道之前，您將無法將應用程式與 VNet 整合。 

### <a name="configure-vnet-integration-with-your-app"></a>設定您的應用程式與 VNet 整合 

若要在您的應用程式上啟用 VNet 整合： 

1. 移至 Azure 入口網站中的應用程式並開啟應用程式設定，然後選取 [網路] > [VNet 整合]。 您的 ASP 必須是標準 SKU，或更適合使用任一 VNet 整合功能。 
 ![VNet 整合 UI][1]

1. 選取 [新增 VNet]。 
 ![新增 VNet 整合][2]

1. 選取您的 VNet。 
  ![選取您的 VNet][8]
  
在最後一個步驟之後，您的應用程式將會重新啟動。  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>閘道所需的 VNet 整合功能運作方式

閘道所需的 VNet 整合功能是以點對站 VPN 技術為基礎。 點對站技術可將網路存取限制為僅限裝載應用程式的虛擬機器。 應用程式會限制為僅透過混合式連線或透過 VNet 整合，將流量傳送至網際網路。 

![VNet 整合的運作方式][3]

## <a name="managing-vnet-integration"></a>管理 VNet 整合
要在應用程式層級上，才能連接或中斷連接 VNet。 可在多個應用程式之間影響 VNet 整合的作業是在 App Service 方案層級上。 從應用程式 > 網路 > VNet 整合入口網站中，您可以取得 VNet 的詳細資料。 您可以在 ASP > 網路 > VNet 整合入口網站的 ASP 層級中看到類似的資訊，包括該 App Service 方案中的哪些應用程式使用指定的整合。

 ![VNet 詳細資料][4]

您在 VNet 整合 UI 中提供的資訊在應用程式與 ASP 入口網站之間是相同的。

* VNet 名稱 - 虛擬網路 UI 的連結
* 位置 - 這會反映 VNet 的位置。 在另一個位置與 VNet 整合，可能會導致您的應用程式出現延遲問題。 
* 憑證狀態 - 反映您的憑證是否在 App Service 方案和 VNet 之間同步處理。
* 閘道狀態-如果您要使用閘道所需的 VNet 整合，您可以看到閘道狀態。
* VNet 位址空間 - 顯示 VNet 的 IP 位址空間。 
* 點對站位址空間 - 顯示 VNet 的點對站 IP 位址空間。 當您使用 [需要閘道] 功能呼叫 VNet 時，來自位址的應用程式將會是這些位址的其中一個。 
* 站對站位址空間：您可以使用站對站 VPN，將 VNet 連線到內部部署資源或連線到其他 VNet。 使用該 VPN 連線定義的 IP 範圍，如此處所示。
* DNS 伺服器 - 顯示使用 VNet 設定的 DNS 伺服器。
* 路由傳送至 VNet 的 IP 位址 - 顯示路由傳送至 VNet 的位址區塊 (用於驅動流量進入 VNet) 

您可以在 VNet 整合的應用程式檢視中採取的唯一作業，就是中斷連接目前連接到 VNet 的應用程式。 若要中斷您應用程式與 VNet 的連線，請選取 [中斷連線]。 當您從 VNet 中斷連線時，您的應用程式將重新啟動。 中斷連線不會變更您的 VNet。 不會移除子網或閘道。 如果您想要刪除 VNet，則需要先中斷應用程式與 VNet 的連線，並刪除其中的資源，例如閘道。 

若要觸達 ASP VNet 整合 UI，請開啟您的 ASP UI 並選取 [網路]。  在 [VNet 整合] 下，選取 [按一下這裡以設定] 以開啟 [網路功能狀態 UI]。

![ASP VNet 整合資訊][5]

ASP VNet 整合 UI 會向您顯示 ASP 中的應用程式所使用的所有 VNet。 若要查看每個 VNet 的詳細資料，按一下您感興趣的 VNet 即可。 您可以在此處執行兩項動作。

* **同步網路**。 同步網路作業僅適用于與閘道相關的 VNet 整合功能。 執行同步網路作業可確保您的憑證和網路資訊同步。如果您新增或變更 VNet 的 DNS，則需要執行**同步網路**作業。 這項作業將重新啟動任何使用此 VNet 的應用程式。
* **新增路由** 新增路由將驅動輸出流量進入 VNet。

**路由** VNet 中所定義的路由用於將應用程式中的流量導向至 VNet。 如果需要將額外的輸出流量傳送到 VNet，則可以在此處新增這些位址區塊。 這項功能僅適用于閘道所需的 VNet 整合。

**憑證**當閘道所需的 VNet 整合啟用時，會有必要的憑證交換，以確保連線的安全性。 除了憑證以外，還有 DNS 組態、路由，以及其他描述網路的類似項目。
如果憑證或網路資訊已變更，您需要按一下 [同步網路]。 按一下 [同步處理網路] 時，會導致應用程式與 VNet 之間的連線短暫中斷。 如果應用程式未重新啟動，失去連線會導致您的網站無法正常運作。 

## <a name="accessing-on-premises-resources"></a>存取內部部署資源
應用程式可以透過與具有站對站連線的 VNnet 整合以存取內部部署資源。 如果您使用閘道所需的 VNet 整合，您需要使用點對站位址區塊來更新內部部署 VPN 閘道路由。 第一次設定站對站 VPN 時，用來設定它的指令碼應該設定路由，包括點對站 VPN。 如果您在建立站對站 VPN 之後新增點對站 VPN，則需要手動更新路由。 此做法的詳細資料會根據閘道而有所不同，不在這裡詳述。 您不能使用站對站 VPN 連線來設定 BGP。

區域 VNet 整合功能不需要額外的設定，即可透過您的 VNet 和內部部署進行。 您只需要使用 ExpressRoute 或站對站 VPN，將您的 VNet 連線到內部部署。 

> [!NOTE]
> 閘道所需的 VNet 整合功能不會將應用程式與具有 ExpressRoute 閘道的 VNet 整合。 即使在[共存模式][VPNERCoex]中設定 ExpressRoute 閘道，VNet 整合仍無法正常執行。 如果您需要透過 ExpressRoute 連線存取資源，您可以使用區域 VNet 整合功能，或在您的 VNet 中執行的[App Service 環境][ASE]。 
> 
> 

## <a name="peering"></a>對等互連
如果您使用與區域 VNet 整合的對等互連，則不需要執行任何額外的設定。 

如果您使用閘道所需的 VNet 與對等互連整合，您必須設定一些額外的專案。 若要設定對等互連以搭配您的應用程式使用：

1. 在您的應用程式連線到的 VNet 上新增對等互連連線。 新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許閘道器傳輸]。
1. 在 VNet 上新增對等互連連線，該 VNet 與您連線的 VNet 對等互連。 在目的地 VNet 上新增對等互連連線時，啟用 [允許虛擬網路存取] 並核取 [允許轉寄的流量] 和 [允許遠端閘道器]。
1. 移至入口網站中的 [App Service 方案] > [網路] > [VNet 整合 UI]。  選取您的應用程式連線到的 VNet。 在路由區段中，新增與您的應用程式所連線到的 VNet 對等互連之 VNet 的位址範圍。  


## <a name="pricing-details"></a>定價詳細資料
除了 ASP 定價層費用以外，區域 VNet 整合功能不需額外付費。

使用閘道所需的 VNet 整合功能有三個相關費用：

* ASP 定價層費用-您的應用程式必須在標準、Premium 或 PremiumV2 App Service 方案中。 您可以在這裡看到這些成本的詳細資料： [App Service 定價][ASPricing]。 
* 資料傳輸成本-資料輸出需要付費，即使 VNet 位於相同的資料中心也一樣。 這些費用會在[資料傳輸定價詳細資料][DataPricing]中說明。 
* VPN 閘道成本-點對站 VPN 需要 VNet 閘道的成本。 詳細資料位於[VPN 閘道定價][VNETPricing]頁面。


## <a name="troubleshooting"></a>疑難排解
儘管功能易於設定，這不表示您的體驗不會遇到問題。 如果您在存取所需端點時遇到問題，有一些公用程式，您可以用來從應用程式主控台測試連線功能。 有兩個您可以使用的主控台。 一個是 Kudu 主控台，另一個則是 Azure 入口網站中的主控台。 若要從您的應用程式觸達 Kudu 主控台，請移至 [工具] -> [Kudu]。 您也可以連線到位於 [sitename]. azurewebsites. net 的 Kudo 主控台。 網站載入後，請移至 調試主控台 索引標籤。若要進入 Azure 入口網站裝載的主控台，請從您的應用程式移至 工具-> 主控台。 

#### <a name="tools"></a>工具
由於安全性限制，工具 **ping**、**nslookup** 和 **tracert** 無法透過主控台運作。 為了填滿此空隙，已加入兩個不同的工具。 為了測試 DNS 功能，已加入名為 nameresolver.exe的工具。 語法為：

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
* 您的目的地是 RFC 1918 位址
* 是否有 NSG 封鎖來自您整合子網的輸出
* 如果跨 ExpressRoute 或 VPN 進行，您的內部部署閘道是否已設定為將流量備份至 Azure？ 如果您可以觸達 VNet 中的端點，但不能連線到內部部署，這就很適合進行檢查。

**閘道所需的 VNet 整合**
* RFC 1918 範圍中的點對站位址範圍（10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255）？
* 閘道是否顯示為正在入口網站中啟動？ 如果您的閘道已關閉，請重新啟動。
* 憑證是否顯示為同步，或您是否懷疑網路設定已變更？  如果您的憑證未同步，或您懷疑 VNet 設定已變更，但未與您的 Asp 同步，則點擊 [同步網路]。
* 如果跨 ExpressRoute 或 VPN 進行，您的內部部署閘道是否已設定為將流量備份至 Azure？ 如果您可以觸達 VNet 中的端點，但不能連線到內部部署，這就很適合進行檢查。

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


## <a name="powershell-automation"></a>PowerShell 自動化

您可以使用 PowerShell 將 App Service 與 Azure 虛擬網路整合。 如需隨時可執行的指令碼，請參閱[將 Azure App Service 中的應用程式連線到 Azure 虛擬網路](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) \(英文\)。


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

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
