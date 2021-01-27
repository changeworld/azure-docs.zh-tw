---
title: 使用 P2S 的遠端工作： Azure VPN 閘道
description: 此頁面說明如何利用 VPN 閘道，讓您能夠從遠端工作，因為 COVID-19-19 流感。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880079"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>使用 Azure VPN 閘道點對站的遠端工作

>[!NOTE]
>本文說明如何利用 Azure VPN 閘道、Azure、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作，並減輕因 COVID-19-19 危機所面臨的網路問題。
>

本文說明可供組織為其使用者設定遠端存取的選項，或在 COVID-19-19 epidemic 期間以額外容量補充其現有解決方案的選項。

Azure 點對站解決方案是以雲端為基礎，而且可以快速布建，以滿足使用者從家裡工作的增加需求。 當不再需要增加的容量時，就可以輕鬆且快速地擴大和關閉。

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>關於點對站 VPN

點對站 (P2S) VPN 閘道連線可讓您建立從個別用戶端電腦到您的虛擬網路的安全連線。 P2S 連線的建立方式是從用戶端電腦開始。 此解決方案適用于想要從遠端位置（例如從家中或會議）連線到 Azure Vnet 或內部部署資料中心的遠端工作者。 本文說明如何讓使用者根據各種案例從遠端工作。

下表顯示用戶端作業系統和這些系統可用的驗證選項。 根據已在使用中的用戶端作業系統，選取驗證方法會很有説明。 例如，如果您有混合的用戶端作業系統需要連線，請選取 [使用憑證型驗證的 OpenVPN]。 此外，請注意，點對站 VPN 僅支援路由式 VPN 閘道。

![顯示用戶端作業系統和可用驗證選項的螢幕擷取畫面。](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>案例 1-使用者只需要存取 Azure 中的資源

在此案例中，遠端使用者只需要存取 Azure 中的資源。

![此圖顯示僅需要存取 Azure 資源的使用者的點對站案例。](./media/working-remotely-support/scenario1.png "案例 1")

概括而言，您必須執行下列步驟，才能讓使用者安全地連線至 Azure 資源：

1. 建立虛擬網路閘道 (（如果不存在）) 。
2. 在閘道上設定點對站 VPN。

   * 針對憑證驗證，請遵循 [此連結](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)。
   * 若為 OpenVPN，請遵循 [此連結](vpn-gateway-howto-openvpn.md)。
   * 針對 Azure AD 驗證，請遵循 [此連結](openvpn-azure-ad-tenant.md)。
   * 針對點對站連線進行疑難排解，請遵循 [此連結](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
3. 下載並散發 VPN 用戶端設定。
4. 如果已選取) 給用戶端的憑證驗證，則將憑證散發 (。
5. 連接到 Azure VPN。

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>案例 2-使用者需要存取 Azure 和/或內部內部部署資源中的資源

在此案例中，遠端使用者需要存取 Azure 中的資源，以及內部部署資料中心 (s) 的資源。

![針對需要存取 Azure 資源的使用者，顯示點對站案例的圖表。](./media/working-remotely-support/scenario2.png "案例 2")

概括而言，您必須執行下列步驟，才能讓使用者安全地連線至 Azure 資源：

1. 建立虛擬網路閘道 (（如果不存在）) 。
2. 在閘道上設定點對站 VPN (查看 [案例 1](#scenario1)) 。
3. 在已啟用 BGP 的 Azure 虛擬網路閘道上設定站對站通道。
4. 設定內部部署裝置以連接到 Azure 虛擬網路閘道。
5. 從 Azure 入口網站下載點對站設定檔，並將其散發給用戶端

若要瞭解如何設定站對站 VPN 通道，請參閱 [此連結](./tutorial-site-to-site-portal.md)。

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>原生 Azure 憑證驗證的常見問題集

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 驗證的常見問題集

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>後續步驟

* [設定 P2S 連接-Azure AD authentication](openvpn-azure-ad-tenant.md)

* [設定 P2S 連線 - RADIUS 驗證](point-to-site-how-to-radius-ps.md)

* [設定 P2S 連線 - Azure 原生憑證驗證](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" 是 OpenVPN Inc. 的商標。**