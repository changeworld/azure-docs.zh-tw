---
title: 處理 Python 版 MSAL 中的錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何處理適用于 Python 應用程式的錯誤和例外狀況、條件式存取宣告挑戰和重試 MSAL。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761081"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>處理 Python 版 MSAL 中的錯誤和例外狀況

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>適用于 Python 的 MSAL 錯誤處理

在適用於 Python 的 MSAL 中，大部分錯誤都會以 API 呼叫的傳回值形式來傳達。 此錯誤會以包含 Microsoft 身分識別平台所傳回 JSON 回應的字典來表示。

* 成功的回應會包含 `"access_token"` 索引鍵。 回應的格式則由 OAuth2 通訊協定來定義。 如需詳細資訊，請參閱 [5.1 成功回應](https://tools.ietf.org/html/rfc6749#section-5.1)
* 錯誤回應會包含 `"error"`，且通常會包含 `"error_description"`。 回應的格式則由 OAuth2 通訊協定來定義。 如需詳細資訊，請參閱 [5.2 錯誤回應](https://tools.ietf.org/html/rfc6749#section-5.2)

有錯誤傳回時，`"error_description"` 索引鍵會包含人類看得懂的訊息；而這一般又會包含 Microsoft 身分識別平台的錯誤碼。 如需各種錯誤碼的詳細資訊，請參閱[驗證與授權錯誤碼](./reference-aadsts-error-codes.md)。

在適用於 Python 的 MSAL 中，大部分錯誤都會藉由傳回錯誤值來加以處理，因此例外狀況很罕見。 `ValueError`只有當您嘗試使用程式庫的方式有問題時（例如 API 參數 (s) 格式不正確時，才會擲回例外狀況。

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>後續步驟

請考慮 [在 MSAL For Python 中啟用記錄功能](msal-logging-python.md) ，以協助您診斷和偵測問題。
