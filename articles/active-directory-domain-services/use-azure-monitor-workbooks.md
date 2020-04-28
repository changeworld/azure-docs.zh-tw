---
title: 搭配 Azure AD Domain Services 使用 Azure 監視器活頁簿 |Microsoft Docs
description: 瞭解如何使用 Azure 監視器活頁簿來審查安全性審查，並瞭解 Azure Active Directory Domain Services 受控網域中的問題。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: bdfc7d37d99dc5511f47e33d1848c3f142a9693e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654461"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>使用 Azure 監視器活頁簿審查 Azure AD Domain Services 中的安全性 audit 事件

為了協助您瞭解 Azure Active Directory Domain Services （Azure AD DS）受控網域的狀態，您可以啟用安全性 audit 事件。 然後您可以使用將文字、分析查詢和參數結合成豐富互動式報表的 Azure 監視器活頁簿，來檢查這些安全性 audit 事件。 Azure AD DS 包含適用于安全性總覽和帳戶活動的活頁簿範本，可讓您深入探索 audit 事件和管理您的環境。

本文說明如何使用 Azure 監視器活頁簿來審查 Azure AD DS 中的安全性 audit 事件。

## <a name="before-you-begin"></a>開始之前

若要完成本文，您需要下列資源和許可權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以[建立及設定 Azure Active Directory Domain Services 實例][create-azure-ad-ds-instance]。
* 針對將資料串流至 Log Analytics 工作區的 Azure Active Directory Domain Services 受控網域，啟用了安全性 audit 事件。
    * 如有需要，請[啟用 Azure Active Directory Domain Services 的安全性審核][enable-security-audits]。

## <a name="azure-monitor-workbooks-overview"></a>Azure 監視器活頁簿總覽

當 Azure AD DS 中開啟了安全性 audit 事件時，可能很難分析並找出受控網域中的問題。 Azure 監視器可讓您匯總這些安全性 audit 事件並查詢資料。 有了 Azure 監視器活頁簿，您可以將此資料視覺化，使其更快速且更容易識別問題。

活頁簿範本是策劃的報表，專為多個使用者和小組彈性地重複使用所設計。 當您開啟活頁簿範本時，就會載入來自 Azure 監視器環境的資料。 您可以使用範本，而不會影響組織中的其他使用者，也可以根據範本儲存您自己的活頁簿。

Azure AD DS 包含下列兩個活頁簿範本：

* 安全性總覽報告
* 帳戶活動報告

如需如何編輯及管理活頁簿的詳細資訊，請參閱[Azure 監視器活頁簿總覽](../azure-monitor/platform/workbooks-overview.md)。

## <a name="use-the-security-overview-report-workbook"></a>使用安全性總覽報告活頁簿

為了協助您進一步瞭解使用方式並找出潛在的安全性威脅，安全性總覽報告會摘要說明登入資料，並識別您可能想要檢查的帳戶。 您可以在特定的日期範圍內查看事件，並向下切入特定的登入事件，例如錯誤的密碼嘗試或帳戶已停用的位置。

若要存取 [安全性總覽] 報告的活頁簿範本，請完成下列步驟：

1. 搜尋並選取 Azure 入口網站中的**Azure Active Directory Domain Services** 。
1. 選取您的受控網域，例如*aaddscontoso.com*
1. 從左側功能表中，選擇 [監視] [ **> 活頁簿**]

    ![在 [Azure 入口網站中選取 [活頁簿] 功能表選項](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. 選擇 [**安全性總覽] 報告**。
1. 從活頁簿頂端的下拉式功能表中，選取您的 Azure 訂用帳戶，然後 Azure 監視器工作區]。 選擇**時間範圍**，例如 [*過去7天*]。

    ![在 [Azure 入口網站中選取 [活頁簿] 功能表選項](./media/use-azure-monitor-workbooks/select-query-filters.png)

    **磚視圖**和**圖表視圖**選項也可以變更，以視需要分析和視覺化資料

1. 若要向下切入到特定的事件種類，請選取其中一個登**入結果**卡，例如 [*帳戶鎖定*]，如下列範例所示：

    ![Azure 監視器活頁簿中視覺化的安全性總覽報表資料範例](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 圖表下方 [安全性總覽] 報告的下半部，會細分選取的 [活動類型]。 您可以依照右手邊的使用者名稱進行篩選，如下列範例報告所示：

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>使用帳戶活動報表活頁簿

為了協助您針對特定使用者帳戶的問題進行疑難排解，帳戶活動報告會細分詳細的 audit 事件記錄檔資訊。 您可以在登入期間提供錯誤的使用者名稱或密碼，以及登入嘗試的來源進行檢查。

若要存取 [帳戶活動] 報表的活頁簿範本，請完成下列步驟：

1. 搜尋並選取 Azure 入口網站中的**Azure Active Directory Domain Services** 。
1. 選取您的受控網域，例如*aaddscontoso.com*
1. 從左側功能表中，選擇 [監視] [ **> 活頁簿**]
1. 選擇 [**帳戶活動] 報表**。
1. 從活頁簿頂端的下拉式功能表中，選取您的 Azure 訂用帳戶，然後 Azure 監視器工作區]。 選擇**時間範圍**，例如 [*過去30天*]，然後選取您希望**磚視圖**代表資料的方式。 您可以依**帳戶使用者名稱**進行篩選，例如*felix*，如下列範例報告所示：

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    圖表下方的區域會顯示個別的登入事件，以及活動結果和來源工作站等資訊。 這項資訊可協助您判斷可能造成帳戶鎖定或表示潛在攻擊的重複登入事件來源。

如同安全性總覽報告，您可以向下切入報表頂端的不同磚，視需要將資料視覺化並加以分析。

## <a name="save-and-edit-workbooks"></a>儲存和編輯活頁簿

Azure AD DS 提供的兩個範本活頁簿是開始使用您自己的資料分析的絕佳位置。 如果您需要更細微的資料查詢和調查，可以儲存自己的活頁簿並編輯查詢。

1. 若要儲存其中一個活頁簿範本的複本，請選取 [**編輯] > 另存新檔 > 共用報表**]，然後提供名稱並加以儲存。
1. 從您自己的範本複本中，選取 [**編輯**] 以進入編輯模式。 您可以選擇報表的任何部分旁邊的藍色 [**編輯**] 按鈕，然後加以變更。

Azure 監視器活頁簿中的所有圖表和資料表都是使用 Kusto 查詢所產生。 如需建立您自己查詢的詳細資訊，請參閱[Azure 監視器記錄查詢][azure-monitor-queries]和[Kusto 查詢教學][kusto-queries]課程。

## <a name="next-steps"></a>後續步驟

如果您需要調整密碼和鎖定原則，請參閱[受控網域上的密碼和帳戶鎖定原則][password-policy]。

如有使用者的問題，請瞭解如何針對[帳戶登入問題][troubleshoot-sign-in]或[帳戶鎖定問題][troubleshoot-account-lockout]進行疑難排解。

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
