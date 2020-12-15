---
title: Microsoft 身分識別平臺管理員同意通訊協定
description: Microsoft 身分識別平臺端點的授權說明，包括範圍、許可權及同意。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7de97fd775853f64803ab62ac397e754d065e4df
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509320"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Microsoft 身分識別平臺上的系統管理員同意

某些許可權需要系統管理員的同意，才能在租使用者中授與這些許可權。  您也可以使用系統管理員同意端點來授與整個租使用者的許可權。

## <a name="recommended-sign-the-user-into-your-app"></a>建議：將使用者登入您的應用程式

通常，當您建置使用系統管理員同意端點的應用程式時，應用程式會需要一個可供系統管理員核准應用程式權限的頁面或檢視。 此頁面可以是應用程式註冊流程的一部分、應用程式設定的一部分，或是專用的「連接」流程。 在許多情況下，應用程式只在使用者利用工作或學校 Microsoft 帳戶登入之後顯示此「連接」檢視是很合理的。

將使用者登入應用程式時，您可以先識別系統管理員所屬的組織，然後再要求他們核准必要的權限。 雖然這並非絕對必要，但這麼做可協助您為組織使用者建立更直覺式的體驗。 若要將使用者登入，請遵循我們的 [Microsoft 身分識別平臺通訊協定教學](active-directory-v2-protocols.md)課程。

## <a name="request-the-permissions-from-a-directory-admin"></a>向目錄管理員要求權限

當您準備好向組織的系統管理員要求許可權時，您可以將使用者重新導向至 Microsoft 身分識別平臺系統 *管理員同意端點*。

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```

| 參數 | 條件 | 說明 |
| ---: | ---: | :---: |
| `tenant` | 必要 | 您想要要求權限的目錄租用戶。 可以提供 GUID 或易記的名稱格式，或是一般會參考使用 `organizations` (如範例所示)。 請勿使用「一般」，因為個人帳戶無法在租使用者的內容中提供系統管理員同意。 若要確保與管理租使用者的個人帳戶具有最佳相容性，請盡可能使用租使用者識別碼。 |
| `client_id` | 必要 | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗指派給您應用程式的 **應用程式 (用戶端) 識別碼**。 |
| `redirect_uri` | 必要 |您想要傳送回應以供應用程式處理的重新導向 URI。 它必須與您在應用程式註冊入口網站中註冊的其中一個重新導向 URI 完全相符。 |
| `state` | 建議 | 包含在要求中的值，也會在權杖回應中傳回。 它可以是您想要的任何內容的字串。 請在驗證要求出現之前，先使用此狀態在應用程式中將使用者狀態的相關資訊 (例如他們之前所在的網頁或檢視) 編碼。 |
|`scope` | 必要 | 定義應用程式所要求的許可權集合。 這可以是使用/.default) 或動態範圍的靜態 (。 這可以包括 OIDC 範圍 (`openid` 、 `profile` `email`) 。 |


此時，Azure AD 會要求租用戶系統管理員登入來完成要求。 系統會要求系統管理員核准您在參數中要求的擁有權限 `scope` 。  如果您已使用靜態 (`/.default`) 值，其運作方式就像 v1.0 系統管理員同意端點，而且會要求應用程式所需許可權中的所有範圍要求同意。

### <a name="successful-response"></a>成功的回應

如果系統管理員為您的應用程式核准權限，則成功的回應看起來會像這樣︰

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| 參數 | 說明 |
| ---: | :---: |
| `tenant`| 將應用程式所要求的權限授與應用程式的目錄租用戶 (採用 GUID 格式)。|
| `state` | 一個包含在要求中而將一併在權杖回應中傳回的值。 它可以是您想要的任何內容的字串。 此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。|
| `scope` | 針對應用程式授與存取權的許可權集合。|
| `admin_consent` | 將設定為 `True`。|

### <a name="error-response"></a>錯誤回應

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

加入至成功回應中所看到的參數，錯誤參數如下所示。

| 參數 | 描述 |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error` | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。|
| `error_description` | 可協助開發人員識別錯誤根本原因的特定錯誤訊息。|
| `tenant`| 將應用程式所要求的權限授與應用程式的目錄租用戶 (採用 GUID 格式)。|
| `state` | 一個包含在要求中而將一併在權杖回應中傳回的值。 它可以是您想要的任何內容的字串。 此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。|
| `admin_consent` | 將設定為， `True` 表示系統管理員同意流程發生此回應。|

## <a name="next-steps"></a>後續步驟
- 請參閱[如何將應用程式轉換成多租用戶](howto-convert-app-to-be-multi-tenant.md)
- 瞭解在 [授權碼授與流程期間，OAuth 2.0 通訊協定層如何支援同意](v2-oauth2-auth-code-flow.md#request-an-authorization-code)。
- 瞭解 [多租使用者應用程式如何使用同意架構](./howto-convert-app-to-be-multi-tenant.md) 來實行「使用者」和「系統管理員」同意，以支援更高階的多層式應用程式模式。
- 瞭解 [Azure AD 應用程式同意體驗](application-consent-experience.md)