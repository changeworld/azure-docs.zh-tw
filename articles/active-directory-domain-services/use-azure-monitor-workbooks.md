---
title: 將 Azure 監視器活頁簿與 Azure AD 域服務一起使用 |微軟文檔
description: 瞭解如何使用 Azure 監視器活頁簿查看安全審核並瞭解 Azure 活動目錄域服務託管域中的問題。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: 4bf6926651a0e2e9289b3c729abea16b1c66bce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108636"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>使用 Azure 監視器活頁簿查看 Azure AD 域服務中的安全審核事件

為了説明您瞭解 Azure 活動目錄域服務 （Azure AD DS） 託管域的狀態，可以啟用安全審核事件。 然後，可以使用 Azure 監視器活頁簿檢查這些安全審核事件，這些活頁簿將文本、分析查詢和參數合併為豐富的互動式報表。 Azure AD DS 包括用於安全概述和帳戶活動的活頁簿範本，這些範本可讓您深入瞭解審核事件並管理環境。

本文介紹如何使用 Azure 監視器活頁簿查看 Azure AD DS 中的安全審核事件。

## <a name="before-you-begin"></a>開始之前

要完成本文，您需要以下資源和特權：

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱，[請創建帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要，請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
* 為 Azure 活動目錄域服務託管域啟用了安全審核事件，這些域將資料流程式傳輸到日誌分析工作區。
    * 如果需要，[請為 Azure 活動目錄域服務啟用安全審核][enable-security-audits]。

## <a name="azure-monitor-workbooks-overview"></a>Azure 監視器活頁簿概述

在 Azure AD DS 中打開安全審核事件時，可能很難分析和識別託管域中的問題。 Azure 監視器允許您聚合這些安全審核事件並查詢資料。 使用 Azure 監視器活頁簿，可以視覺化此資料，以便更快、更輕鬆地識別問題。

活頁簿範本是精心策劃的報表，專為多個使用者和團隊靈活重用而設計。 打開活頁簿範本時，將載入 Azure 監視器環境中的資料。 您可以使用範本而不影響組織中的其他使用者，還可以根據範本保存您自己的活頁簿。

Azure AD DS 包括以下兩個活頁簿範本：

* 安全概述報告
* 帳戶活動報告

有關如何編輯和管理活頁簿的詳細資訊，請參閱[Azure 監視器活頁簿概述](../azure-monitor/platform/workbooks-overview.md)。

## <a name="use-the-security-overview-report-workbook"></a>使用安全概述報告活頁簿

為了説明您更好地瞭解使用方式和識別潛在的安全威脅，安全概述報告匯總了登錄資料並標識了可能需要檢查的帳戶。 您可以查看特定日期範圍內的事件，並深入瞭解特定的登錄事件，例如密碼嘗試錯誤或帳戶被禁用的位置。

要訪問安全概述報告的活頁簿範本，完成以下步驟：

1. 在 Azure 門戶中搜索並選擇**Azure 活動目錄域服務**。
1. 選擇託管域，例如*aaddscontoso.com*
1. 從左側的功能表中選擇 **"監視>活頁簿**

    ![在 Azure 門戶中選擇"活頁簿"功能表選項](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. 選擇**安全概述報告**。
1. 從活頁簿頂部的下拉式功能表中，選擇 Azure 訂閱，然後選擇 Azure 監視器工作區。 選擇**時間範圍**，如最近*7 天*。

    ![在 Azure 門戶中選擇"活頁簿"功能表選項](./media/use-azure-monitor-workbooks/select-query-filters.png)

    也可以更改 **"磁貼視圖**"和 **"圖表"視圖**選項，以便根據需要分析和視覺化資料

1. 要向下切入到特定事件種類，請選擇**登錄結果**卡片之一，如 *"帳戶鎖定"，* 如以下示例所示：

    ![示例安全概述 報表在 Azure 監視器活頁簿中視覺化的資料](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 圖表下方的安全概覽報告的下半部分將細分所選活動類型。 您可以按右側涉及的使用者名進行篩選，如以下示例報告所示：

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>使用帳戶活動報表活頁簿

為了説明您解決特定使用者帳戶的問題，帳戶活動報告會分解詳細的審核事件日誌資訊。 您可以查看登錄期間何時提供了錯誤的使用者名或密碼，以及登錄嘗試的來源。

要存取帳戶活動報表的活頁簿範本，完成以下步驟：

1. 在 Azure 門戶中搜索並選擇**Azure 活動目錄域服務**。
1. 選擇託管域，例如*aaddscontoso.com*
1. 從左側的功能表中選擇 **"監視>活頁簿**
1. 選擇**帳戶活動報告**。
1. 從活頁簿頂部的下拉式功能表中，選擇 Azure 訂閱，然後選擇 Azure 監視器工作區。 選擇**時間範圍**，如最近*30 天*，然後選擇希望**磁貼視圖**如何表示資料。 您可以按**帳戶使用者名**（如*felix*） 進行篩選，如以下示例報告所示：

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    圖表下方的區域顯示單個登錄事件以及活動結果和源工作站等資訊。 此資訊可説明確定可能導致帳戶鎖定或指示潛在攻擊的登錄事件的重複源。

與安全概述報表一樣，您可以向下切入報表頂部的不同磁貼，以便根據需要視覺化和分析資料。

## <a name="save-and-edit-workbooks"></a>保存和編輯活頁簿

Azure AD DS 提供的兩個範本活頁簿是一個從您自己的資料分析開始的好地方。 如果需要在資料查詢和調查中更精細，可以保存自己的活頁簿並編輯查詢。

1. 要保存其中一個活頁簿範本的副本，請選擇 **"編輯>保存為>共用報表**，然後提供名稱並保存它。
1. 從您自己的範本副本中，選擇 **"編輯"** 以進入編輯模式。 您可以選擇報表任何部分旁邊的藍色 **"編輯"** 按鈕並進行更改。

Azure 監視器活頁簿中的所有圖表和表都是使用 Kusto 查詢生成的。 有關創建自己的查詢的詳細資訊，請參閱[Azure 監視器日誌查詢][azure-monitor-queries]和[Kusto 查詢教程][kusto-queries]。

## <a name="next-steps"></a>後續步驟

如果需要調整密碼和鎖定策略，請參閱[託管域上的密碼和帳戶鎖定策略][password-policy]。

對於使用者的問題，瞭解如何解決[帳戶登錄問題][troubleshoot-sign-in]或[帳戶鎖定問題][troubleshoot-account-lockout]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
