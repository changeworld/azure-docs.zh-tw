---
title: 在 P2S 用戶端的輪輻 Vnet 中管理資源的安全存取
titleSuffix: Azure Virtual WAN
description: 本文可協助您使用 Azure 虛擬 WAN 和 Azure 防火牆規則，來管理對使用者 VPN (點對站) 用戶端的虛擬網路的安全存取。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 12/11/2020
ms.author: cherylmc
ms.openlocfilehash: c2efd9ac137c226c1d3a77e2cb6ebe17d75cb496
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051525"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>管理使用者 VPN 用戶端輪輻 Vnet 中資源的安全存取

本文說明如何使用虛擬 WAN 和 Azure 防火牆規則和篩選器來管理安全存取，以透過點對站 IKEv2 或開啟 VPN 連線來連線到 Azure 中的資源。 如果您有想要限制 Azure 資源存取權的遠端使用者，或在 Azure 中保護您的資源，這項設定會很有説明。

本文中的步驟可協助您建立下圖中的架構，以允許使用者 VPN 用戶端存取連線到虛擬中樞的輪輻 VNet 中的特定資源 (VM1) ，而不是 (VM2) 的其他資源。 使用此架構範例作為基本的指導方針。

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="圖：安全的虛擬中樞" :::

## <a name="prerequisites"></a>先決條件

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* 您有想要使用之驗證設定的可用值。 例如，RADIUS 伺服器、Azure Active Directory 驗證，或 [產生和匯出憑證](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)。

## <a name="create-a-virtual-wan"></a>建立虛擬 WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>定義 P2S 設定參數

點對站 (P2S) 設定會定義連接遠端用戶端的參數。 本節可協助您定義 P2S 設定參數，然後建立將用於 VPN 用戶端設定檔的設定。 您接下來的指示取決於您要使用的驗證方法。

### <a name="authentication-methods"></a>驗證方法

選取驗證方法時，您有三個選擇。 每個方法都有特定需求。 選取下列其中一種方法，然後完成步驟。

* **Azure Active Directory authentication：** 取得下列各項：

   * 您 Azure AD 租使用者中註冊的 Azure VPN 企業應用程式的 **應用程式識別碼** 。
   * **簽發者**。 範例： `https://sts.windows.net/your-Directory-ID`.
   * **Azure AD 的租** 使用者。 範例： `https://login.microsoftonline.com/your-Directory-ID`.

* **Radius 型驗證：** 取得 Radius 伺服器 IP、Radius 伺服器密碼和憑證資訊。

* **Azure 憑證：** 針對此設定，必須提供憑證。 您需要產生或取得憑證。 每個用戶端都需要用戶端憑證。 此外， (公開金鑰) 的根憑證資訊必須上傳。 如需所需憑證的詳細資訊，請參閱 [產生及匯出憑證](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)。

下列範例顯示 Azure 憑證驗證。

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>建立中樞和閘道

在本節中，您會建立具有點對站閘道的虛擬中樞。 設定時，您可以使用下列範例值：

* **中樞私人 IP 位址空間：** 10.0.0.0/24
* **用戶端位址集區：** >10.5.0.0/16
* **自訂 DNS 伺服器：** 您最多可以列出5部 DNS 伺服器

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>產生 VPN 用戶端組態檔

在本節中，您會產生並下載設定檔的設定檔。 這些檔案是用來設定用戶端電腦上的原生 VPN 用戶端。 如需用戶端設定檔內容的相關資訊，請參閱 [點對站設定-憑證](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md)。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>設定 VPN 用戶端

使用下載的設定檔來設定遠端存取用戶端。 每個作業系統的程序都不同，請遵循適用於您的系統的指示。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>連接輪輻 VNet

在本節中，您會將輪輻虛擬網路連接到虛擬 WAN 中樞。

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>建立虛擬機器

在本節中，您會在 VNet 中建立兩個 Vm： VM1 和 VM2。 在網狀圖中，我們會使用10.18.0.4 和10.18.0.5。 設定 Vm 時，請務必在 [網路功能] 索引標籤上選取您所建立的虛擬網路 () 。 如需建立 VM 的步驟，請參閱 [快速入門：建立 vm](../virtual-machines/windows/quick-create-portal.md)。

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>保護虛擬中樞

標準虛擬中樞沒有內建安全性原則可保護輪輻虛擬網路中的資源。 安全的虛擬中樞會使用 Azure 防火牆或協力廠商提供者來管理連入和連出流量，以保護您在 Azure 中的資源。

使用下列文章將中樞轉換成安全的中樞： [在虛擬 WAN 中樞中設定 Azure 防火牆](howto-firewall.md)。

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> 建立規則來管理和篩選流量

建立指示 Azure 防火牆行為的規則。 藉由保護中樞的安全，我們可確保輸入虛擬中樞的所有封包都受限於防火牆處理，才能存取您的 Azure 資源。

完成這些步驟之後，您將建立可讓 VPN 使用者使用私人 IP 位址10.18.0.4 存取 VM 的架構，但 **不能** 存取具有私人 ip 位址的 vm 10.18.0。5

1. 在 Azure 入口網站中，流覽至 [ **防火牆管理員**]。
1. 在 [安全性] 底下，選取 [ **Azure 防火牆原則**]。
1. 選取 [建立 Azure 防火牆原則]。
1. 在 [ **原則詳細資料**] 底下輸入名稱，然後選取您的虛擬中樞部署所在的區域。
1. 完成時，選取 下一步:**DNS 設定 (預覽)** 。
1. 完成時，選取 下一步:**規則**。
1. 在 [規則] 索引標籤上，選取 [新增規則集合]。
1. 提供集合的名稱。 將類型設定為 [ **網路**]。 新增優先順序值 **100**。
1. 填入規則、來源類型、來源、通訊協定、目的地埠和目的地類型的名稱，如下列範例所示。 然後，選取 [ **新增**]。 此規則可讓 VPN 用戶端集區中的任何 IP 位址存取具有私人 IP 位址10.18.04 的 VM，而不是任何其他連線到虛擬中樞的資源。 建立任何您想要的規則，以符合您所需的架構和許可權規則。

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="防火牆規則" :::

1. 完成時，選取 [下一步:威脅情報]。
1. 完成時，選取 [下一步:中樞]。
1. 在 [中樞] 索引標籤上，選取 [建立虛擬中樞的關聯]。
1. 選取您稍早建立的虛擬中樞，然後選取 [ **新增**]。
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。

此程式可能需要5分鐘或更長的時間才能完成。

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>透過 Azure 防火牆路由傳送流量

在本節中，您必須確保流量會透過 Azure 防火牆路由傳送。

1. 在入口網站中，從 [ **防火牆管理員**] 選取 [ **安全虛擬中樞**]。
1. 選取您建立的虛擬中樞。
1. 在 [設定] 下方，選取 [安全性設定]。
1. 在 [私人流量] 下，選取 [透過 Azure 防火牆傳送]。
1. 確認 VNet 連線和分支連接私人流量都受到 Azure 防火牆的保護。
1. 選取 [儲存]。

## <a name="validate"></a><a name="validate"></a>驗證

確認安全中樞的設定。

1. 從用戶端裝置透過 VPN 連線到 **安全虛擬中樞** 。
1. 從您的用戶端偵測 IP 位址 **10.18.0.4** 。 您應該會看到回應。
1. 從您的用戶端偵測 IP 位址 **10.18.0.5** 。 您應該看不到回應。

### <a name="considerations"></a>考量

* 請確定安全虛擬中樞上的 **有效路由表** ，有防火牆的下一個躍點可進行私人流量。 若要存取有效的路由表，請流覽至您的 **虛擬中樞** 資源。 在 [連線 **能力**] 底下，選取 [ **路由**]，然後選取 [ **有效路由**]。 從該處選取 **預設** 路由表。
* 確認您已在 [ [建立規則](#create-rules) ] 區段中建立規則。 如果缺少這些步驟，您所建立的規則將不會實際與中樞相關聯，且路由表和封包流程將不會使用 Azure 防火牆。

## <a name="next-steps"></a>下一步

* 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 常見問題集](virtual-wan-faq.md)。
* 如需 Azure 防火牆的詳細資訊，請參閱 [Azure 防火牆常見問題](../firewall/firewall-faq.yml)。
