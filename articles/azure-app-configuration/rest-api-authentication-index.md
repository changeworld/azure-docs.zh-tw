---
title: Azure 應用程式組態 REST API-驗證
description: 使用 Azure 應用程式組態 REST API 進行驗證的參考頁面
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932688"
---
# <a name="authentication"></a>驗證

所有 HTTP 要求都必須經過驗證。 以下是支援的驗證配置。

## <a name="hmac"></a>HMAC

[HMAC 驗證](./rest-api-authentication-hmac.md) 會使用隨機產生的秘密來簽署要求承載。 您可以在 [HMAC 授權](./rest-api-authorization-hmac.md) 區段中找到使用此驗證方法之要求的授權詳細資料。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) 驗證](../active-directory/authentication/overview-authentication.md) 會利用從 Azure Active Directory 取得的持有人權杖來驗證要求。 若要瞭解如何使用此驗證方法來取得要求的詳細資訊，請參閱 [Azure AD 授權](./rest-api-authorization-azure-ad.md) 區段。