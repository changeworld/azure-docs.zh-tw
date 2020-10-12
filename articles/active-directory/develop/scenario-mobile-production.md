---
title: 準備用於生產的行動應用程式呼叫 web Api |蔚藍
titleSuffix: Microsoft identity platform
description: '了解如何建置會呼叫 Web API 的行動應用程式。  (準備生產環境的應用程式。 ) '
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
ms.openlocfilehash: d1a7407c947c1c5bae1eed00acb0f216722f3d09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121029"
---
# <a name="prepare-mobile-apps-for-production"></a>準備適用于生產環境的行動應用程式

本文提供如何在將行動裝置應用程式移至生產環境之前，改善其品質和可靠性的詳細資料。

## <a name="handle-errors"></a>處理錯誤

當您準備用於生產的行動應用程式時，可能會發生數個錯誤狀況。 您將處理的主要案例是無訊息失敗，而且會回到互動。 您應考慮的其他條件包括：無網路情況、服務中斷、系統管理員同意的需求，以及其他案例特定案例。

針對每個 Microsoft 驗證程式庫 (MSAL) 類型，您可以找到說明如何處理錯誤狀況的範例程式碼和 wiki 內容：

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>緩和和調查問題

若要更妥善診斷應用程式中的問題，請收集資料。 如需您可以收集之資料類型的相關資訊，請參閱 [MSAL 應用程式中的記錄](./msal-logging.md)。

以下是資料收集的一些建議：

- 當使用者遇到問題時，可能會要求協助。 最佳做法是捕捉並暫時儲存記錄。 提供可讓使用者上傳記錄檔的位置。 MSAL 會提供記錄延伸模組，以抓取有關驗證的詳細資訊。

- 如果有可用的遙測，請透過 MSAL 加以啟用，以收集使用者如何登入應用程式的相關資料。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

若要試用其他範例，請參閱 [Desktop 和 mobile public 用戶端應用程式](sample-v2-code.md#desktop-and-mobile-public-client-apps)。