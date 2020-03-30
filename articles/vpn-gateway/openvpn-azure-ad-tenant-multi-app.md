---
title: VPN 閘道：適用于不同使用者組的 Azure AD 租戶：Azure AD 身份驗證
description: 您可以使用 P2S VPN 使用 Azure AD 身份驗證連接到 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485588"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>為 P2S OpenVPN 協定連接創建 Azure 活動目錄租戶

連接到 VNet 時，可以使用基於證書的身份驗證或 RADIUS 身份驗證。 但是，當您使用 Open VPN 協定時，也可以使用 Azure 活動目錄身份驗證。 如果希望不同的使用者集能夠連接到不同的 VPN 閘道，則可以在 AD 中註冊多個應用，並將它們連結到不同的 VPN 閘道。 本文可説明您為 P2S OpenVPN 身份驗證設置 Azure AD 租戶，並在 Azure AD 中創建和註冊多個應用，以允許不同使用者和組進行不同的訪問。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. 在閘道上啟用身份驗證

在此步驟中，您將在 VPN 閘道上啟用 Azure AD 身份驗證。

1. 通過運行以下命令在 VPN 閘道上啟用 Azure AD 身份驗證。 請務必修改命令以反映您自己的環境：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > 請勿在上面的命令中使用 Azure VPN 用戶端的應用程式 ID：它將授予所有使用者對 VPN 閘道的存取權限。 使用您註冊的應用程式的 ID。

2. 通過運行以下命令創建和下載設定檔。 更改 -ResourcGroup 名稱和 -Name 值以匹配您自己的值。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. 運行命令後，您將看到類似于下面的結果。 將結果 URL 複製到瀏覽器以下載設定檔 ZIP 檔案。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. 提取下載的 ZIP 檔案。

5. 流覽到解壓縮的"AzureVPN"資料夾。

6. 記下"azurevpnconfig.xml"檔的位置。 azurevpnconfig.xml 包含 VPN 連接的設置，可以直接導入到 Azure VPN 用戶端應用程式中。 您還可以將此檔分發給需要通過電子郵件或其他方式連接的所有使用者。 使用者將需要有效的 Azure AD 憑據才能成功連接。

## <a name="next-steps"></a>後續步驟

要連接到虛擬網路，必須創建和配置 VPN 用戶端設定檔。 請參閱[為 P2S VPN 連接配置 VPN 用戶端](openvpn-azure-ad-client.md)。
