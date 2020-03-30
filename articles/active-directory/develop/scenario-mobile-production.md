---
title: 準備移動應用調用 Web API 進行生產 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建調用 Web API 的移動應用。 （為生產準備應用。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132379"
---
# <a name="prepare-mobile-apps-for-production"></a>準備移動應用進行生產

本文詳細介紹了如何在將移動應用移動到生產之前提高移動應用的品質和可靠性。

## <a name="handle-errors"></a>處理錯誤

在為生產準備移動應用時，可能會出現多個錯誤條件。 您將處理的主要情況是靜默故障和對交互的回退。 應考慮的其他條件包括無網路情況、服務中斷、管理員同意要求以及其他特定于方案的情況。

對於每種 Microsoft 身份驗證庫 （MSAL） 類型，您可以找到描述如何處理錯誤條件的示例代碼和 wiki 內容：

- [MSAL 安卓維琪](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET維琪](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>緩解和調查問題

為了更好地診斷應用中的問題，請收集資料。 有關可以收集的資料類型的資訊，請參閱[在 MSAL 應用程式中登錄](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)。

以下是資料收集的一些建議：

- 當使用者遇到問題時，他們可能會尋求説明。 最佳做法是捕獲和臨時存儲日誌。 提供使用者可以上載日誌的位置。 MSAL 提供日誌記錄擴展，以捕獲有關身份驗證的詳細資訊。

- 如果遙測可用，請通過 MSAL 使其收集有關使用者如何登錄到你的應用的資料。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

要嘗試其他示例，請參閱[桌面和移動公共用戶端應用](sample-v2-code.md#desktop-and-mobile-public-client-apps)。
