---
title: VPN 閘道：用於 P2S VPN 連線的 Azure AD 租使用者： Azure AD 驗證
description: 您可以使用 P2S VPN，使用 Azure AD authentication 來連線到您的 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: alzam
ms.openlocfilehash: b9627862002a70dc84b0e268128c53a97df0ebe8
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472293"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>建立 P2S OpenVPN 通訊協定連接的 Azure Active Directory 租使用者

連線到您的 VNet 時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 不過，當您使用 Open VPN 通訊協定時，您也可以使用 Azure Active Directory 驗證。 本文可協助您設定 P2S Open VPN 驗證的 Azure AD 租使用者。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

## <a name="tenant"></a>1. 建立 Azure AD 租使用者

使用[建立新的租](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)使用者文章中的步驟，建立 Azure AD 租使用者：

* 組織名稱
* 初始功能變數名稱

範例：

   ![新增 Azure AD 租使用者](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. 建立 Azure AD 租使用者使用者

接下來，建立兩個使用者帳戶。 建立一個全域系統管理員帳戶和一個主要使用者帳戶。 主要使用者帳戶會當做您的主要內嵌帳戶（服務帳戶）使用。 當您建立 Azure AD 租使用者使用者帳戶時，您會針對您想要建立的使用者類型調整目錄角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟，為您的 Azure AD 租使用者建立至少兩個使用者。 請務必變更**目錄角色**以建立帳戶類型：

* 全域管理員
* User

## <a name="enable-authentication"></a>3. 在 VPN 閘道上啟用 Azure AD 驗證

1. 找出您要用於驗證之目錄的目錄識別碼。 它會列在 [Active Directory] 頁面的 [屬性] 區段中。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 複製 [目錄識別碼]。

3. 以指派**全域管理員**角色的使用者身分登入 Azure 入口網站。

4. 接下來，請授與系統管理員同意。 在瀏覽器的網址列中，複製並貼上您部署位置的相關 URL：

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

5. 若出現提示，請選取**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 出現提示時，選取 [**接受**]。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. 在您的 Azure AD 下的 [**企業應用程式**] 中，您會看到列出**Azure VPN** 。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. 如果您還沒有正常運作的點對站台環境，請依照指示來建立一個。 請參閱[建立點對站 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，來建立及設及使用原生 Azure 憑證驗證的點對站 VPN 閘道。 

    > [!IMPORTANT]
    > OpenVPN 不支援基本 SKU。

9. 藉由執行下列命令，在 VPN 閘道上啟用 Azure AD 驗證，請務必修改命令以反映您自己的環境：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > 請確定您在 `AadIssuerUri` 值的結尾包含尾端斜線。 否則，此命令將會失敗。

10. 執行下列命令來建立並下載設定檔。 變更-ResourceGroupName 和-Name 值，以符合您自己的值。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. 執行命令之後，您會看到類似下面的結果。 將結果 URL 複製到您的瀏覽器，以下載設定檔 zip 檔案。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. 將下載的 zip 檔案解壓縮。

13. 流覽至解壓縮的 "AzureVPN" 資料夾。

14. 記下 "azurevpnconfig" 檔案的位置。 Azurevpnconfig 包含 VPN 連線的設定，而且可以直接匯入至 Azure VPN 用戶端應用程式。 您也可以將此檔案散發給所有需要透過電子郵件或其他方式連接的使用者。 使用者需要有效的 Azure AD 認證，才能成功連接。

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
