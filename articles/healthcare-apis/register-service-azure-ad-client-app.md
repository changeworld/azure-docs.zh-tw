---
title: 在 Azure AD Azure API for FHIR 中註冊服務應用程式
description: 瞭解如何在 Azure Active Directory 中註冊服務用戶端應用程式。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 19d6b0ebfa2570b04c3a9dda3fe69428aa0eed75
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629272"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊服務用戶端應用程式

在本文中，您將瞭解如何在 Azure Active Directory 中註冊服務用戶端應用程式。 用戶端應用程式註冊是應用程式的 Azure Active Directory 標記法，可用來驗證和取得權杖。 服務用戶端的目的是要由應用程式用來取得存取權杖，而不需要使用者進行互動式驗證。 在取得存取權杖時，它會有特定的應用程式許可權，並使用應用程式密碼 (密碼) 。

請遵循下列步驟來建立新的服務用戶端。

## <a name="app-registrations-in-azure-portal"></a>Azure 入口網站中的應用程式註冊

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至 **Azure Active Directory**。

2. 選取 **應用程式註冊**。

    ![Azure 入口網站。 新的應用程式註冊。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 選取 [新增註冊]。

4. 提供服務用戶端的顯示名稱。 服務用戶端應用程式通常不會使用回復 URL。

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure 入口網站。新增服務用戶端應用程式註冊。":::

5. 選取 [註冊]。

## <a name="api-permissions"></a>API 權限

您已註冊應用程式，接下來您必須選取此應用程式應該要能夠代表使用者要求的 API 許可權：

1. 選取 [API 權限]****。
1. 選取 [新增權限]。

    如果您使用 Azure API for FHIR，您會在**我的組織使用的 api**下搜尋**azure 醫療保健 api** ，以新增 azure 醫療保健 api 的許可權。 

    如果您參考不同的資源應用程式，請選取您先前在**api**下建立的[FHIR API 資源應用程式註冊](register-resource-azure-ad-client-app.md)。

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Azure 入口網站。新增服務用戶端應用程式註冊。" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. 選取 [範圍] () 機密應用程式應該能夠代表使用者要求的許可權：

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Azure 入口網站。新增服務用戶端應用程式註冊。":::

1. 將同意授與應用程式。 如果您沒有必要的許可權，請洽詢您的 Azure Active Directory 系統管理員：

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Azure 入口網站。新增服務用戶端應用程式註冊。":::

## <a name="application-secret"></a>應用程式祕密

服務用戶端需要秘密 (密碼) 才能取得權杖。

1. 選取 [憑證和秘密]。
2. 選取 [新增用戶端密碼]。

    ![Azure 入口網站。 服務用戶端密碼](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 提供秘密 (1 年、2年或永不) 的描述和持續時間。

4. 一旦產生密碼之後，它只會在入口網站中顯示一次。 請記下它，並將其儲存在安全的地方。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Active Directory 中註冊服務用戶端應用程式。 接下來，您可以瞭解 Azure API for FHIR 的其他設定。
 
>[!div class="nextstepaction"]
>[其他設定](azure-api-for-fhir-additional-settings.md)