---
title: Azure AD v2 Windows Desktop 快速入門 - 設定 | Microsoft Docs
description: Windows Desktop .NET (XAML) 應用程式可取得存取權杖，以及呼叫受 Azure Active Directory v2 端點保護之 API 的方法。
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: e84d99d18d999a7b629f65f9d18355321f04e538
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712509"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>將應用程式的註冊資訊新增到您的應用程式
在這個步驟中，您需要將應用程式識別碼新增到您的專案。

1.  開啟 `App.xaml.cs` 並將包含 `ClientId` 的那一行取代為：

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>下一步

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
