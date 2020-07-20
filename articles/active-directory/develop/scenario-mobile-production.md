---
title: 準備行動應用程式-針對生產環境呼叫 web Api |Azure
titleSuffix: Microsoft identity platform
description: 了解如何建置會呼叫 Web API 的行動應用程式。 （準備適用于生產環境的應用程式）。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882534"
---
# <a name="prepare-mobile-apps-for-production"></a>準備用於生產的行動應用程式

本文提供有關如何改善行動應用程式的品質和可靠性，再將其移至生產環境的詳細資料。

## <a name="handle-errors"></a>處理錯誤

當您準備用於生產的行動應用程式時，可能會發生數個錯誤狀況。 您將處理的主要案例是無訊息失敗，並會回到互動。 您應該考慮的其他條件包括：無網路狀況、服務中斷、系統管理員同意需求，以及其他案例特定的情況。

針對每個「Microsoft 驗證程式庫」（MSAL）類型，您可以找到說明如何處理錯誤狀況的範例程式碼和 wiki 內容：

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>緩和和調查問題

若要更有效地診斷應用程式中的問題，請收集資料。 如需您可以收集之資料類型的相關資訊，請參閱[MSAL 應用程式中的記錄](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)。

以下是資料收集的一些建議：

- 當使用者遇到問題時，可能會要求您提供協助。 最佳做法是捕捉並暫時儲存記錄。 提供使用者可上傳記錄的位置。 MSAL 提供記錄延伸模組來捕獲有關驗證的詳細資訊。

- 如果有可用的遙測，請透過 MSAL 加以啟用，以收集使用者如何登入您的應用程式的相關資料。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

若要嘗試其他範例，請參閱[桌面和行動裝置公用用戶端應用程式](sample-v2-code.md#desktop-and-mobile-public-client-apps)。
