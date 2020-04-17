---
title: 將呼叫 Web API 的 Web API 移動到生產 - 微軟身份平臺 |蔚藍
description: 瞭解如何將調用 Web API 的 Web API 移至生產。
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537146"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>呼叫 Web API 的 Web API:移轉到生產

獲取調用 Web API 的權杖後,可以將應用移動到生產。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>深入了解

現在,您已經瞭解了如何從您自己的 Web API 調用 Web API 的基礎知識,您可能對以下教程感興趣,本教程描述了用於建構呼叫 Web API 的受保護 Web API 的代碼。

| 範例 | 平台 | 描述 |
|--------|----------|-------------|
| [活動-目錄-阿斯普內核心-webapi-教程-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET核心 2.2 Web API,桌面 (WPF) | ASP.NET Core 2.2 Web API 呼叫 Microsoft Graph,您可以使用 Microsoft 識別平臺 (v2.0) 從 WPF 應用程式呼叫它。 |
