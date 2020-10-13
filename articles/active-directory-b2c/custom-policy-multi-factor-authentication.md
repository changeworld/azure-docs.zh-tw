---
title: Azure Active Directory B2C 中的 Multi-Factor Authentication | Microsoft Docs
description: 如何在受 Azure Active Directory B2C 保護的取用者導向應用程式中啟用 Multi-Factor Authentication。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 69096e5f650a131c5af7ec4da60b7cbca225a56f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87116611"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中啟用多重要素驗證 | Microsoft Docs

Azure Active Directory B2C (Azure AD B2C) 直接與 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) 整合，讓您可以在應用程式中新增第二層安全性來註冊和登入體驗。 您不需要撰寫單一行程式碼，即可啟用多重要素驗證。 如果您已經建立註冊和登入使用者流程，仍然可以啟用多重要素驗證。

此功能可協助應用程式處理諸如下列的各種案例：

- 您在存取某一個應用程式時不需要多重要素驗證，但在存取另一應用程式時需要它。 例如，客戶可以使用社交或本機帳戶登入汽車保險應用程式，但必須先驗證電話號碼，才能存取同個目錄中註冊的家庭保險應用程式。
- 一般而言，您在存取應用程式時不需要多重要素驗證，但在存取其中的敏感部分資訊時則需要它。 例如，客戶可以使用社交或本機帳戶登入銀行應用程式來檢查帳戶餘額，但必須先完成電話號碼驗證，才能嘗試進行轉帳匯款作業。

## <a name="set-multi-factor-authentication"></a>設定多重要素驗證

當您建立使用者流程時，可以選擇啟用多重要素驗證。

![設定多重要素驗證](./media/custom-policy-multi-factor-authentication/add-policy.png)

將 [多重要素驗證]**** 設定為 [已啟用]****。

您可以使用 [執行使用者流程]**** 來驗證體驗。 請確認下列狀況：

在進行多重要素驗證步驟之前，已在租用戶中建立客戶帳戶。 在步驟執行過程中，系統會要求客戶提供電話號碼進行驗證。 若驗證成功，會將電話號碼附加至帳戶以供之後使用。 即使客戶取消或卸除，下次登入時系統可能會要求客戶再度驗證電話號碼 (已啟用多重要素驗證)。

## <a name="add-multi-factor-authentication"></a>新增多重要素驗證

您可以在先前建立的使用者流程上啟用多重要素驗證。

啟用多重要素驗證：

1. 選取使用者流程，然後選取 [屬性]****。
2. 選取 [多重要素驗證]**** 旁邊的 [已啟用]****。
3. 按一下頁面頂端的 [儲存]  。


