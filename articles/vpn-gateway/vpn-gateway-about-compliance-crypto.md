---
title: Azure VPN 閘道：加密要求
description: 本文說明密碼編譯需求與 Azure VPN 閘道。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902831"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>關於密碼編譯需求和 Azure VPN 閘道

此文章說明如何針對 Azure 內的跨單位 S2S VPN 通道和 VNet 對 VNet 連線，設定 Azure VPN 閘道，以滿足您的密碼編譯需求。

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>關於用於 Azure VPN 連接的 IKEv1 和 IKEv2

傳統上，我們只允許用於基本 SKU 的 IKEv1 連接，並允許所有 VPN 閘道 SKU（基本 SKU）以外的 IKEv2 連接。 基本 SKU 只允許 1 個連接，除了其他限制（如性能）外，僅支援 IKEv1 協定的舊設備的客戶經驗有限。 為了增強使用 IKEv1 協定的客戶體驗，我們現在允許所有 VPN 閘道 SKU 的 IKEv1 連接，但基本 SKU 除外。 有關詳細資訊，請參閱[VPN 閘道 SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)。

![Azure VPN 閘道 IKEv1 和 IKEv2 連接](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

當 IKEv1 和 IKEv2 連接應用於同一 VPN 閘道時，這兩個連接之間的傳輸將自動啟用。

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>關於 Azure VPN 閘道的 IPsec 和 IKE 原則參數

IPsec 和 IKE 通訊協定標準支援各種不同的密碼編譯演算法的各種組合。 如果不請求加密演算法和參數的特定組合，Azure VPN 閘道將使用一組預設建議。 選擇預設原則集的用意，是要在預設設定中將與各種協力廠商 VPN 裝置的互通性最大化。 如此一來，原則和提案數目就無法涵蓋所有可用的密碼編譯演算法和金鑰長度組合。

本文列出了 Azure VPN 閘道的預設策略集：[關於 VPN 設備和網站到網站 VPN 閘道連接的 IPsec/IKE 參數](vpn-gateway-about-vpn-devices.md)。

## <a name="cryptographic-requirements"></a>密碼編譯需求

對於需要特定加密演算法或參數（通常是由於合規性或安全要求）的通信，現在可以將其 Azure VPN 閘道配置為使用具有特定加密演算法和關鍵優勢的自訂 IPsec/IKE 策略，而不是 Azure 預設策略集。

例如，Azure VPN 閘道的 IKEv2 主模式策略僅使用 Diffie-Hellman 組 2（1024 位），而您可能需要指定更強大的組才能在 IKE 中使用，例如組 14 （2048 位）、組 24（2048 位 MODP 組）或 ECP（橢圓曲線）組） 256 位或 384 位（組 19 和組 20）。 類似的需求亦適用於 IPsec 快速模式原則。

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>自訂的 IPsec/IKE 原則與 Azure VPN 閘道

Azure VPN 閘道現在支援個別連線的自訂 IPsec/IKE 原則。 針對站對站或 VNet 對 VNet 連線，您可以針對 IPsec 和 IKE 選擇特定的密碼編譯演算法組合，搭配所需的金鑰強度，如下列範例所示：

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

您可以建立 IPsec/IKE 原則，並將其套用至新連線或現有連線。

### <a name="workflow"></a>工作流程

1. 按照其他使用說明文件所述，依據連線的拓撲來建立虛擬網路、VPN 閘道或區域網路閘道。
2. 建立 IPsec/IKE 原則
3. 您可以在建立 S2S 或 VNet 對 VNet 連線時套用原則。
4. 如果已經建立連線，您可以套用原則，或將其更新為現有連線。

## <a name="ipsecike-policy-faq"></a>IPsec/IKE 原則常見問題集

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>後續步驟

如需為連線設定自訂 IPsec/IKE 原則的逐步指示，請參閱[設定 IPsec/IKE 原則](vpn-gateway-ipsecikepolicy-rm-powershell.md)。

另請參閱[連線多個原則型 VPN 裝置](vpn-gateway-connect-multiple-policybased-rm-ps.md)，以深入了解 UsePolicyBasedTrafficSelectors 選項。
