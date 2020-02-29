---
title: 在客戶註冊期間停用電子郵件驗證
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 的客戶註冊期間停用電子郵件驗證。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c598664ef721103740716ad6215ddaea53bc635f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183069"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中的客戶註冊期間停用電子郵件驗證

根據預設，Azure Active Directory B2C （Azure AD B2C）會驗證您客戶的本機帳戶電子郵件地址（使用電子郵件地址或使用者名稱註冊之使用者的帳戶）。 Azure AD B2C 藉由要求客戶在註冊程式期間進行驗證，來確保有效的電子郵件地址。 它也可防止惡意執行者在您的應用程式中使用自動化進程來產生詐騙帳戶。

有些應用程式開發人員偏好在註冊過程中略過電子郵件驗證，而改為讓客戶在稍後驗證其電子郵件地址。 為支援此方式，您可以將 Azure AD B2C 設定為停用電子郵件驗證。 這麼做會建立更流暢的註冊程式，讓開發人員能夠彈性地區分已驗證其電子郵件地址的客戶，而不是。

請遵循下列步驟來停用電子郵件驗證：

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 使用上方功能表中的 [**目錄 + 訂**用帳戶] 篩選，選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程]。
1. 選取您要停用電子郵件驗證的使用者流程。 例如， *B2C_1_signinsignup*。
1. 選取 [**頁面配置**]。
1. 選取 [**本機帳戶註冊頁面**]。
1. 在 [**使用者屬性**] 底下，選取 [**電子郵件地址**]。
1. 在 [**需要驗證**] 下拉式選，選取 [**否**]。
1. 選取 [儲存]。 此使用者流程的電子郵件驗證現已停用。

> [!WARNING]
> 在註冊程序期間停用電子郵件驗證可能會導致收到垃圾郵件。 如果您停用預設 Azure AD B2C 提供的電子郵件驗證，建議您執行更換驗證系統。
