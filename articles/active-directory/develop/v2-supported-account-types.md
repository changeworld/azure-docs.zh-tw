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
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b3b0114bb5d545755fe59c49605d6def341d2275
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535769"
---
# <a name="supported-account-types"></a>支援的帳戶類型

本文說明應用程式中支援哪些帳戶類型（有時稱為「物件」）。

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Microsoft 身分識別平台應用程式中支援的帳戶類型

在 Microsoft Azure 公用雲端中，大部分應用程式類型都能夠以任何對象身分來登入使用者：

- 如果您要撰寫企業營運 (LOB) 應用程式，您可以在自己的組織中登入使用者。 這類應用程式有時稱為**單一租用戶**。
- 如果您是 ISV，您可以撰寫以下列條件登入使用者的應用程式：

  - 在任何組織中。 這類應用程式稱為**多租用戶** Web 應用程式。 您有時會看到其透過使用者的公司或學校帳戶來登入使用者。
  - 使用他們的公司、學校或個人 Microsoft 帳戶。
  - 僅使用個人 Microsoft 帳戶。
    > [!NOTE]
    > 目前，Microsoft 身分識別平台支援個人 Microsoft 帳戶的方式只能藉由註冊適用於**公司、學校或個人 Microsoft 帳戶**的應用程式，然後藉由在建置應用程式時指定 Azure AD 授權單位 (例如 `https://login.microsoftonline.com/consumers`)，以在應用程式的程式碼中限制登入。

- 如果您要撰寫企業對消費者的應用程式，您也可以使用 Azure AD B2C，透過使用者的社交身分識別來登入他們。

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>某些驗證流程不支援所有帳戶類型

某些帳戶類型不能搭配特定驗證流程使用。 例如，在桌面、UWP 應用程式或精靈應用程式中：

- 精靈應用程式只能搭配 Azure Active Directory 組織使用。 嘗試使用精靈應用程式來操作 Microsoft 個人帳戶並無意義 (一律不會授與管理員同意)。
- 您只能搭配公司或學校帳戶使用整合式 Windows 驗證流程 (在您的組織或任何組織中)。 事實上，整合式 Windows 驗證會搭配網域帳戶運作，而且需要機器加入網域或加入 Azure AD。 此流程對個人 Microsoft 帳戶而言沒有任何意義。
- [資源擁有者的密碼授與](./v2-oauth-ropc.md) (使用者名稱/密碼)，不能搭配個人 Microsoft 帳戶使用。 事實上，在每個登入工作階段上，個人 Microsoft 帳戶需要可存取個人資源的使用者同意。 這就是為何此行為與非互動式流程不相容。
- 裝置程式碼流程尚不適用於個人 Microsoft 帳戶。

## <a name="supported-account-types-in-national-clouds"></a>國家雲端中支援的帳戶類型

 應用程式也可以在[國家雲端](authentication-national-cloud.md)中登入使用者。 不過，這些雲端中不支援 Microsoft 個人帳戶 (因為這些雲端的定義)。 這就是為什麼針對這些雲端，您組織 (單一租用戶) 或任何組織 (多租用戶應用程式) 的支援帳戶類型會減少。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Active Directory中的租用戶](./single-and-multi-tenant-apps.md)
- 深入了解[國家雲端](./authentication-national-cloud.md)
