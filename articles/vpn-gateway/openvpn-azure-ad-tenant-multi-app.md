---
title: VPN 閘道： Azure AD 不同使用者群組的租使用者： Azure AD 驗證
description: 您可以使用 P2S VPN，使用 Azure AD authentication 連接到您的 VNet
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819682"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>為 P2S OpenVPN 通訊協定連線建立 Active Directory (AD) 租使用者

連接到您的 VNet 時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 不過，當您使用開放式 VPN 通訊協定時，也可以使用 Azure Active Directory authentication。 如果您想要讓不同的使用者集合能夠連線到不同的 VPN 閘道，您可以在 AD 中註冊多個應用程式，並將其連結至不同的 VPN 閘道。 本文可協助您設定 P2S OpenVPN authentication 的 Azure AD 租使用者，並在 Azure AD 中建立並註冊多個應用程式，以允許不同的使用者和群組進行不同的存取。

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. 在閘道上啟用驗證

在此步驟中，您將在 VPN 閘道上啟用 Azure AD authentication。

1. 流覽至 **點對站** 設定，並挑選 **OpenVPN (SSL) ** 作為通道 **類型**，以在 VPN 閘道上啟用 Azure AD 驗證。 選取 [ **Azure Active Directory** ] 作為 **驗證類型** ，然後在 [ **Azure Active Directory** ] 區段下填入資訊。

    ![Azure 入口網站視圖](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > 請勿使用 Azure VPN 用戶端的應用程式識別碼：它會將 VPN 閘道的存取權授與所有使用者。 使用您註冊 () 應用程式的識別碼。

2. 按一下 [ **下載 VPN 用戶端** ] 連結，以建立並下載設定檔。

3. 將下載的 zip 檔案解壓縮。

4. 流覽至解壓縮的 "AzureVPN" 資料夾。

5. 記下 "azurevpnconfig.xml" 檔案的位置。 azurevpnconfig.xml 包含 VPN 連接的設定，而且可以直接匯入 Azure VPN Client 應用程式中。 您也可以將這個檔案散發給所有需要透過電子郵件或其他方式連接的使用者。 使用者將需要有效的 Azure AD 認證才能成功連線。

## <a name="next-steps"></a>下一步

若要連接到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱 [設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
