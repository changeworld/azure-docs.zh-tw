---
title: Azure Active Directory B2C 中的 Multi-Factor Authentication | Microsoft Docs
description: 如何在受 Azure Active Directory B2C 保護的取用者導向應用程式中啟用 Multi-Factor Authentication。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e328caa80a0e63f68f2563bc91a6405341ad064e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102063"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中啟用多重要素驗證 | Microsoft Docs

Azure Active Directory B2C (Azure AD B2C) 直接與 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) 整合，讓您可以在應用程式中新增第二層安全性來註冊和登入體驗。 您不需要撰寫單一行程式碼，即可啟用多重要素驗證。 如果您已經建立註冊和登入使用者流程，仍然可以啟用多重要素驗證。

這項功能可協助應用程式處理案例，例如：

- 您在存取某一個應用程式時不需要多重要素驗證，但在存取另一應用程式時需要它。 例如，客戶可以使用社交或本機帳戶登入汽車保險應用程式，但必須先驗證電話號碼，才能存取同個目錄中註冊的家庭保險應用程式。
- 一般而言，您在存取應用程式時不需要多重要素驗證，但在存取其中的敏感部分資訊時則需要它。 例如，客戶可以使用社交或本機帳戶登入銀行應用程式來檢查帳戶餘額，但必須先完成電話號碼驗證，才能嘗試進行轉帳匯款作業。

## <a name="set-multi-factor-authentication"></a>設定多重要素驗證

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 使用上方功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，來選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程]****。
1. 選取您要啟用 MFA 的使用者流程。 例如， *B2C_1_signinsignup*。
1. 選取 [屬性] 。
1. 在 [ **多重要素驗證** ] 區段中，選取所需的 **mfa 方法**，然後在 [ **MFA 強制** ] 下，選取 [ **永遠開啟**] 或 [ **[條件](conditional-access-user-flow.md) 式 (建議的) **。 針對 [條件]，建立 [條件式存取原則](conditional-access-identity-protection-setup.md) 原則，並指定您想要套用原則的應用程式。 
1. 選取 [儲存]。 此使用者流程現在已啟用 MFA。

您可以使用 [執行使用者流程]**** 來驗證體驗。 請確認下列狀況：

在進行多重要素驗證步驟之前，已在租用戶中建立客戶帳戶。 在步驟執行過程中，系統會要求客戶提供電話號碼進行驗證。 若驗證成功，會將電話號碼附加至帳戶以供之後使用。 即使客戶取消或卸除，下次登入時系統可能會要求客戶再度驗證電話號碼 (已啟用多重要素驗證)。



