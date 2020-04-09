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
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885050"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>呼叫 Web API 的 Web API:移轉到生產

獲取調用 Web API 的權杖後,可以將應用移動到生產。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>深入了解

現在,您已經瞭解了如何從您自己的 Web API 調用 Web API 的基礎知識,您可能對以下教程感興趣,本教程描述了用於建構呼叫 Web API 的受保護 Web API 的代碼。

| 範例 | 平台 | 描述 |
|--------|----------|-------------|
| [活動-目錄-阿斯普內核心-webapi-教程-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET核心 2.2 Web API,桌面 (WPF) | ASP.NET核心 2.2 Web API 呼叫Microsoft Graph,您可以使用Microsoft識別平臺 (v2.0) 從 WPF 應用程式呼叫它。 |
