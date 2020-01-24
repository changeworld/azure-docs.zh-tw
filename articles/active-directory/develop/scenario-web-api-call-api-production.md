---
title: 將 Web API 呼叫 web Api 移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將呼叫 web Api 的 Web API 移至生產環境。
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
ms.custom: aaddev
ms.openlocfilehash: 474f771f007666179295f4502108acee88d1dc33
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701717"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>呼叫 web Api 的 Web API：移至生產環境

取得權杖以呼叫 web Api 之後，您就可以將應用程式移至生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>了解更多

既然您已經知道如何從自己的 Web API 呼叫 web Api 的基本概念，您可能會對下列教學課程感興趣，其中說明用來建立受保護 Web API 以呼叫 web Api 的程式碼。

| 範例 | 平台 | 說明 |
|--------|----------|-------------|
| [active directory-aspnetcore-webapi-教學課程-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API、Desktop （WPF） | ASP.NET Core 2.2 Web API 呼叫 Microsoft Graph，您可以使用 Microsoft 身分識別平臺（v2.0）從 WPF 應用程式呼叫。 |
