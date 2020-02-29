---
title: 設定權杖-Azure Active Directory B2C |Microsoft Docs
description: 瞭解如何在 Azure Active Directory B2C 中設定權杖存留期和相容性設定。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189612"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定權杖

在本文中，您將瞭解如何在 Azure Active Directory B2C （Azure AD B2C）中設定[權杖的存留期和相容性](tokens-overview.md)。

## <a name="prerequisites"></a>必要條件

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。

## <a name="configure-token-lifetime"></a>設定權杖存留期

您可以在任何使用者流程上設定權杖存留期。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 **[使用者流程（原則）** ]。
5. 開啟您先前建立的使用者流程。
6. 選取 [內容]。
7. 在 [**權杖存留期**] 底下，調整下列屬性以符合應用程式的需求：

    ![Azure 入口網站中的權杖存留期屬性設定](./media/configure-tokens/token-lifetime.png)

8. Haga clic en **Guardar**.

## <a name="configure-token-compatibility"></a>設定權杖相容性

1. 選取 **[使用者流程（原則）** ]。
2. 開啟您先前建立的使用者流程。
3. 選取 [內容]。
4. 在 [**權杖相容性設定**] 底下，調整下列屬性以符合應用程式的需求：

    ![Azure 入口網站中的權杖相容性屬性設定](./media/configure-tokens/token-compatibility.png)

5. Haga clic en **Guardar**.

## <a name="next-steps"></a>後續步驟

深入了解如何[使用存取權杖](access-tokens.md)。



