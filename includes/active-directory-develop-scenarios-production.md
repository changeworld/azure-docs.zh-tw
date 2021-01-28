---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98954949"
---
## <a name="enable-logging"></a>啟用記錄

為了協助偵測和驗證失敗的疑難排解案例，Microsoft 驗證程式庫提供內建的記錄支援。 記錄是下列文章中涵蓋的每個程式庫：

:::row:::
    :::column:::
        - [MSAL.NET 中的記錄](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Android 版 MSAL 中的記錄](../articles/active-directory/develop/msal-logging-android.md)
        - [MSAL.js 中的記錄](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [iOS/macOS 版 MSAL 中的記錄](../articles/active-directory/develop/msal-logging-ios.md)
        - [Java 版 MSAL 中的記錄](../articles/active-directory/develop/msal-logging-java.md)
        - [Python 版 MSAL 中的記錄](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

以下是資料收集的一些建議：

- 當使用者遇到問題時，可能會要求協助。 最佳做法是捕捉並暫時儲存記錄。 提供可讓使用者上傳記錄檔的位置。 MSAL 會提供記錄延伸模組，以抓取有關驗證的詳細資訊。

- 如果有可用的遙測，請透過 MSAL 加以啟用，以收集使用者如何登入應用程式的相關資料。


## <a name="validate-your-integration"></a>驗證您的整合

遵循 [Microsoft 身分識別平台整合檢查清單](../articles/active-directory/develop/identity-platform-integration-checklist.md)來測試您的整合。
