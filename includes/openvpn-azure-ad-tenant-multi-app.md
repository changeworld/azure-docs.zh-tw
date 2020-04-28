---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485584"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. 建立 Azure AD 租使用者

使用[建立新的租](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)使用者文章中的步驟，建立 Azure AD 租使用者：

* 組織名稱
* 初始網域名稱

  範例：

   ![新增 Azure AD 租使用者](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. 建立租使用者使用者

在此步驟中，您會建立兩個 Azure AD 租使用者使用者：一個全域系統管理員帳戶和一個主要使用者帳戶。 主要使用者帳戶會當做您的主要內嵌帳戶（服務帳戶）使用。 當您建立 Azure AD 租使用者使用者帳戶時，您會針對您想要建立的使用者類型調整目錄角色。 使用[本文](../articles/active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟，為您的 Azure AD 租使用者建立至少兩個使用者。 請務必變更**目錄角色**以建立帳戶類型：

* 全域管理員
* User

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. 註冊 VPN 用戶端

在 Azure AD 租使用者中註冊 VPN 用戶端。

1. 找出您要用於驗證之目錄的目錄識別碼。 它會列在 [Active Directory] 頁面的 [屬性] 區段中。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 複製 [目錄識別碼]。

3. 以指派**全域管理員**角色的使用者身分登入 Azure 入口網站。

4. 接下來，請授與系統管理員同意。 在瀏覽器的網址列中，複製並貼上您部署位置的相關 URL：

    公用

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 若出現提示，請選取**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 出現提示時，選取 [**接受**]。

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. 在您的 Azure AD 下的 [**企業應用程式**] 中，您會看到列出**Azure VPN** 。

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. 註冊其他應用程式

在此步驟中，您會針對各種使用者和群組註冊其他應用程式。

1. 在您的 Azure Active Directory 底下，依序按一下 [**應用程式註冊**] 和 [ **+ 新增註冊**]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. 在 [**註冊應用程式**] 頁面上，輸入**名稱**。 選取所需的**支援帳戶類型**，然後按一下 [**註冊**]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 註冊新的應用程式後，按一下 [應用程式] 分頁底下的 [**公開 API** ]。

4. 按一下 [ **+ 新增領域**]。

5. 保留預設的 [**應用程式識別碼 URI**]。 按一下 [儲存並繼續]****。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 填寫必要的欄位，並確定**狀態**為 [**已啟用**]。 按一下 [**新增領域**]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. 按一下 [**公開 API** ]，然後按 [**新增用戶端應用程式**]。  針對 [**用戶端識別碼**]，輸入下列值（視雲端而定）：

    - 輸入**41b23e61-6c1e-4545-b367-cd054e0ed4b4** （適用于 Azure**公用**）
    - 針對 Azure**政府**機構輸入**51bb15d4-3a4f-4ebf-9dca-40096fe32426**
    - 針對 Azure**德國**輸入**538ee9e6-310a-468d-afef-ea97365856a9**
    - 輸入適用于 Azure**中國世紀**的**49f817b6-84ae-4cc0-928c-73f27289b3aa**

8. 按一下 [**新增應用程式**]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. 從 [**總覽**] 頁面複製 [**應用程式（用戶端）識別碼**]。 您將需要此資訊來設定您的 VPN 閘道。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 重複 [[註冊其他應用程式](#register-apps)] 區段中的步驟，建立您的安全性需求所需的多個應用程式。 每個應用程式都會與 VPN 閘道相關聯，而且可以有一組不同的使用者。 只有一個應用程式可以與閘道建立關聯。

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. 將使用者指派給應用程式

將使用者指派給您的應用程式。

1. 在 [ **Azure AD > 企業應用程式**] 下，選取新註冊的應用程式，然後按一下 [**屬性**]。 確定 [**需要使用者指派嗎？** ] 設定為 **[是]**。 按一下 [檔案]  。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 在 [應用程式] 頁面上，按一下 [**使用者和群組**]，然後按一下 [ **+ 新增使用者**]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. 在 [**新增指派**] 底下，按一下 [**使用者和群組**]。 選取您想要能夠存取此 VPN 應用程式的使用者。 按一下 [選取]。 

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)