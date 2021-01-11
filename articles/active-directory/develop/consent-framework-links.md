---
title: 應用程式同意如何運作
description: 深入了解 Azure AD 同意架構的運作方式，以了解如何在開發搭配 Azure AD 執行的應用程式時使用它
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 5db45ea49f7e0be674f07a3f8e077656f3d6dee2
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064982"
---
# <a name="how-application-consent-works"></a>應用程式同意如何運作

此文章旨在協助您深入了解 Azure AD 同意架構的運作方式，以便您可以更有效地開發應用程式。

## <a name="recommended-documents"></a>建議的文件

- 取得有關[同意如何讓資源擁有者控管應用程式對資源的存取權](./developer-glossary.md#consent)的基本知識。
- 取得有關 [Azure AD 同意架構如何實作同意](./quickstart-register-app.md)的逐步概觀。
- 深入了解[多租用戶應用程式如何使用同意架構](./howto-convert-app-to-be-multi-tenant.md)來實作「使用者」與「系統管理員」同意，進而支援更進階的多層應用程式模式。
- 深入了解[授權碼授與流程期間 OAuth 2.0 通訊協定層如何支援同意](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)。

## <a name="next-steps"></a>後續步驟
[AzureAD StackOverflow (英文)](https://stackoverflow.com/questions/tagged/azure-active-directory)