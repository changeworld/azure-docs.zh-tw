---
title: 註冊 Microsoft Graph 應用程式
titleSuffix: Azure AD B2C
description: 藉由註冊已授與必要圖形 API 許可權的應用程式，準備使用 Microsoft Graph 來管理 Azure AD B2C 資源。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67870a458138101f3b8a009f7c96c74991396284
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675181"
---
# <a name="register-a-microsoft-graph-application"></a>註冊 Microsoft Graph 應用程式

[Microsoft Graph][ms-graph] 可讓您管理 Azure AD B2C 租使用者內的許多資源，包括客戶使用者帳戶和自訂原則。 藉由撰寫呼叫 [MICROSOFT GRAPH API][ms-graph-api]的腳本或應用程式，您可以自動化租使用者管理工作，例如：

* 將現有的使用者存放區遷移至 Azure AD B2C 租使用者
* 在 Azure DevOps 中使用 Azure 管線部署自訂原則，以及管理自訂原則金鑰
* 在您自己的頁面上裝載使用者註冊，並在幕後的 Azure AD B2C 目錄中建立使用者帳戶
* 自動註冊應用程式
* 取得審核記錄

下列各節可協助您準備使用 Microsoft Graph API，將 Azure AD B2C 目錄中的資源管理自動化。

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API 互動模式

當您使用 Microsoft Graph API 來管理 Azure AD B2C 租使用者中的資源時，可以使用兩種通訊模式：

* **互動式** -適用于執行一次的工作，您可以使用 B2C 租使用者中的系統管理員帳戶來執行管理工作。 此模式需要系統管理員先使用其認證登入，才能呼叫 Microsoft Graph API。

* **自動化** -針對已排程或連續執行的工作，這個方法會使用您以執行管理工作所需的許可權設定的服務帳戶。 您可以藉由註冊應用程式，讓應用程式和腳本使用其 *應用程式， (用戶端) 識別碼* 和 **OAuth 2.0 用戶端認證** 授與來驗證應用程式，以在 Azure AD B2C 中建立「服務帳戶」。 在此情況下，應用程式本身就像是呼叫 Microsoft Graph API，而非先前所述的互動式方法中的系統管理員使用者。

您可以建立下列各節所示的應用程式註冊，以啟用 **自動化** 互動案例。

雖然 Azure AD B2C authentication 服務目前不直接支援 OAuth 2.0 用戶端認證授與流程，但是您可以在 Azure AD B2C 租使用者中，使用 Azure AD 和應用程式的 Microsoft 身分識別平臺/token 端點來設定用戶端認證流程。 Azure AD B2C 租用戶會與 Azure AD 企業租用戶共用某些功能。

## <a name="register-management-application"></a>註冊管理應用程式

您必須先在 Azure AD B2C 租使用者中建立應用程式註冊，以授與所需的 API 許可權，您的腳本和應用程式才能與 [MICROSOFT GRAPH API][ms-graph-api] 互動以管理 Azure AD B2C 資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如， *managementapp1*。
1. 選取 [僅此組織目錄中的帳戶]。
1. 在 [ **許可權**] 底下，清除 [將 *管理員同意授與 openid 和 offline_access 許可權* ] 核取方塊。
1. 選取 [註冊]。
1. 記錄應用程式 **(用戶端) 識別碼** 出現在應用程式總覽頁面上。 您會在稍後的步驟中使用此值。

### <a name="grant-api-access"></a>授與 API 存取權限

接下來，授與已註冊的應用程式許可權，以透過呼叫 Microsoft Graph API 來操作租使用者資源。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>建立用戶端密碼

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

您現在有一個應用程式，具有在 Azure AD B2C 租使用者中 *建立*、 *讀取*、 *更新* 及 *刪除* 使用者的許可權。 繼續前往下一節，以新增 *密碼更新* 許可權。

## <a name="enable-user-delete-and-password-update"></a>啟用使用者刪除和密碼更新

*讀取和寫入目錄資料* 許可權 **不包括刪除** 使用者或更新使用者帳戶密碼的能力。

如果您的應用程式或腳本需要刪除使用者或更新其密碼，請將 *使用者系統管理員* 角色指派給您的應用程式：

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後使用 [ **目錄 + 訂** 用帳戶] 篩選器切換至您的 Azure AD B2C 租使用者。
1. 搜尋並選取 [Azure AD B2C]。
1. 在 [ **管理**] 底下，選取 [ **角色和系統管理員**]。
1. 選取 [ **使用者管理員** ] 角色。
1. 選取 [ **新增指派**]。
1. 在 [ **選取** ] 文字方塊中，輸入您稍早註冊的應用程式名稱，例如 *managementapp1*。 當您的應用程式出現在搜尋結果中時，請加以選取。
1. 選取 [新增]  。 可能需要幾分鐘的時間才能完全傳播許可權。

## <a name="next-steps"></a>下一步

既然您已註冊管理應用程式，並已授與它必要的許可權，您的應用程式和服務 (例如 Azure Pipelines) 可以使用其認證和許可權來與 Microsoft Graph API 互動。 

* [從 Azure AD 取得存取權杖](/graph/auth-v2-service#4-get-an-access-token)
* [使用存取權杖來呼叫 Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Microsoft Graph 支援的 B2C 作業](microsoft-graph-operations.md)
* [使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶](microsoft-graph-operations.md)
* [使用 Azure AD 報告 API 取得審核記錄](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
