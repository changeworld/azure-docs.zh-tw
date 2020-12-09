---
title: Azure 應用程式組態 REST API-授權
description: 使用 Azure 應用程式組態 REST API 的授權參考頁面
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932637"
---
# <a name="authorization"></a>授權

授權是指在提出要求時，用來判斷呼叫端所擁有之許可權的程式。 有多個授權模型。 用於要求的授權模型取決於所使用的 [驗證](./rest-api-authentication-index.md) 方法。 授權模型如下所示。

## <a name="hmac"></a>HMAC

與 HMAC 驗證相關聯的 [授權模型](./rest-api-authorization-hmac.md) 模型會將許可權分割為唯讀或讀寫。 如需詳細資訊，請參閱 [HMAC 授權](./rest-api-authorization-hmac.md) 頁面。

## <a name="azure-active-directory"></a>Azure Active Directory

與 Azure Active Directory (Azure AD) authentication 相關聯的 [授權模型](./rest-api-authorization-azure-ad.md) 會使用 Azure RBAC 來控制許可權。 如需詳細資訊，請參閱 [Azure AD 授權](./rest-api-authorization-azure-ad.md) 頁面。
