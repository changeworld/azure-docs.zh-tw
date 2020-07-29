---
title: 要求存取套件-Azure AD 的權利管理
description: 瞭解如何在 Azure Active Directory 的權利管理中，使用我的存取權入口網站來要求存取套件的存取權。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e744adeae55252ec70c8f766db1e29a3898886c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078107"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>要求存取 Azure AD 權利管理中的存取套件

透過 Azure AD 的權利管理，存取套件可讓您一次性設定資源和原則，以自動管理存取套件生命週期的存取權。 

存取套件管理員可以設定原則，要求使用者必須核准才能存取封裝。 需要存取權套件的使用者可以提交要求以取得存取權。 本文說明如何提交存取要求。

## <a name="sign-in-to-the-my-access-portal"></a>登入我的存取權入口網站

第一個步驟是登入我的存取權入口網站，您可以在其中要求存取權套件的存取權。

**先決條件角色：** 申請者

1. 從您正在使用的專案或商務經理尋找電子郵件或訊息。 電子郵件應該包含您需要存取的存取套件連結。 此連結會以開頭 `myaccess` ，包括目錄提示，並以存取套件識別碼結尾。  （對於美國政府而言，網域可能 `https://myaccess.microsoft.us` 改為）。
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 開啟連結。

1. 登入我的存取權入口網站。

    請務必使用您的組織（公司或學校）帳戶。 如果您不確定，請洽詢您的專案或商務經理。

## <a name="request-an-access-package"></a>要求存取套件

在我的存取權入口網站中找到存取套件之後，您就可以提交要求。

**先決條件角色：** 申請者

1. 在清單中尋找存取封裝。  如有需要，您可以輸入搜尋字串，然後選取 [**名稱**]、[**目錄**] 或 [**資源**] 篩選來進行搜尋。

    ![我的存取權入口網站-資源搜尋](./media/entitlement-management-request-access/my-access-resource-search.png)

1. 按一下核取記號以選取存取封裝。

1. 按一下 [要求存取]  以開啟 [要求存取] 窗格。

    ![我的存取權入口網站 - 存取套件](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. 如果顯示 [**商業理由**] 方塊，請輸入需要存取的理由。

1. 如果已啟用 [**特定期間的要求？** ]，請選取 **[是] 或 [** **否**]。

1. 如有必要，請指定開始日期和結束日期。

    ![我的存取權入口網站 - 要求存取](./media/entitlement-management-shared/my-access-request-access.png)

1. 完成後，按一下 [**提交**] 以提交您的要求。

1. 按一下 [**要求歷程記錄**] 以查看您的要求清單和狀態。

    如果存取套件需要核准，則要求現在會處於擱置中的核准狀態。

### <a name="select-a-policy"></a>選取原則

如果您要求存取已套用多項原則的存取封裝，可能會要求您選取原則。 例如，存取套件管理員可能會為兩個內部員工群組設定具有兩個原則的存取封裝。 第一個原則可能會允許存取60天，而且需要核准。 第二個原則可能允許存取2天，而不需要核准。 如果您遇到這種情況，您必須選取您想要使用的原則。

![我的存取權入口網站-要求存取-多個原則](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>重新提交要求

當您要求存取封裝的存取權時，您的要求可能會遭到拒絕，或者當核准者沒有時間回應時，您的要求可能會過期。 如果您需要存取權，您可以再試一次，然後重新提交您的要求。 下列程式說明如何重新提交存取要求：

**先決條件角色：** 申請者

1. 登入**我的存取權**入口網站。

1. 按一下左側導覽功能表中的 [**要求歷程記錄**]。

1. 尋找您要重新提交要求的存取套件。

1. 按一下核取記號以選取存取封裝。

1. 按一下所選存取套件右邊的藍色 [**視圖**] 連結。
    
    ![選取存取套件和視圖連結](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    右側會開啟一個窗格，其中包含存取封裝的要求歷程記錄。
    
    ![選取 [重新提交] 按鈕](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. 按一下窗格底部的 [**重新提交**] 按鈕。

## <a name="cancel-a-request"></a>取消要求

如果您提交存取要求，而要求仍在**等待核准**狀態，您可以取消要求。

**先決條件角色：** 申請者

1. 在我的存取權入口網站中，按一下左側的 [**要求歷程記錄**]，以查看您的要求清單和狀態。

1. 針對您想要取消的要求，按一下 [ **View** ] 連結。

1. 如果要求仍在**等待核准**狀態，您可以按一下 [**取消要求**] 來取消要求。

    ![我的存取權入口網站-取消要求](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 按一下 [**要求歷程記錄**] 確認已取消要求。

## <a name="next-steps"></a>後續步驟

- [核准或拒絕存取要求](entitlement-management-request-approve.md)
- [要求處理和電子郵件通知](entitlement-management-process.md)
