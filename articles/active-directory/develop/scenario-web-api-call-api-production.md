---
title: 將 Web API 呼叫 web Api 移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將呼叫 web Api 的 Web API 移至生產環境。
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
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537146"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>呼叫 web Api 的 Web API：移至生產環境

取得權杖以呼叫 web Api 之後，您就可以將應用程式移至生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>深入了解

既然您已經知道如何從自己的 Web API 呼叫 web Api 的基本概念，您可能會對下列教學課程感興趣，其中說明用來建立受保護 Web API 以呼叫 web Api 的程式碼。

| 範例 | 平台 | 說明 |
|--------|----------|-------------|
| [active directory-aspnetcore-webapi-教學課程-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API、Desktop （WPF） | ASP.NET Core 2.2 Web API 會呼叫 Microsoft Graph，您可以使用 Microsoft 身分識別平臺（v2.0）從 WPF 應用程式呼叫。 |
