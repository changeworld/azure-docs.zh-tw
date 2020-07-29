---
title: 尋找自訂開發應用程式的 API |Azure
description: 如何在您的自訂開發 Azure AD 應用程式中設定存取特定 API 所需的權限
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: cd3b21050c6a442284647212fdf7c5707943ffc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885611"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>如何尋找自訂開發應用程式所需的特定 API

存取 API 需要設定存取範圍和角色。 如果您想要將資源應用程式 Web API 公開給用戶端應用程式，您需要設定 API 的存取範圍和角色。 如果您想要讓用戶端應用程式存取 Web API，您需要設定權限以存取應用程式註冊中的 API。

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>設定資源應用程式以公開 Web API

當您公開 Web API 時，該 API 會在將權限新增到應用程式註冊時顯示於 [選取 API]**** 清單中。 若要新增存取範圍，請遵循[設定應用程式以公開 Web api](quickstart-configure-app-expose-web-apis.md)中所述的步驟。

## <a name="configuring-a-client-application-to-access-web-apis"></a>設定用戶端應用程式以存取 Web API

當您將權限新增到應用程式註冊時，您可以**新增 API 存取**以公開 Web API。 若要存取 web Api，請遵循[設定用戶端應用程式以存取 Web api](quickstart-configure-app-access-web-apis.md)中所述的步驟。

## <a name="next-steps"></a>後續步驟

- [了解 Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
