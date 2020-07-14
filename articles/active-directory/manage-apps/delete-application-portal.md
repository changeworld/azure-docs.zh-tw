---
title: 快速入門：從您的 Azure Active Directory (Azure AD) 租用戶中刪除應用程式
description: 本快速入門將使用 Azure 入口網站來刪除 Azure Active Directory (Azure AD) 租用戶中的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955993"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>快速入門：從您的 Azure Active Directory (Azure AD) 租用戶中刪除應用程式

本快速入門會使用 Azure 入口網站來刪除已新增至您 Azure AD 租用戶的應用程式。

## <a name="prerequisites"></a>必要條件

若要從您的 Azure AD 租用戶中刪除應用程式，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- (選擇性：完成[檢視您的應用程式](view-applications-portal.md))。
- (選擇性：完成[新增應用程式](add-application-portal.md))。
- (選擇性：完成[設定應用程式](add-application-portal-configure.md))。
- (選擇性：完成[設定單一登入](add-application-portal-setup-sso.md))。

>[!IMPORTANT]
>若要測試本教學課程中的步驟，我們建議您使用非實際執行環境。

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>從 Azure AD 租用戶中刪除應用程式

若要從您的 Azure AD 租用戶中刪除應用程式：

1. 在 Azure AD 入口網站中選取 [企業應用程式]，然後尋找並選取您要刪除的應用程式。 在此案例中，我們會刪除上一個快速入門中新增的 GitHub_test 應用程式。
2. 在導覽的 [管理] 區段中，選取 [屬性]。
3. 選取 [刪除]，然後選取 [是]，確認您要從 Azure AD 租用戶中刪除應用程式。


## <a name="next-steps"></a>後續步驟

- [應用程式管理的最佳做法](application-management-fundamentals.md)
- [應用程式管理常見案例](common-scenarios.md)
- [應用程式管理可見性和控制](cloud-app-security.md)