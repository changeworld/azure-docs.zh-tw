---
title: 應用的服務條款和隱私聲明 |蔚藍
description: 了解如何為註冊使用 Azure AD 的應用程式，設定服務條款和隱私權聲明。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 40e7a05505bc501c1c622e627a6d97cc57db1cfa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884234"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>如何:為應用配置服務條款和隱私聲明

開發人員所建置和管理的應用程式若與 Azure Active Directory (Azure AD) 和 Microsoft 帳戶整合，就應該包含應用程式服務條款和隱私權聲明的連結。 使用者會透過使用者同意體驗看到服務條款和隱私權聲明。 服務條款和隱私權聲明可協助使用者了解應用程式是可信的。 對於面向使用者的多租用戶應用程式 (供多個目錄使用或可供任何 Microsoft 帳戶使用的應用程式) 來說，服務條款和隱私權聲明特別重要。

您必須負責建立應用程式的服務條款和隱私權聲明文件，並提供這些文件的 URL。 對於無法提供這些連結的多租用戶應用程式，應用程式的使用者同意體驗會顯示警示，從而讓使用者不敢同意應用程式。

> [!NOTE]
> * 單一租用戶應用程式不會顯示警示。
> * 如果這兩個連結有所遺漏 (其中一個或兩個都遺漏)，應用程式會顯示警示。

## <a name="user-consent-experience"></a>使用者同意體驗

下列範例顯示在設定服務條款和隱私權聲明時，以及未設定這些連結時的使用者同意體驗。

![具有和沒有所提供隱私權聲明與服務條款的螢幕擷取畫面](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>將服務條款和隱私權聲明文件的連結格式化

在新增應用程式的服務條款和隱私權聲明文件連結前，請確定 URL 符合下列指導方針。

| 指導方針     | 描述                           |
|---------------|---------------------------------------|
| [格式]        | 有效的 URL                             |
| 有效的結構描述 | HTTP 和 HTTPS<br/>建議使用 HTTPS |
| 最大長度    | 2048 個字元                       |

範例：`https://myapp.com/terms-of-service` 和 `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>新增服務條款和隱私權聲明的連結

準備好服務條款和隱私權聲明時，即可使用下列其中一種方法在應用程式中新增這些文件的連結：

* [通過 Azure 門戶](#azure-portal)
* [使用應用程式物件 JSON](#app-object-json)
* [使用微軟圖形 API](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>使用 Azure 入口網站
按照 Azure 門戶中的這些步驟操作。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 瀏覽至 [應用程式註冊]**** 區段，然後選取應用程式。
3. 打開 **"品牌"** 窗格。
4. 填妥 [服務條款 URL]**** 和 [隱私權聲明 URL]**** 欄位。
5. 儲存您的變更。

    ![套用屬性包含服務條款和隱私聲明網址](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>使用應用程式物件 JSON

如果您想要直接修改應用程式物件 JSON，則可以在 Azure 入口網站或應用程式註冊入口網站中使用資訊清單編輯器，以包含應用程式的服務條款和隱私權聲明連結。

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>使用微軟圖形 API

要以程式設計方式更新所有應用,可以使用 Microsoft 圖形 API 更新所有應用,以包括指向服務條款和隱私聲明文檔的連結。

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * 請小心避免覆寫已指派給下列欄位的預先存在值：`supportUrl`、`marketingUrl` 和 `logoUrl`
> * 僅當使用 Azure AD 帳戶登錄時,Microsoft 圖形 API 才有效。 不支援 Microsoft 個人帳戶。
