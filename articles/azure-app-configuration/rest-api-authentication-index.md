---
title: Azure 應用程式組態 REST API-驗證
description: 使用 Azure 應用程式組態 REST API 進行驗證的參考頁面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424097"
---
# <a name="authentication"></a>驗證

所有 HTTP 要求都必須經過驗證。 以下是支援的驗證配置。

## <a name="hmac"></a>HMAC

[HMAC 驗證](./rest-api-authentication-hmac.md) 會使用隨機產生的秘密來簽署要求承載。 您可以在 [HMAC 授權](./rest-api-authorization-hmac.md) 區段中找到使用此驗證方法之要求的授權詳細資料。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) 驗證](/azure/active-directory/authentication/overview-authentication) 會利用從 Azure Active Directory 取得的持有人權杖來驗證要求。 若要瞭解如何使用此驗證方法來取得要求的詳細資訊，請參閱 [Azure AD 授權](./rest-api-authorization-azure-ad.md) 區段。
