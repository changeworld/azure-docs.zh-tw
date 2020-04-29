---
title: 使用我的員工管理密碼和電話號碼（預覽）-Azure AD |Microsoft Docs
description: 使用我的員工管理使用者的密碼和電話號碼
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 29c27c876e90bce6a38226f68adf44a26465cfea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770858"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>使用我的員工來委派使用者管理（預覽）

您的組織可以使用「**我的員工**」將使用者管理工作委派給授權單位（例如商店經理或小組領導人），以協助員工成員存取所需的應用程式。 如果您的小組成員因為忘記密碼而無法存取應用程式，則會遺失生產力。 這也會提高支援成本，並在系統管理程式中造成瓶頸。  在我的員工中，無法存取其帳戶的小組成員只需按幾下滑鼠就能重新取得存取權，而不需要系統管理員的協助。

## <a name="manage-your-staff-in-my-staff"></a>在我的員工中管理您的員工

在我的員工中管理您的小組成員非常簡單。 若要開始，請[移至 [我的員工](https://aka.ms/mystaff)]，選取一個小組或位置，然後選取一個使用者。 位置和小組成員會由您的 IT 系統管理員決定，而且您無法加以變更。

如果您管理一個以上的位置，當您移至 [我的員工] 時，您必須選取 [位置]，才能看到指派給該位置的小組成員。

如果您還沒有足夠的許可權可以存取「我的員工」，您會看到下列訊息：「糟糕，您目前未獲授權可以看到我的員工。 如需詳細資訊，請洽詢您的系統管理員。」

### <a name="find-a-staff-member-in-my-staff"></a>在我的員工中尋找員工成員

您必須先開啟員工成員的設定檔，才能開始管理。

1. [開啟 [我的員工](https://aka.ms/mystaff)]，如有需要，請選取一個位置。

    ![在 [我的員工] 中選取小組成員的位置](media/my-staff-team-manager/allaus.png)

1. 開啟小組成員的設定檔。

    ![在 [我的員工] 中選取某個位置的其中一個使用者](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>重設使用者密碼

如果您的組織已授與您許可權，您可以為員工成員重設密碼。

1. [開啟 [我的員工](https://aka.ms/mystaff)]。
1. 開啟員工成員的設定檔。
1. 選取 [重設密碼]****。

    ![在 [我的員工] 中重設使用者密碼](media/my-staff-team-manager/resetpassword1.png)

1. 產生或輸入新的密碼。 您可能會看到自動產生的暫時密碼，或者系統可能會要求您輸入使用者的暫時密碼。

    ![在 [我的員工] 重設之後複製暫時使用者密碼](media/my-staff-team-manager/resetpassword2.png)

在您重設使用者的密碼之後，請為使用者提供暫時密碼。 當使用者以其暫時密碼登入時，必須加以變更。

## <a name="manage-a-users-phone-number"></a>管理使用者的電話號碼

如果您的組織已授與您許可權，您可以管理您的員工成員的電話號碼。

### <a name="add-a-phone-number"></a>新增電話號碼

1. [開啟 [我的員工](https://aka.ms/mystaff)]。
1. 開啟員工成員的設定檔。
1. 選取 [**新增電話號碼**]。

    ![在 [我的員工] 中新增使用者電話號碼](media/my-staff-team-manager/addphone1.png)

1. 新增電話號碼，然後選取 [**儲存**]。

    ![在我的員工中儲存新增的使用者電話號碼](media/my-staff-team-manager/addphone2.png)

註冊使用者的電話號碼之後，他們就可以使用它來登入 SMS、執行雙步驟驗證，或自行重設其密碼，視您組織的設定而定。

![向我的員工註冊的新電話號碼](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>編輯電話號碼

1. [開啟 [我的員工](https://aka.ms/mystaff)]。
1. 開啟員工成員的設定檔。
1. 選取 [**編輯電話號碼**]。

    ![從 [我的員工] 中的使用者設定檔選取 [編輯]](media/my-staff-team-manager/editphone2.png)

1. 輸入新的電話號碼，然後選取 [**儲存**]。

    ![編輯「我的員工」中的員工成員電話號碼](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>啟用使用者的電話號碼登入

如果您的組織中已啟用使用電話號碼做為使用者名稱（SMS 登入）的登入，您可以將此驗證新增至現有的使用者電話號碼。

1. [開啟 [我的員工](https://aka.ms/mystaff)]。
1. 開啟員工成員的設定檔。
1. 如果畫面底部出現一則訊息，指出以您的電話號碼作為使用者名稱登入，請選取 [**啟用**] 以開始處理常式。 如果使用者已啟用使用其電話號碼登入，就會出現此訊息。

    ![當「我的員工」中的位置支援電話登入時，請參閱訊息](media/my-staff-team-manager/enableforms1.png)

1. 當您完成時，選取 [確定]****。

    ![在 [我的員工] 中移除員工成員電話號碼](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>移除電話號碼

1. [開啟 [我的員工](https://aka.ms/mystaff)]。
1. 開啟員工成員的設定檔。
1. 選取 [**移除電話號碼**]。
1. 當您完成時，請選取 [**刪除**]。

    ![在 [我的員工] 中移除員工成員電話號碼](media/my-staff-team-manager/deletephone1.png)
