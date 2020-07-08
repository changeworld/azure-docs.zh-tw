---
title: 自訂權杖快取序列化（MSAL Python） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何序列化適用于 Python 的 MSAL 權杖快取
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, tracking-python
ms.openlocfilehash: 57efe11f0c44d7dfe6d35f2e56d512d6e2e2fa98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85479278"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>適用于 Python 的 MSAL 中的自訂權杖快取序列化

在 MSAL Python 中，當您建立[ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)的實例時，預設會提供持續存在於應用程式會話期間的記憶體內部權杖快取。

權杖快取的序列化，讓應用程式的不同會話可以存取它，但不會提供「現成可用」。 這是因為 MSAL Python 可用於無法存取檔案系統的應用程式類型（例如 Web apps）。 若要在 MSAL Python 應用程式中擁有持續性權杖快取，您必須提供自訂權杖快取序列化。

序列化權杖快取的策略會因您撰寫的是公用用戶端應用程式（桌面）或機密用戶端應用程式（web 應用程式、Web API 或 daemon 應用程式）而有所不同。

## <a name="token-cache-for-a-public-client-application"></a>公用用戶端應用程式的權杖快取

公用用戶端應用程式會在使用者的裝置上執行，並管理單一使用者的權杖。 在此情況下，您可以將整個快取序列化成檔案。 如果您的應用程式或其他應用程式可以同時存取快取，請記得提供檔案鎖定。 如需如何將權杖快取序列化至檔案而不鎖定的簡單範例，請參閱[SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)類別參考檔中的範例。

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web 應用程式的權杖快取（機密用戶端應用程式）

針對 web 應用程式或 web Api，您可以使用會話或 Redis 快取，或資料庫來儲存權杖快取。 每個使用者應該有一個權杖快取（每個帳戶），因此請確定您會將每個帳戶的權杖快取序列化。

## <a name="next-steps"></a>後續步驟

如需如何使用 Windows 或 Linux Web 應用程式的權杖快取或 Web API 的範例，請參閱[ms-身分識別 webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) 。 此範例適用于呼叫 Microsoft Graph API 的 web 應用程式。
