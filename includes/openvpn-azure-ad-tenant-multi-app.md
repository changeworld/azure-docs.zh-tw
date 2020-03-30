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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485584"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. 創建 Azure AD 租戶

使用["創建新租戶](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)"文章中的步驟創建 Azure AD 租戶：

* 組織名稱
* 初始網域名稱

  範例：

   ![新的 Azure AD 租戶](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. 創建租戶使用者

在此步驟中，您將創建兩個 Azure AD 租戶使用者：一個全域管理員帳戶和一個主使用者帳戶。 主使用者帳戶用作主嵌入帳戶（服務帳戶）。 創建 Azure AD 租戶使用者帳戶時，將調整要創建的使用者類型的"目錄"角色。 使用[本文](../articles/active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟為 Azure AD 租戶創建至少兩個使用者。 請務必更改**目錄角色**以創建帳戶類型：

* 全域管理員
* User

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. 註冊 VPN 用戶端

在 Azure AD 租戶中註冊 VPN 用戶端。

1. 找到要用於身份驗證的目錄的目錄 ID。 它列在"活動目錄"頁的屬性部分中。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 如果出現提示，請選擇**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 選擇"在提示時**接受**"。

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. 在 Azure AD 下，在**企業應用程式中**，您將看到列出的**Azure VPN。**

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. 註冊其他申請

在此步驟中，您可以為各種使用者和組註冊其他應用程式。

1. 在 Azure 活動目錄下，按一下**應用註冊**，然後**按一下 "新註冊**"。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. 在 **"註冊應用程式**"頁上，輸入**名稱**。 選擇所需的**受支援帳戶類型**，然後按一下"**註冊**"。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 註冊新應用後，按一下應用邊欄選項卡下的 **"公開 API"。**

4. 按一下 **= 添加作用域**。

5. 保留預設**的應用程式 ID URI**。 按一下 [儲存並繼續]****。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 填寫所需欄位並確保**狀態****已啟用**。 按一下"**添加範圍**"。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. 按一下 **"公開 API"** 然後 **= 添加用戶端應用程式**。  對於**用戶端 ID，** 根據雲輸入以下值：

    - 輸入**41b23e61-6c1e-4545-b367-cd054e0ed4b44**用於 Azure**公共**
    - 輸入 Azure**政府** **51bb15d4-3a4f-4ebf-9dca-40096fe32426**
    - 輸入 Azure**德國** **538ee9e6-310a-468d-afef-ea97365856a9**
    - 輸入**49f817b6-84ae-4cc0-928c-73f27289b3aa**適用于 Azure**中國 21Vianet**

8. 按一下"**添加應用程式**"。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. 從 **"概述"** 頁複製**應用程式（用戶端）ID。** 您需要此資訊來配置 VPN 閘道。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 重複此[寄存器附加應用程式](#register-apps)部分中的步驟，以創建安全要求所需的盡可能多的應用程式。 每個應用程式都將與 VPN 閘道關聯，並可以具有一組不同的使用者。 只能將一個應用程式關聯到閘道。

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. 將使用者分配給應用程式

將使用者分配給您的應用程式。

1. 在**Azure AD-> 企業應用程式中**，選擇新註冊的應用程式並按一下**屬性**。 確保**所需的使用者分配？** 設置為 **"是**"。 按一下 [儲存]****。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 在應用頁面上，按一下 **"使用者和組**"，然後按一下 **"添加使用者**"。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. 在 **"添加分配"** 下，按一下 **"使用者"和"組**"。 選擇要能夠訪問此 VPN 應用程式的使用者。 按一下 **"選擇**"。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)