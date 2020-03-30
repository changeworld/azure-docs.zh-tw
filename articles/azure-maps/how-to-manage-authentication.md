---
title: 管理身份驗證 |微軟 Azure 地圖
description: 使用 Azure 門戶在 Microsoft Azure 映射中管理身份驗證。
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335534"
---
# <a name="manage-authentication-in-azure-maps"></a>管理 Azure 地圖服務中的驗證

創建 Azure 地圖帳戶後，將創建用戶端 ID 和金鑰以支援 Azure 活動目錄 （Azure AD） 身份驗證和共用金鑰身份驗證。

## <a name="view-authentication-details"></a>檢視驗證詳細資料

創建 Azure 地圖帳戶後，將生成主鍵和輔助鍵。 我們建議您[在使用共用金鑰身份驗證調用 Azure 映射](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)時使用主金鑰作為訂閱金鑰。 您可以在滾動鍵更改等方案中使用輔助鍵。 有關詳細資訊，請參閱[Azure 映射中的身份驗證](https://aka.ms/amauth)。

您可以在 Azure 門戶中查看身份驗證詳細資訊。 在您的帳戶中，在 **"設置"** 功能表上，選擇 **"身份驗證**"。

![驗證詳細資料](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>註冊和配置 Azure AD 應用

1. 在 Azure 門戶中，在 Azure 服務清單中，選擇**Azure 活動目錄** > **應用註冊** > **"新註冊**"。  

    ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

1. 如果您已註冊應用，請繼續執行下一步。 如果您尚未註冊應用，請輸入**名稱**，選擇**支援帳戶類型**，然後選擇"**註冊**"。  

    ![應用註冊詳細資訊](./media/how-to-manage-authentication/app-create.png)

1. 要將委派的 API 許可權分配給 Azure 映射，請轉到應用程式。 然後在**應用註冊**下，選擇**API 許可權** > **添加許可權**。 在我的**組織使用的 API**下，搜索並選擇**Azure 映射**。

    ![添加應用 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

1. 選擇 **"訪問 Azure 地圖**"旁邊的核取方塊，然後選擇 **"添加許可權**"。

    ![選擇應用 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

1. 完成以下步驟之一，具體取決於身份驗證方法。 

    * 如果應用程式使用 Azure 映射 Web SDK 的使用者權杖身份驗證，則`oauth2AllowImplicitFlow`啟用 。 要啟用它，在應用註冊的 **"清單"** 部分中，設置為`oauth2AllowImplicitFlow`true。 
    
       ![應用程式資訊清單](./media/how-to-manage-authentication/app-manifest.png)

    * 如果應用程式使用伺服器或應用程式身份驗證，則在應用註冊頁上轉到**證書&機密**。 然後上傳公開金鑰證書或通過選擇 **"新用戶端機密**"創建密碼。 
    
       ![建立用戶端密碼](./media/how-to-manage-authentication/app-keys.png)

        如果創建密碼，則在選擇 **"添加**"後，複製密碼並將其安全地存儲。 您將使用此密碼從 Azure AD 獲取權杖。

       ![添加用戶端機密](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>向 Azure 映射授予基於角色的存取控制

將 Azure 地圖帳戶與 Azure AD 租戶關聯後，可以授予存取控制。 通過將使用者、組或應用程式分配給一個或多個 Azure 映射存取控制角色，可以授予*基於角色的存取控制*（RBAC）。 

1. 轉到**Azure 地圖帳戶**。 選擇**存取控制 （IAM）** > **角色指派**。

    ![授與 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. 在 **"角色指派**"選項卡上，在 **"角色**"下，選擇**Azure 地圖日期讀取器（預覽）。** 在 [存取權指派對象為]**** 底下，選取 [Azure AD 使用者、群組或服務主體]****。 選擇使用者或應用程式。 然後選擇 **"保存**"。

    ![新增角色指派](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>檢視可用的 Azure 地圖服務 RBAC 角色

要查看可用於 Azure 映射的 RBAC 角色，請轉到**訪問控制項 （IAM）。** 選擇**角色**，然後搜索以*Azure 映射*開頭的角色。 這些 Azure 映射角色是您可以授予存取權限的角色。

![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>查看 Azure 地圖 RBAC

RBAC 提供精細的存取控制。

要查看已授予 Azure 地圖 RBAC 的使用者和應用，請轉到**存取控制 （IAM）。** 在此處，選擇**角色指派**，然後按**Azure 映射**進行篩選。

![查看已被授予 RBAC 的使用者和應用](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>要求 Azure 地圖服務的權杖

註冊應用並將其與 Azure 映射關聯後，可以請求訪問權杖。

如果應用程式使用 Azure 映射 Web SDK 的使用者權杖身份驗證，則使用 Azure 映射用戶端 ID 和 Azure AD 應用 ID 配置 HTML 頁。

如果應用程式使用伺服器或應用程式身份驗證，則從 Azure AD 權杖終結點`https://login.microsoftonline.com`請求權杖。 在您的請求中，請使用以下詳細資訊： 

* Azure AD 資源識別碼`https://atlas.microsoft.com/`
* Azure 映射用戶端 ID
* Azure AD 應用程式識別碼
* Azure AD 應用註冊密碼或證書

| Azure 環境   | Azure AD 權杖終結點 | Azure 資源識別碼 |
| --------------------|-------------------------|-------------------|
| Azure 公共雲        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure 政府雲   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

有關向使用者和服務主體請求 Azure AD 的訪問權杖的詳細資訊，請參閱 Azure [AD 的身份驗證方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱[Azure AD 和 Azure 映射 Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

查找 Azure 地圖帳戶的 API 使用方式指標：
> [!div class="nextstepaction"] 
> [檢視使用計量](how-to-view-api-usage.md)

流覽演示如何將 Azure AD 與 Azure 映射集成的示例：

> [!div class="nextstepaction"]
> [Azure AD 身份驗證示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
