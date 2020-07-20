---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011985"
---
適用于 .NET 的[Microsoft Azure 應用程式驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)用戶端程式庫 (預覽) 可簡化從程式碼取得和更新權杖的流程。 應用程式驗證用戶端程式庫會自動管理驗證。 程式庫會使用開發人員的認證, 在本機開發期間進行驗證。 在本機開發期間使用開發人員認證會比較安全，因為您不需要建立 Azure AD 認證，或在開發人員之間共用認證。 當解決方案稍後部署至 Azure 時, 程式庫會自動切換為使用應用程式認證。

若要在 Azure 儲存體應用程式中使用應用程式驗證程式庫, 請從[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)安裝最新的預覽套件, 以及適用于[.net 的 Azure 儲存體常見用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)和[Azure Blob 儲存體用戶端程式庫的最新版本適用于 .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)。 將下列**using**語句新增至您的程式碼:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
