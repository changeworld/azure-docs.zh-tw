---
title: 包含檔案
description: 包含檔案
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94627997"
---
您可以使用 MSAL 的權杖快取執行，讓背景應用程式、Api 和服務可以使用存取權杖快取，以繼續代表使用者執行動作。 如果背景應用程式和服務需要在使用者結束前端 web 應用程式之後，以使用者身分繼續工作，這項功能就特別有用。

目前，大部分的背景程式在需要使用使用者的資料時，會使用 [應用程式許可權](/graph/auth/auth-concepts#microsoft-graph-permissions) ，而不需要他們提供驗證或重新驗證。 由於應用程式許可權通常需要系統管理員同意（需要提高許可權），因此當開發人員不想取得使用者原本同意其應用程式的許可權時，就會發生不必要的分歧。

此 GitHub 上的程式碼範例會示範如何從背景應用程式存取 MSAL 的權杖快取，以避免這種不必要的分歧：

 [從背景應用程式、Api 和服務存取已登入使用者的權杖快取](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)