---
title: 針對在 Azure 入口網站中檢視計費帳戶的問題進行疑難排解
description: 針對您嘗試在 Azure 入口網站中檢視計費帳戶時發生的問題，本文可協助您進行疑難排解。
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f701e41c62336bcd7638360a27a0fb4c3ce3ec7d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686661"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>針對在 Azure 入口網站中檢視計費帳戶的問題進行疑難排解

當您註冊使用 Azure 時，就會建立計費帳戶。 您可使用計費帳戶來管理您的發票、付款及追蹤成本。 您可以擁有多個計費帳戶的存取權。 例如，您可能已針對您的個人用途註冊 Azure。 但也可以透過組織的 Enterprise 合約或 Microsoft 客戶合約獲得 Azure 的存取權。 針對上述每個案例，您會有不同的計費帳戶。 針對您嘗試在 Azure 入口網站中檢視計費帳戶時發生的問題，本文可協助您進行疑難排解。

您可以在 [[Azure 成本管理 + 帳單](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade)] 頁面中檢視計費帳戶。

若要深入了解計費帳戶及識別您的計費帳戶類型，請參閱[在 Azure 入口網站中檢視計費帳戶](view-all-accounts.md)。

如果您在 Azure 入口網站中沒看到您的計費帳戶，請嘗試下列選項：

## <a name="sign-in-to-a-different-tenant"></a>使用不同的租用戶登入

您的計費帳戶會與單一 Azure Active Directory 租用戶相關聯。 如果您登入不正確的租用戶，您將無法在 [成本管理 + 帳單] 頁面上看到您的計費帳戶。 使用下列步驟在 Azure 入口網站中切換至另一個租用戶，並在該租用戶中檢視您的計費帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頁面右上方的設定檔 (電子郵件地址)。
1. 選取 [切換目錄]。  
    ![顯示在入口網站中選取切換目錄的螢幕擷取畫面](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. 從 [所有目錄] 區段底下選取目錄。  
    ![顯示在入口網站中選取目錄的螢幕擷取畫面](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>使用不同的電子郵件地址登入

有些使用者有多個電子郵件地址可登入 [Azure 入口網站](https://portal.azure.com)。 並非所有的電子郵件地址都有計費帳戶的存取權。 如果您使用有權管理資源但無權查看計費帳戶的電子郵件地址來登入，您就不會在 Azure 入口網站的 [[成本管理 + 帳單](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade)] 頁面上看到計費帳戶。

請以有權存取計費帳戶的電子郵件地址登入 Azure 入口網站。

## <a name="sign-in-with-a-different-identity"></a>使用不同的租身分識別登入

有些使用者會有兩個電子郵件地址相同的身分識別 - 公司或學校帳戶及個人帳戶。 一般而言，只有其中一個身分識別有權限可檢視計費帳戶。 您可能有兩個具有單一電子郵件地址的身分識別。 當您使用無權檢視計費帳戶的身分登入時，您就不會在 [[成本管理 + 帳單](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade)] 頁面中看到計費帳戶。 使用下列步驟來切換您的身分識別：

1. 在 InPrivate/Incognito 視窗中，登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的電子郵件地址有兩個身分識別，您會看到可選取個人帳戶或公司/學校帳戶的選項。 選取其中一個帳戶。
1. 如果您在 Azure 入口網站的 [成本管理 + 帳單] 頁面中沒看到計費帳戶，請重複步驟 1 和 2，然後選取其他身分識別。

## <a name="contact-us-for-help"></a>如需協助，請與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

閱讀下列計費和訂用帳戶文章，以協助針對問題進行疑難排解。

- [卡片遭拒](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [訂用帳戶登入問題](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [找不到訂用帳戶](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [已停用企業成本檢視](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
