---
title: 支援的帳戶類型-Microsoft 身分識別平臺 |Azure
description: 有關應用程式中對象及所支援帳戶類型的概念文件
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 50a0859b58e2db0d3f644db01cfcde8c533b6871
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518125"
---
# <a name="supported-account-types"></a>支援的帳戶類型

本文說明 Microsoft 身分識別平臺應用程式中支援的帳戶類型（有時稱為*物件）。*

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>公用雲端中的帳戶類型

在 Microsoft Azure 公用雲端中，大部分類型的應用程式都可以使用任何物件登入使用者：

- 如果您要撰寫企業營運（LOB）應用程式，您可以在自己的組織中登入使用者。 這類應用程式有時稱為*單一租*使用者。
- 如果您是 ISV，您可以撰寫可登入使用者的應用程式：

  - 在任何組織中。 這類應用程式稱為多租*使用者 web 應用*程式。 有時候，您會看到它使用其公司或學校帳戶登入使用者。
  - 使用其公司或學校或個人的 Microsoft 帳戶。
  - 僅使用個人 Microsoft 帳戶。
    
- 如果您撰寫的是「企業對消費者」應用程式，您也可以使用 Azure Active Directory B2C （Azure AD B2C），以他們的社交身分識別登入使用者。

## <a name="account-type-support-in-authentication-flows"></a>驗證流程中的帳戶類型支援

某些帳戶類型不能搭配特定驗證流程使用。 例如，在桌面、UWP 或 daemon 應用程式中：

- Daemon 應用程式只能與 Azure AD 組織搭配使用。 嘗試使用 daemon 應用程式來操作 Microsoft 個人帳戶並不合理。 永遠不會授與系統管理員同意。
- 您只能使用整合式 Windows 驗證流程搭配公司或學校帳戶（在您的組織或任何組織中）。 整合式 Windows 驗證可與網域帳戶搭配使用，而且需要電腦加入網域或 Azure AD 聯結。 此流程對於個人 Microsoft 帳戶沒有意義。
- [資源擁有者密碼認證授](./v2-oauth-ropc.md)與（使用者名稱/密碼）無法搭配個人 Microsoft 帳戶使用。 個人 Microsoft 帳戶會要求使用者同意在每個登入會話存取個人資源。 這就是這種行為與非互動式流程不相容的原因。

## <a name="account-types-in-national-clouds"></a>國家雲端中的帳戶類型

應用程式也可以在[國家雲端](authentication-national-cloud.md)中登入使用者。 不過，這些雲端不支援 Microsoft 個人帳戶。 這就是為什麼針對這些雲端，支援的帳戶類型會減少到您的組織（單一租使用者）或任何組織（多租使用者應用程式）。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure Active Directory 中的](./single-and-multi-tenant-apps.md)租用。
- 深入瞭解[國家](./authentication-national-cloud.md)雲端。
