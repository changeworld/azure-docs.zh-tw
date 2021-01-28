---
title: 在 MSAL for Python 中記錄錯誤和例外狀況
titleSuffix: Microsoft identity platform
description: 瞭解如何在適用于 Python 的 MSAL 中記錄錯誤和例外狀況
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 560caa7e29ce12b58e151a1362aaf2c662646f13
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954795"
---
# <a name="logging-in-msal-for-python"></a>Python 版 MSAL 中的記錄

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>適用于 Python 記錄的 MSAL

MSAL Python 中的記錄會使用標準的 Python 記錄機制，例如， `logging.info("msg")` 您可以設定 MSAL 記錄，如下所示 (，並在 [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)) 中查看其運作方式：

### <a name="enable-debug-logging-for-all-modules"></a>啟用所有模組的偵錯工具記錄

依預設，會關閉任何 Python 腳本中的記錄功能。 如果您想要啟用整個 Python 腳本中所有模組的 debug 記錄，請使用：

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>僅無回應 MSAL 記錄

若只要無回應 MSAL 程式庫記錄，並在 Python 腳本中的所有其他模組啟用 debug 記錄，請關閉 MSAL Python 所使用的記錄器：

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python 中的個人和組織資料

適用于 Python 的 MSAL 不會記錄個人資料或組織資料。 沒有任何屬性可以開啟或關閉個人或組織資料記錄。

您可以使用標準的 Python 記錄來記錄任何您想要的內容，但您必須負責安全地處理敏感性資料和遵循法規需求。

如需有關在 Python 中記錄的詳細資訊，請參閱 Python 的  [記錄：操作說明](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)。

## <a name="next-steps"></a>下一步

如需更多程式碼範例，請參閱 [Microsoft 身分識別平臺程式碼範例](sample-v2-code.md)。

---