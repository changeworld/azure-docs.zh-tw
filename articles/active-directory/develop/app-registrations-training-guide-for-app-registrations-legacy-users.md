---
title: 新版 Azure 入口網站應用程式註冊訓練指南
description: 介紹新的 Azure 門戶應用註冊體驗
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154571"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>新版 Azure 入口網站應用程式註冊訓練指南

您可以在 Azure 門戶中找到新[應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗中的許多改進。 如果您熟悉 Azure 門戶中的應用註冊（舊版）體驗，請使用本培訓指南開始使用新體驗。

在 Azure 活動目錄中，此處介紹的新應用程式註冊體驗通常可用 （GA）。 在 Azure 活動目錄 B2C（Azure AD B2C）中，此體驗處於預覽狀態。

## <a name="key-changes"></a>主要更改

- 應用註冊不僅限於*Web 應用/API*或*本機*應用。 您可以通過註冊相應的重定向 URI，對所有這些應用使用相同的應用註冊。

- 舊版體驗支援僅使用組織 （Azure AD） 帳戶登錄的應用。 應用已註冊為單租戶。 應用僅支援應用註冊目錄中的組織帳戶。 可以修改應用為多租戶，並支援所有組織帳戶。 新的體驗允許您註冊可以同時支援這些選項以及第三個選項的應用：所有組織帳戶以及個人 Microsoft 帳戶。

- 僅當使用組織帳戶登錄到 Azure 門戶時，舊體驗才可用。 借助新體驗，您可以使用與目錄無關的個人 Microsoft 帳戶。

## <a name="list-of-applications"></a>應用程式清單

新的應用清單顯示通過 Azure 門戶中的舊應用註冊體驗註冊的應用程式。 這些應用使用 Azure AD 帳戶登錄。 新的應用清單還顯示通過應用程式註冊門戶註冊的應用。 這些應用使用 Azure AD 和個人 Microsoft 帳戶登錄。

>[!NOTE]
>已棄用應用程式註冊門戶。

新的應用清單沒有**應用程式類型**列，因為單個應用註冊可以是幾種類型。 該清單有兩個附加列：**在 上創建**和**證書&機密**。 **證書&機密**顯示已在應用上註冊的憑據的狀態。 狀態包括 **"當前**"、**即將過期**和**過期**。

## <a name="new-app-registration"></a>新增應用程式註冊

在舊體驗中，要註冊需要提供的應用：**名稱**、**應用程式類型**和**登錄 URL/重定向 URI**。 創建的應用僅是 Azure AD 單租戶應用程式。 他們只支援應用註冊的目錄中的組織帳戶。

在新體驗中，必須為應用提供**名稱**，並選擇 **"支援帳戶類型**"。 您可以選擇提供**重定向 URI。** 如果提供重定向 URI，則需要指定它是否是 Web/公共（移動和桌面）。 有關詳細資訊，請參閱[快速入門：使用 Microsoft 標識平臺註冊應用程式](quickstart-register-app.md)。 有關 Azure AD B2C，請參閱[在 Azure 活動目錄 B2C 中註冊應用程式](../../active-directory-b2c/tutorial-register-applications.md)。

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>應用程式註冊門戶和應用註冊頁面之間的差異

### <a name="the-legacy-properties-page"></a>舊屬性頁

舊體驗具有 **"屬性"** 頁。 **屬性**具有以下欄位：

- **名稱**
- **物件識別碼**
- **應用程式 ID**
- **應用程式識別碼 URI**
- **標誌**
- **首頁 URL**
- **登出 URL**
- **服務條款 URL**
- **隱私權聲明 URL**
- **應用程式類型**
- **多租戶**

新體驗沒有該頁面。 您可以在其中找到等效功能：

- **名稱**，**徽標**，**主頁 URL**，**服務條款 URL**和**隱私權聲明 URL**現在位於應用的品牌頁面上。 **Branding**
- **物件識別碼**和**應用程式（用戶端）ID**位於 **"概述"** 頁上。
- 舊體驗中**由多租戶**切換控制的功能已被**身份驗證**頁面上**的支援帳戶類型**替換。 有關詳細資訊，請參閱[快速入門：修改應用程式支援的帳戶](quickstart-modify-supported-accounts.md)。
- **登出 URL**現在位於**身份驗證**頁上。
- **應用程式類型**不再是有效的欄位。 相反，重定向可在 **"身份驗證"** 頁上找到的 URI 確定支援哪些應用類型。
- **應用程式 ID URI**現在稱為**應用程式 ID URI，** 您可以在**公開 API**上找到它。 在舊體驗中，此屬性使用以下格式自動註冊： `https://{tenantdomain}/{appID}`， 例如 ， `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. 在新的體驗中，它自動生成為`api://{appID}`，但需要顯式保存。 在 Azure AD B2C`https://{tenantdomain}/{appID}`租戶中，仍使用該格式。

### <a name="reply-urlsredirect-urls"></a>回復 URL/重定向 URL

在舊版體驗中，應用具有 **"回復 URL"** 頁。 在新體驗中，可以在應用的**身份驗證**頁面上找到回復 URL。 它們現在稱為**重定向 URI。**

重定向 URI 的格式已更改。 它們需要與應用類型（Web 或公共）相關聯。 出於安全原因，不支援通配`http://`符和方案，除非*http://localhost*.

### <a name="keyscertificates--secrets"></a>金鑰/證書&機密

在舊版體驗中，應用具有 **"金鑰"** 頁。 在新的體驗中，它已被重命名為**證書&機密**。

**公開金鑰**現在稱為**證書**。 **密碼**現在稱為**用戶端機密**。

### <a name="required-permissionsapi-permissions"></a>所需的許可權/API 許可權

在舊版體驗中，應用具有 **"必需"許可權**頁。 在新的體驗中，它已被重命名為 API**許可權**。

在傳統體驗中選擇 API 時，可以從 Microsoft API 的小清單中選擇。 您還可以搜索租戶中的服務主體。 在新的體驗中，您可以從多個選項卡中進行選擇：**微軟 API、****我的組織使用的 API**或**我的 API**。 組織使用選項卡搜索租戶中的服務主體的**API**上的搜索欄。

> [!NOTE]
> 如果應用程式未與租戶關聯，則看不到此選項卡。 有關如何請求許可權的詳細資訊，請參閱[快速入門：配置用戶端應用程式以訪問 Web API](quickstart-configure-app-access-web-apis.md)。

舊體驗在 **"請求的許可權"** 頁的頂部有一個 **"授予許可權**"按鈕。 在新體驗中，"**授予同意"** 頁在應用的**API 許可權**部分上包含 **"授予管理員同意**"按鈕。 按鈕的功能方式也存在一些差異。

在舊版體驗中，邏輯因登錄使用者和請求的許可權而異。 其邏輯是：

- 如果僅請求使用者同意許可權，並且登錄使用者不是管理員，則使用者可以授予使用者對請求的許可權的同意。
- 如果請求了至少一個需要管理員同意的許可權，並且登錄使用者不是管理員，則使用者在嘗試授予同意時出錯。
- 如果登錄使用者是管理員，則對所有請求的許可權授予管理員同意。

在新的體驗中，只有管理員才能授予同意。 當管理員選擇**授予管理員同意**時，將授予所有請求的版權管理員同意。

## <a name="deleting-an-app-registration"></a>刪除應用註冊

在舊版體驗中，您只能刪除單租戶應用。 多租戶應用的刪除按鈕已禁用。 在新體驗中，您可以刪除任何狀態的應用，但必須確認該操作。 有關詳細資訊，請參閱[快速入門：刪除在 Microsoft 標識平臺註冊的應用程式](quickstart-remove-app.md)。

## <a name="application-manifest"></a>應用程式資訊清單

舊版和新體驗在清單編輯器中使用不同版本的 JSON 格式。 有關詳細資訊，請參閱[Azure 活動目錄應用清單](reference-app-manifest.md)。

## <a name="new-ui"></a>新 UI

新體驗為以下屬性添加了 UI 控制項：

- **身份驗證**頁具有**隱式授予流**`oauth2AllowImplicitFlow`（）。 與舊體驗不同，您可以啟用**Access 權杖**或**ID 權杖**，或者同時啟用這兩種權杖。
- **"公開 API"** 頁包含**由此 API** `oauth2Permissions`（ ） 和**授權用戶端應用程式**（ ） 定義的作用域 。`preAuthorizedApplications` 有關如何將應用配置為 Web API 並公開許可權/作用域的詳細資訊，請參閱[快速入門：將應用程式佈建為公開 Web API。](quickstart-configure-app-expose-web-apis.md)
- **"品牌"** 頁包含**發行者域**。 發行者域在[應用程式的同意提示](application-consent-experience.md)符上向使用者顯示。 有關詳細資訊，請參閱[如何：配置應用程式的發行者域](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新體驗具有以下限制：

- 用戶端機密（應用密碼）的格式不同于舊體驗的格式，可能會中斷 CLI。
- UI 不支援更改受支援帳戶的值。 您需要使用應用清單，除非您在 Azure AD 單租戶和多租戶之間切換。
