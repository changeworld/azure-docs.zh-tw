---
title: VPN 閘道：用於 P2S VPN 連接的 Azure AD 租戶：Azure AD 身份驗證
description: 您可以使用 P2S VPN 使用 Azure AD 身份驗證連接到 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: f4092f651a3058c8a2e738c81d9db7e296386bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402887"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>為 P2S OpenVPN 協定連接創建 Azure 活動目錄租戶

連接到 VNet 時，可以使用基於證書的身份驗證或 RADIUS 身份驗證。 但是，當您使用 Open VPN 協定時，也可以使用 Azure 活動目錄身份驗證。 本文可説明您為 P2S 開放 VPN 身份驗證設置 Azure AD 租戶。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. 驗證 Azure AD 租戶

驗證您是否具有 Azure AD 租戶。 如果沒有 Azure AD 租戶，則可以使用["創建新租戶](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)"文章中的步驟創建一個租戶：

* 組織名稱
* 初始網域名稱

範例：

   ![新的 Azure AD 租戶](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. 創建 Azure AD 租戶使用者

Azure AD 租戶需要以下帳戶：全域管理員帳戶和主使用者帳戶。 主使用者帳戶用作主嵌入帳戶（服務帳戶）。 創建 Azure AD 租戶使用者帳戶時，將調整要創建的使用者類型的"目錄"角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟為 Azure AD 租戶創建至少兩個使用者。 請務必更改**目錄角色**以創建帳戶類型：

* 全域管理員
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. 在 VPN 閘道上啟用 Azure AD 身份驗證

1. 找到要用於身份驗證的目錄的目錄 ID。 它列在"活動目錄"頁的屬性部分中。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 複製 [目錄識別碼]。

3. 以分配**全域管理員**角色的使用者身份登錄到 Azure 門戶。

4. 接下來，給予管理員同意。 複製並粘貼與您的部署位置相關的 URL 在瀏覽器的網址列中：

    公開

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 如果出現提示，請選擇**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 選擇"在提示時**接受**"。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. 在 Azure AD 下，在**企業應用程式中**，您將看到列出的**Azure VPN。**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. 如果您還沒有正常運作的點對站台環境，請依照指示來建立一個。 請參閱[建立點對站 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，來建立及設及使用原生 Azure 憑證驗證的點對站 VPN 閘道。 

    > [!IMPORTANT]
    > OpenVPN 不支援基本 SKU。

9. 通過運行以下命令在 VPN 閘道上啟用 Azure AD 身份驗證，確保修改命令以反映您自己的環境：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > 請確保在`AadIssuerUri`值的末尾包含尾隨斜杠。 否則，該命令將失敗。

10. 通過運行以下命令創建和下載設定檔。 更改 -資源組名稱和 -Name 值以匹配您自己的值。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. 運行命令後，您將看到類似于下面的結果。 將結果 URL 複製到瀏覽器以下載設定檔 ZIP 檔案。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. 提取下載的 ZIP 檔案。

13. 流覽到解壓縮的"AzureVPN"資料夾。

14. 記下"azurevpnconfig.xml"檔的位置。 azurevpnconfig.xml 包含 VPN 連接的設置，可以直接導入到 Azure VPN 用戶端應用程式中。 您還可以將此檔分發給需要通過電子郵件或其他方式連接的所有使用者。 使用者將需要有效的 Azure AD 憑據才能成功連接。

## <a name="next-steps"></a>後續步驟

要連接到虛擬網路，必須創建和配置 VPN 用戶端設定檔。 請參閱[為 P2S VPN 連接配置 VPN 用戶端](openvpn-azure-ad-client.md)。
