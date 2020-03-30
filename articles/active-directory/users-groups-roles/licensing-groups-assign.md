---
title: 將授權指派給群組 - Azure Active Directory | Microsoft Docs
description: 如何使用 Azure Active Directory 群組授權的方式將授權指派給使用者
services: active-directory
keywords: Azure AD 授權
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253061"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>依據 Azure Active Directory 中的群組成員資格將授權指派給使用者

本文將引導您向一組使用者分配產品許可證，並驗證他們在 Azure 活動目錄 （Azure AD） 中是否獲得了正確的許可。

在本範例中，租用戶包含名為「人力資源部門」**** 的安全性群組。 這個群組包括人力資源部門的所有成員 (大約是 1,000 位使用者)。 您想要將 Office 365 Enterprise E3 授權指派給整個部門。 產品中包含的 Yammer Enterprise 服務需要暫時停用，直到部門準備好要開始使用它為止。 您也要將 Enterprise Mobility + Security 授權部署到相同群組的使用者。

> [!NOTE]
> 並非所有位置都可使用某些 Microsoft 服務。 系統管理員必須指定使用者的使用位置屬性，才能將授權指派給使用者。
>
> 如果是群組授權指派，未指定使用位置的任何使用者會繼承目錄的位置。 如果您在多個位置擁有使用者，我們建議您一律將使用位置設為 Azure AD 中使用者建立流程的一部分 (例如，透過 AAD Connect 設定) - 這可確保授權指派的結果一律是正確的，且使用者不會在不允許的位置收到服務。

## <a name="step-1-assign-the-required-licenses"></a>步驟 1︰指派所需的授權

1. 使用許可證管理員帳戶登錄到[**Azure AD 管理中心**](https://aad.portal.azure.com)。 要管理許可證，帳戶必須是授權管理員、使用者管理員或全域管理員。

1. 選擇 **"許可證"** 可打開一個頁面，您可以在其中查看和管理租戶中的所有可許可產品。

1. **在"所有產品**"下，通過選擇產品名稱，同時選擇 Office 365 企業版 E5 和企業移動性 + 安全 E3。 要開始分配，請選擇頁面頂部的 **"分配**"。

   ![選擇要分配許可證的產品](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. 在"**分配許可證"** 頁上，選擇 **"使用者和組**"以打開使用者和組的清單。

1. 選擇使用者或組，然後使用頁面底部的 **"選擇"** 按鈕確認您的選擇。

1. 在 **"分配許可證**"頁上，按一下 **"分配"選項**，該選項顯示我們以前選擇的兩個產品中包含的所有服務方案。 查找**Yammer 企業，** 並將其**關閉**以從產品許可證禁用該服務。 通過按一下 **"許可證"選項**底部的 **"確定"** 進行確認。

   ![為許可證選擇服務方案](./media/licensing-groups-assign/assignment-options.png)
  
1. 要完成分配，請在"**分配許可證"** 頁上按一下頁面底部的 **"分配**"。

1. 右上角顯示的通知會顯示程序的狀態和結果。 如果無法完成指派給群組 (例如，因為群組中已存在的授權)，按一下通知以檢視失敗的詳細資料。

將許可證分配給組時，Azure AD 將處理該組的所有現有成員。 此過程可能需要一些時間，隨組大小而變化。 下一個步驟將說明如何確認處理程序已完成，並且決定是否需要進一步注意以解決問題。

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>步驟 2︰確認已完成初始指派

1. 轉到**Azure 活動目錄** > **組**。 選擇分配給許可證的組。

1. 在"組"頁上，選擇 **"許可證**"。 可讓您快速確認授權是否已完全指派給使用者，以及是否有任何錯誤需要探究。 可用資訊如下：

   - 當前分配給組的服務許可證。 選取項目來顯示已啟用且要進行變更的特定服務。

   - 最新許可證更改的狀態更新，如果正在處理更改或所有使用者成員的處理已完成，這些更改可用。

   - 有關處於錯誤狀態的使用授權分配的資訊。

   ![許可錯誤和許可證狀態](./media/licensing-groups-assign/assignment-errors.png)

1. 請參閱**Azure 活動目錄** > **使用者和組** > *名稱* > **稽核記錄**下有關許可證處理的詳細資訊。 檢查以下活動：

   - 活動： `Start applying group based license to users`. 當我們的系統拾取群組的授權指派變更，並開始將其套用到所有使用者成員時，就會進行記錄。 它包含已進行之變更的相關資訊。

   - 活動： `Finish applying group based license to users`. 當系統完成處理群組中的所有使用者時，就會進行記錄。 它包含已成功處理的使用者人數和無法獲得群組授權指派的使用者人數之摘要。

   [閱讀本節](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity)以深入了解如何使用稽核記錄，以分析群組型授權所做的變更。

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>步驟 3︰檢查授權問題及解決這些問題

1. 轉到**Azure 活動目錄** > **組**，並查找許可證分配給的組。
1. 在"組"頁上，選擇 **"許可證**"。 頁面頂部的通知顯示有 10 個使用者無法分配許可證。 打開它以查看此組處於許可錯誤狀態的所有使用者的清單。
1. [失敗的指派]**** 資料行告訴我們，兩個產品授權都無法指派給使用者。 [失敗的前幾大原因]**** 資料行包含失敗的原因。 在此案例中為 [衝突的服務方案]****。

   ![無法分配的許可證](./media/licensing-groups-assign/failed-assignments.png)

1. 選擇使用者以打開使用者的**許可證**頁面。 此頁顯示當前分配給使用者的所有許可證。 在此範例中，使用者擁有繼承自 **Kiosk 使用者** 群組的 Office 365 Enterprise E1 授權。 這會與系統嘗試從**人力資源部門**群組套用的 E3 授權衝突。 如此一來，該群組沒有任何授權會指派給使用者。

   ![查看使用者的所有許可證衝突](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. 若要解決這個衝突，從 **Kiosk 使用者**群組移除使用者。 在 Azure AD 處理變更之後，會正確指派**人力資源部門**授權。

## <a name="next-steps"></a>後續步驟

要瞭解有關使用組分配許可證的功能集的更多情況，請參閱以下文章：

- [什麼是 Azure Active Directory 中以群組為基礎的授權？](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [識別及解決 Azure Active Directory 中群組的授權問題](licensing-groups-resolve-problems.md)
- [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](licensing-groups-migrate-users.md)
- [如何使用 Azure Active Directory 中的群組型授權在產品授權之間移轉使用者](licensing-groups-change-licenses.md)
- [Azure Active Directory 群組型授權其他案例 (英文)](../active-directory-licensing-group-advanced.md)
- [Azure Active Directory 群組型授權的 PowerShell 範例](licensing-ps-examples.md)
