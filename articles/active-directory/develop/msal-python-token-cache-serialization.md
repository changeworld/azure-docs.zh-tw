---
title: 自訂權杖緩存序列化 （MSAL Python） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何為 Python 的 MSAL 序列化權杖緩存
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76704386"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Python 的 MSAL 中的自訂權杖緩存序列化

在 MSAL Python 中，預設情況下在創建[用戶端應用程式](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)實例時提供在記憶體中保留的權杖緩存。

權杖緩存的序列化，以便應用的不同會話可以訪問它，不會"開箱即用"。 這是因為 MSAL Python 可用於無法訪問檔案系統的應用類型，如 Web 應用。 要在 MSAL Python 應用中具有持久權杖緩存，必須提供自訂權杖緩存序列化。

序列化權杖緩存的策略因您編寫公共用戶端應用程式（桌面）還是機密用戶端應用程式（Web 應用程式、Web API 或守護進程應用程式）而異。

## <a name="token-cache-for-a-public-client-application"></a>公共用戶端應用程式的權杖緩存

公共用戶端應用程式在使用者設備上運行，並管理單個使用者的權杖。 在這種情況下，您可以將整個緩存序列化到檔中。 請記住，如果應用或其他應用可以同時訪問緩存，則提供檔鎖定。 有關如何在不鎖定的情況下將權杖緩存序列化到檔的簡單示例，請參閱[SerialableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)類引用文檔中的示例。

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Web 應用的權杖緩存（機密用戶端應用程式）

對於 Web 應用或 Web API，您可以使用會話、Redis 緩存或資料庫來存儲權杖緩存。 每個使用者（每個帳戶）應該有一個權杖緩存，因此請確保每個帳戶序列化權杖緩存。

## <a name="next-steps"></a>後續步驟

有關如何為 Windows 或 Linux Web 應用或 Web API 使用權杖緩存的示例，請參閱[ms 身份-python-webapp。](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) 該示例適用于調用 Microsoft 圖形 API 的 Web 應用。
