---
title: VPN 閘道：不同使用者群組的 Azure AD 租使用者： Azure AD 驗證
description: 您可以使用 P2S VPN，使用 Azure AD authentication 來連線到您的 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: alzam
ms.openlocfilehash: 0ef0c7d3a269753067e53a69b9da680db969e25d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414416"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>建立 Azure Active Directory 租用戶以進行 P2S OpenVPN 通訊協定連線

連線到您的 VNet 時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 不過，當您使用 Open VPN 通訊協定時，您也可以使用 Azure Active Directory 驗證。 如果您想要讓不同的使用者集合能夠連線到不同的 VPN 閘道，您可以在 AD 中註冊多個應用程式，並將它們連結至不同的 VPN 閘道。 本文可協助您設定 P2S OpenVPN authentication 的 Azure AD 租使用者，並在 Azure AD 中建立並註冊多個應用程式，以允許不同的使用者和群組進行不同的存取。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. 在閘道上啟用驗證

在此步驟中，您將在 VPN 閘道上啟用 Azure AD 驗證。

1. 流覽至 [**點對站**設定]，然後選取 [ **OpenVPN （SSL）** ] 作為 [通道**類型**]，以在 VPN 閘道上啟用 Azure AD 驗證。 選取 [ **Azure Active Directory** ] 作為 [**驗證類型**]，然後填入 [ **Azure Active Directory** ] 區段下的資訊。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > 請勿使用 Azure VPN 用戶端的應用程式識別碼：它會將 VPN 閘道的存取權授與所有使用者。 使用您所註冊之應用程式的識別碼。

2. 按一下 [**下載 VPN 用戶端**] 連結，以建立並下載設定檔。

3. 將下載的 zip 檔案解壓縮。

4. 流覽至解壓縮的 "AzureVPN" 資料夾。

5. 記下「azurevpnconfig.xml」檔案的位置。 azurevpnconfig.xml 包含 VPN 連線的設定，而且可以直接匯入至 Azure VPN 用戶端應用程式。 您也可以將此檔案散發給所有需要透過電子郵件或其他方式連接的使用者。 使用者需要有效的 Azure AD 認證，才能成功連接。

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
