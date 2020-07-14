---
title: 針對 Azure 訂用帳戶登入問題進行疑難排解
description: 協助解決無法登入 Azure 入口網站或 Azure 帳戶中心的問題。
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: e424a3d0e46f0dc154809eba501d2339bc94b1f5
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85806698"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>針對 Azure 訂用帳戶登入問題進行疑難排解

本指南可協助解決無法登入 Azure 入口網站或 Azure 帳戶中心的問題。

> [!NOTE]
> 如果您在註冊新的 Azure 帳戶時遇到問題，請參閱[針對 Azure 訂用帳戶註冊問題進行疑難排解](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)。

## <a name="page-hangs-in-the-loading-status"></a>頁面在載入狀態停止回應

如果您的網際網路瀏覽器頁面停滯，請嘗試下列每個步驟，直到您可以進入 Azure 入口網站為止。

- 重新整理頁面。
- 使用另一個網際網路瀏覽器。
- 使用您瀏覽器的私密瀏覽模式：

   - **Edge：** 開啟 [設定] (設定檔圖片旁的三個點)，選取 [新增 InPrivate 視窗]，然後瀏覽並登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.azure.com/Subscriptions)。 
   - **Chrome：** 選擇 **Incognito** 模式。
   - **Safari：** 選擇 [檔案]，然後選取 [新增私人視窗]。

- 清除快取並刪除網際網路 Cookie：

   - **Edge：** 開啟 [設定]，然後選取 [隱私權和服務]。 依照 [清除瀏覽資料] 底下的步驟進行。 確認已選取 [瀏覽記錄]、[下載記錄] 及 [快取的影像和檔案] 核取方塊，然後選取 [刪除]。
   - **Chrome：** 選擇 [設定] 並選取 [隱私權和安全性] 底下的 [清除瀏覽資料]。

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>自動將您登入為不同的使用者

如果您在網際網路瀏覽器中使用多個使用者帳戶，就可能會發生此問題。

若要解決此問題，請嘗試下列其中一個方法：

- 清除快取並刪除網際網路 Cookie。

   - **Edge：** 開啟 [設定]，然後選取 [隱私權和服務]。 依照 [清除瀏覽資料] 底下的步驟進行。 確認已選取 [瀏覽記錄]、[下載記錄]、[Cookie] 和 [快取的影像和檔案] 核取方塊，然後選取 [刪除]。
   - **Chrome：** 選擇 [設定] 並選取 [隱私權和安全性] 底下的 [清除瀏覽資料]。
- 將您的瀏覽器設定重設為預設值。
- 使用您瀏覽器的私密瀏覽模式。 
   - **Edge：** 開啟 [設定] (設定檔圖片旁的三個點)，選取 [新增 InPrivate 視窗]，然後瀏覽並登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.azure.com/Subscriptions)。 
   - **Chrome：** 選擇 **Incognito** 模式。
   - **Safari：** 選擇 [檔案]，然後選取 [新增私人視窗]。

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>我可以登入，但我看到「找不到訂用帳戶」的錯誤

如果您選取錯誤的目錄或帳戶沒有足夠的權限，就會發生此問題。

**案例 1：** 您在登入 [Azure 入口網站](https://portal.azure.com/)時收到錯誤訊息

若要修正此問題：

- 選取右上角的帳戶，確定您已選取正確的 Azure 目錄。
- 若已選取正確的 Azure 目錄，但您仍收到錯誤訊息，請[將您的帳戶新增為擁有者](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator)。

**案例 2：** 您在登入 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)時收到錯誤訊息

請檢查您所使用的帳戶是否為帳戶管理員。 若要確認誰是帳戶管理員，請依照下列步驟操作︰

1.  登入 [Azure 入口網站中的訂用帳戶檢視](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1.  選取您想要檢查的訂用帳戶，然後選取 [設定] 。
1.  選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。

## <a name="additional-help-resources"></a>其他說明資源

Azure 計費和訂用帳戶的其他疑難排解文章

- [卡片遭拒](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [訂用帳戶註冊問題](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [找不到訂用帳戶](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [已停用企業成本檢視](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Azure 計費文件](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>如需協助，請與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
