---
title: 對應用程式執行同意時發生非預期的錯誤 | Microsoft Docs
description: 討論對應用程式執行同意的程序期間會發生的錯誤，以及可對錯誤採取的動作
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a7709cf0522727257025b2dddc495b20fe8448
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763749"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>對應用程式執行同意時出現非預期的錯誤

這篇文章討論對應用程式進行同意的程序期間會發生的錯誤。 如果您正在針對未包含任何錯誤訊息的非預期同意提示進行疑難排解，請參閱 [Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)。

許多與 Azure Active Directory 整合的應用程式需要能存取其他資源的權限，才能夠運作。 當這些資源也與 Azure Active Directory 整合時，通常會使用一般的同意架構來要求存取這些資源的權限。 會顯示同意提示，一般會發生在第一次使用應用程式時，但也會發生在後續使用應用程式時。

某些條件必須成立，使用者才能同意應用程式所需的權限。 如果不符合這些條件，便會發生下列錯誤。

## <a name="requesting-not-authorized-permissions-error"></a>要求未經授權權限的錯誤
* **AADSTS90093：** &lt;clientAppDisplayName&gt; 正在要求一或多個您未授權授與的權限。 請連絡系統管理員，其可代表您同意此應用程式。
* **AADSTS90094：** &lt;clientAppDisplayName&gt; 需要只有系統管理員才能授與的權限來存取貴組織中的資源。 請先要求系統管理員授與此應用程式的權限，然後您才能使用。

當非公司系統管理員的使用者嘗試使用的應用程式正在要求只有系統管理員能夠授與的權限時，會發生此錯誤。 可代表其組織授與應用程式存取權的系統管理員能夠解決此錯誤。

當使用者因為 Microsoft 偵測到許可權要求有風險而無法同意應用程式時，也可能會發生此錯誤。 在此情況下，也會記錄「ApplicationManagement」類別、「同意應用程式的活動」和「偵測到具風險的應用程式」的狀態原因的「audit 事件」。

## <a name="policy-prevents-granting-permissions-error"></a>原則禁止授與權限錯誤
* **AADSTS90093：**&lt;tenantDisplayName&gt; 的系統管理員設定了原則，讓您無法將 &lt;應用程式名稱&gt; 正在要求的權限授與該應用程式。 請連絡 &lt;tenantDisplayName&gt; 的系統管理員，其可代表您將權限授與此應用程式。

當公司系統管理員關閉使用者同意應用程式的功能，接著非系統管理員使用者嘗試使用需要同意的應用程式時，會發生此錯誤。 可代表其組織授與應用程式存取權的系統管理員能夠解決此錯誤。

## <a name="intermittent-problem-error"></a>間歇性問題錯誤
* **AADSTS90090：** 看來登入程序在記錄您嘗試授與 &lt;clientAppDisplayName&gt; 的權限時似乎發生問題，此問題間歇性發生。 請稍後再試一次。

此錯誤表示已發生間歇性服務端問題。 再次嘗試同意應用程式，可解決此問題。

## <a name="resource-not-available-error"></a>資源無法使用錯誤
* **AADSTS65005：** 應用程式 &lt;clientAppDisplayName&gt; 要求可存取資源 &lt;resourceAppDisplayName&gt; 的權限，但該資源無法使用。 

請連絡應用程式開發人員。

##  <a name="resource-not-available-in-tenant-error"></a>資源在租用戶無法使用錯誤
* **AADSTS65005：** &lt;clientAppDisplayName&gt; 正在要求存取資源 &lt;resourceAppDisplayName&gt;，但此資源在您的 &lt;tenantDisplayName&gt; 組織中無法使用。 

請確認此資源可以使用，或與 &lt;tenantDisplayName&gt; 的系統管理員連絡。

## <a name="permissions-mismatch-error"></a>權限不符合錯誤
* **AADSTS65005：** 應用程式要求同意存取 &lt;resourceAppDisplayName&gt; 資源。 此要求失敗，因為它不符合在應用程式註冊期間預先設定應用程式的方式。 請連絡應用程式廠商。**

當使用者嘗試同意的應用程式正在要求存取某個資源應用程式的權限，但在組織的目錄 (租用戶) 中找不到該應用程式時，這些錯誤全部發生。 發生這種狀況的原因有數種：

-   用戶端應用程式開發人員將其應用程式設定錯誤，導致應用程式要求存取無效的資源。 在此狀況下，應用程式開發人員必須更新用戶端應用程式的組態，以解決此問題。

-   代表目標資源應用程式的服務主體不存在於組織中，或是過去曾存在，但現已遭移除。 若要解決此問題，在組織中必須佈建資源應用程式的服務主體，讓用戶端應用程式可以向主體要求權限。 服務主體可以用數種方式佈建，這取決於應用程式的類型，包括：

    -   取得資源應用程式 (Microsoft 發佈的應用程式) 的訂用帳戶

    -   同意資源應用程式

    -   透過 Azure 入口網站授與應用程式權限

    -   從 Azure AD 應用程式資源庫新增應用程式

## <a name="next-steps"></a>後續步驟 

[Azure Active Directory 中的應用程式、權限及同意 (v1 端點)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Azure Active Directory v2.0 端點中的範圍、權限及同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


