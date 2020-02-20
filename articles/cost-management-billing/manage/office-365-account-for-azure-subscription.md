---
title: 使用 Office 365 帳戶註冊 Azure | Microsoft Docs
description: 了解如何使用 Office 365 帳戶建立 Azure 訂用帳戶
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 265d9bcafa7caf9af0f8c4c7f33de044fa02349c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199580"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>使用 Office 365 帳戶註冊 Azure 訂用帳戶
如果您有 Office 365 訂用帳戶，您可以使用 Office 365 帳戶來建立 Azure 訂用帳戶。 使用 Office 365 使用者名稱和密碼來登入 [Azure 入口網站](https://portal.azure.com/)。 如果您想要設定虛擬機器或使用其他 Azure 服務，您必須註冊 Azure 訂用帳戶。 您可以與他人共用您的 Azure 訂用帳戶，並[使用角色型存取控制來管理 Azure 訂用帳戶和資源的存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

如果您已經有 Office 365 帳戶和 Azure 訂用帳戶，請參閱[將 Office 365 租用戶關聯至 Azure 訂用帳戶](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>使用 Office 365 帳戶取得 Azure 訂用帳戶

使用 Office 365 使用者名稱和密碼來註冊 Azure 既省時又可避免帳戶數目不斷增加。

1. 在 [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs) 註冊。
2. 使用 Office 365 使用者名稱和密碼來登入。 您使用的帳戶不需具備系統管理員權限。 如果您有一個以上的 Office 365 帳戶，請務必使用您想要與 Azure 訂用帳戶建立關聯之 Office 365 帳戶的認證。

   ![顯示登入頁面的螢幕擷取畫面。](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. 輸入必要資訊並完成註冊程序。 如果您已經有 Office 365 帳戶，可能就不必輸入某些資訊。

    ![顯示註冊表單的螢幕擷取畫面。](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- 如果您需要將組織中的其他人新增至 Azure 訂用帳戶，請參閱[開始在 Azure 入口網站中進行存取管理](../../role-based-access-control/overview.md)。

## <a id="more-about-subs">深入了解 Azure 和 Office 365 訂用帳戶</a>
Office 365 和 Azure 均使用 Azure AD 服務來管理使用者和訂用帳戶。 Azure 目錄就像您可在其中將使用者和訂用帳戶分組的容器。 若要將相同的使用者帳戶用於 Azure 和 Office 365 訂用帳戶，您需要確定 Azure 訂用帳戶是建立在和 Office 365 訂用帳戶相同的目錄中。 請記住下列幾點：

* 訂用帳戶會建立在目錄之下
* 使用者屬於目錄
* 訂用帳戶會落在建立訂用帳戶之使用者的目錄中。 所以 Office 365 訂用帳戶會繫結至與 Azure 訂用帳戶相同的帳戶。
* Azure 訂用帳戶是由目錄中的個別使用者所擁有
* Office 365 訂用帳戶是由目錄本身所擁有。 在目錄中擁有適當權限的使用者可以管理這些訂用帳戶。

![顯示目錄、使用者和訂用帳戶關聯性的螢幕擷取畫面。](./media/office-365-account-for-azure-subscription/19-background-information.png)

如需詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
