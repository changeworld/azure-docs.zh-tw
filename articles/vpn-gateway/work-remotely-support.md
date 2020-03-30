---
title: 遠端使用 P2S：Azure VPN 閘道
description: 本頁介紹如何利用 Azure 堡壘啟用由於 COVID-19 大流行而遠端工作。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337111"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>使用 Azure VPN 閘道點對點的遠端工作

>[!NOTE]
>本文介紹如何利用 Azure VPN 閘道、Azure、Microsoft 網路和 Azure 合作夥伴生態系統遠端工作並緩解因 COVID-19 危機而面臨的網路問題。
>

本文介紹了組織在 COVID-19 流行期間為使用者設置遠端存取或以額外容量補充其現有解決方案的選項。

Azure 點對點解決方案基於雲，可以快速調配，以滿足使用者在家工作日益增長的需求。 當不再需要增加的容量時，它可以輕鬆、快速地擴展並關閉。

## <a name="about-point-to-site-vpn"></a>關於點對站 VPN

點對站 (P2S) VPN 閘道連線可讓您建立從個別用戶端電腦到您的虛擬網路的安全連線。 P2S 連線的建立方式是從用戶端電腦開始。 此解決方案對於希望從遠端位置（如從家庭或會議）連接到 Azure VNet 或本地資料中心的遠端辦公人員非常有用。 本文介紹如何使使用者能夠根據各種方案遠端工作。

下表顯示了用戶端作業系統及其可用的身份驗證選項。 根據已在使用的用戶端作業系統選擇身份驗證方法會很有説明。 例如，如果您有需要連接的用戶端作業系統的混合，請選擇具有基於證書的身份驗證的 OpenVPN。 此外，請注意，點對網站 VPN 僅在基於路由的 VPN 閘道上受支援。

![點對點](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>方案 1 - 使用者僅需要訪問 Azure 中的資源

在這種情況下，遠端使用者只需要訪問 Azure 中的資源。

![點對點](./media/working-remotely-support/scenario1.png "斯卡納裡奧 1")

在高級別上，需要以下步驟才能使使用者能夠安全地連接到 Azure 資源：

1. 創建虛擬網路閘道（如果不存在）
2. 在閘道上配置點對點 VPN
    3. [對於證書身份驗證，請按照此連結](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw)操作 。
    2.  [對於 OpenVPN，請按照此連結](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn)操作 。
    3.  [對於 Azure AD 身份驗證，請按照此連結](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant)操作。
    4.  [要對點對點連接進行故障排除，請按照此連結進行](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)。
3. 下載並分發 VPN 用戶端配置
4. 將證書（如果選擇證書身份驗證）分發到用戶端
5. 連接到 Azure VPN

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>方案 2 - 使用者需要訪問 Azure 和/或預置資源中的資源

在這種情況下，遠端使用者需要訪問 Azure 和內部本地資料中心中的資源。

![點對點](./media/working-remotely-support/scenario2.png "案例 2")

在高級別上，需要以下步驟才能使使用者能夠安全地連接到 Azure 資源：

1. 創建虛擬網路閘道（如果不存在）
2. 在閘道上配置點對點 VPN（請參閱上述方案 1）
3. 在啟用了 BGP 的 Azure 虛擬網路閘道上配置網站到網站隧道
4. 配置本地設備以連接到 Azure 虛擬網路閘道
5. 從 Azure 門戶下載點對點設定檔並分發給用戶端

[請按照此連結瞭解如何佈建網站到網站 VPN 隧道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>原生 Azure 憑證驗證的常見問題集

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 驗證的常見問題集

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>後續步驟

* [配置 P2S 連接 - Azure AD 身份驗證](openvpn-azure-ad-tenant.md)

* [設定 P2S 連線 - RADIUS 驗證](point-to-site-how-to-radius-ps.md)

* [設定 P2S 連線 - Azure 原生憑證驗證](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN"是OpenVPN公司的商標。**