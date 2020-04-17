---
title: 受保護的 Web API - 概述
titleSuffix: Microsoft identity platform
description: 瞭解如何構建受保護的 Web API(概述)。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537197"
---
# <a name="scenario-protected-web-api"></a>方案:受保護的 Web API

在這種情況下,您將瞭解如何公開 Web API。 您還學習如何保護 Web API,以便只有經過身份驗證的使用者才能訪問它。

要使用 Web API,您需要啟用具有工作帳戶和學校帳戶的經過身份驗證的使用者,或者啟用 Microsoft 個人帳戶。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>詳細資料

以下是保護 Web API 所需的特定資訊:

- 你的應用註冊必須至少公開一個作用域。 Web API 接受的權杖版本取決於登錄訪問群體。
- Web API 的代碼設定必須驗證呼叫 Web API 時使用的權杖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-protected-web-api-app-registration.md)
