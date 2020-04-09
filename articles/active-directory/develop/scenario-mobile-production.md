---
title: 準備移動應用調用 Web API 進行生產 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建構調用 Web API 的行動應用。 (為生產準備應用。
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882534"
---
# <a name="prepare-mobile-apps-for-production"></a>準備移動應用進行生產

本文詳細介紹了如何在將移動應用移動到生產之前提高移動應用的品質和可靠性。

## <a name="handle-errors"></a>處理錯誤

在為生產準備移動應用時,可能會出現多個錯誤條件。 您將處理的主要情況是靜默故障和對交互的回退。 應考慮的其他條件包括無網路情況、服務中斷、管理員同意要求以及其他特定於方案的情況。

對每種 Microsoft 認證函式庫 (MSAL) 型態,您可以找到描述如何處理錯誤條件的範例碼和 wiki 內容:

- [MSAL 安卓維琪](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET維琪](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>緩解和調查問題

為了更好地診斷應用中的問題,請收集數據。 有關可以收集的資料型態的資訊,請參閱[在 MSAL 應用程式中登入](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)。

以下是資料收集的一些建議:

- 當使用者遇到問題時,他們可能會尋求説明。 最佳做法是捕獲和臨時存儲日誌。 提供使用者可以上傳日誌的位置。 MSAL 提供日誌記錄擴展,以捕獲有關身份驗證的詳細資訊。

- 如果遙測可用,請通過 MSAL 使其收集有關使用者如何登錄到你的應用的數據。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

要嘗試其他範例,請參閱[桌面與行動公共用戶端應用](sample-v2-code.md#desktop-and-mobile-public-client-apps)。
