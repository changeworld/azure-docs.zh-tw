---
title: 將呼叫 web api 的 web API 移至生產-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何將呼叫 web Api 的 web API 移至生產環境。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518193"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>呼叫 web Api 的 web API：移至生產環境

取得權杖以呼叫 web Api 之後，您可以將應用程式移至生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>深入了解

現在您已瞭解如何從您自己的 web API 呼叫 web Api 的基本概念，您可能會對下列教學課程感興趣，其中說明用來建立受保護的 web API 來呼叫 web Api 的程式碼。

| 範例 | 平台 | 說明 |
|--------|----------|-------------|
| [active directory-aspnetcore-webapi-教學課程-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) 第1章 | ASP.NET Core web API、Desktop (WPF)  | ASP.NET Core web API 呼叫 Microsoft Graph，您可以使用 Microsoft 身分識別平臺 (v2.0) 從 WPF 應用程式呼叫它。 |
