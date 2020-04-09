---
title: 尋找自訂開發應用的 API |蔚藍
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885611"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>如何尋找自訂開發應用程式所需的特定 API

存取 API 需要設定存取範圍和角色。 如果您想要將資源應用程式 Web API 公開給用戶端應用程式，您需要設定 API 的存取範圍和角色。 如果您想要讓用戶端應用程式存取 Web API，您需要設定權限以存取應用程式註冊中的 API。

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>設定資源應用程式以公開 Web API

當您公開 Web API 時，該 API 會在將權限新增到應用程式註冊時顯示於 [選取 API]**** 清單中。 要添加訪問範圍,請按照[配置應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)中概述的步驟操作。

## <a name="configuring-a-client-application-to-access-web-apis"></a>設定用戶端應用程式以存取 Web API

當您將權限新增到應用程式註冊時，您可以**新增 API 存取**以公開 Web API。 要存取 Web API,請按照[設定用戶端應用程式以造訪 Web API](quickstart-configure-app-access-web-apis.md)中概述的步驟操作。

## <a name="next-steps"></a>後續步驟

- [了解 Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
