---
title: 使用我的員工管理密碼和電話號碼 (預覽) - Azure AD | Microsoft Docs
description: 使用 [我的員工] 管理使用者的密碼和電話號碼
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: end-user-help
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: d692302df1f5c03a7dce60858b31e0ca66de4c80
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744545"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>使用 [我的員工] 來委派使用者管理 (預覽)

貴組織可以使用 [我的員工] 將使用者管理工作委派給授權單位 (例如商店經理或小組領導人)，以協助其員工成員存取所需的應用程式。 如果您的小組成員因為忘記密碼而無法存取應用程式，則會遺失生產力。 這也會提高支援成本，並在系統管理程序中造成瓶頸。  使用 [我的員工]，無法存取其帳戶的小組成員只需按幾下滑鼠就能重新取得存取權，不需要系統管理員的協助。

## <a name="manage-your-staff-in-my-staff"></a>在 [我的員工] 中管理您的員工

在 [我的員工] 中管理您的小組成員非常簡單。 若要開始，請[移至 [我的員工]](https://aka.ms/mystaff)、選取小組或位置，然後選取使用者。 位置和位置中的小組成員是由您的 IT 系統管理員決定，而且您無法加以變更。

如果您管理一個以上的位置，當您移至 [我的員工] 時，您必須選取位置，才能看到指派給該位置的小組成員。

如果您還沒有足夠的權限可以存取 [我的員工]，您會看到下列訊息：「糟糕，您目前未獲授權可以看到 [我的員工]。 如需詳細資訊，請連絡您的系統管理員」。

### <a name="find-a-staff-member-in-my-staff"></a>在 [我的員工] 中尋找員工成員

您必須先開啟員工成員的設定檔，才能開始管理。

1. [開啟 [我的員工]](https://aka.ms/mystaff)，並視需要選取位置。

    ![在 [我的員工] 中選取小組成員的位置](media/my-staff-team-manager/allaus.png)

1. 開啟小組成員的設定檔。

    ![在 [我的員工] 中選取某個位置的其中一個使用者](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>重設使用者密碼

如果貴組織已授與您權限，您可以為員工成員重設密碼。

1. [開啟 [我的員工]](https://aka.ms/mystaff)。
1. 開啟員工成員的設定檔。
1. 選取 [重設密碼]。

    ![在 [我的員工] 中重設使用者密碼](media/my-staff-team-manager/resetpassword1.png)

1. 產生或輸入新的密碼。 您可能會看到自動產生的暫時密碼，或者系統可能會要求您輸入使用者的暫時密碼。

    ![在 [我的員工] 中重設之後複製暫時使用者密碼](media/my-staff-team-manager/resetpassword2.png)

在您重設使用者的密碼之後，請為使用者提供暫時密碼。 當使用者以其暫時密碼登入時，必須加以變更。

## <a name="manage-a-users-phone-number"></a>管理使用者的電話號碼

如果貴組織已授與您權限，您可以為員工成員管理電話號碼。

### <a name="add-a-phone-number"></a>新增電話號碼

1. [開啟 [我的員工]](https://aka.ms/mystaff)。
1. 開啟員工成員的設定檔。
1. 選取 [新增電話號碼]。

    ![在 [我的員工] 中新增使用者電話號碼](media/my-staff-team-manager/addphone1.png)

1. 新增電話號碼，然後選取 [儲存]。

    ![在 [我的員工] 中儲存新增的使用者電話號碼](media/my-staff-team-manager/addphone2.png)

註冊使用者的電話號碼之後，他們就可以使用該電話號碼來以文字簡訊登入、執行雙步驟驗證，或自行重設其密碼，視貴組織的設定而定。

![使用 [我的員工] 註冊的新電話號碼](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>編輯電話號碼

1. [開啟 [我的員工]](https://aka.ms/mystaff)。
1. 開啟員工成員的設定檔。
1. 選取 [編輯電話號碼]。

    ![從 [我的員工] 中的使用者設定檔選取 [編輯]](media/my-staff-team-manager/editphone2.png)

1. 輸入新的電話號碼，然後選取 [儲存]。

    ![在 [我的員工] 中編輯員工成員電話號碼](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>為使用者啟用電話號碼登入

如果貴組織中已啟用使用電話號碼作為使用者名稱 (文字簡訊登入) 的登入，您可以將此驗證新增至現有的使用者電話號碼。

1. [開啟 [我的員工]](https://aka.ms/mystaff)。
1. 開啟員工成員的設定檔。
1. 如果畫面底部出現一則訊息，指出您可以使用您的電話號碼作為使用者名稱登入，請選取 [啟用] 以開始程序。 如果使用者已啟用使用其電話號碼登入，就會出現此訊息。

    ![當 [我的員工] 中的位置支援電話登入時，就會看見此訊息](media/my-staff-team-manager/enableforms1.png)

1. 完成後，選取 [確定]。

    ![在 [我的員工] 中移除員工成員電話號碼](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>移除電話號碼

1. [開啟 [我的員工]](https://aka.ms/mystaff)。
1. 開啟員工成員的設定檔。
1. 選取 [移除電話號碼]。
1. 完成後，選取 [刪除]。

    ![在 [我的員工] 中移除員工成員電話號碼](media/my-staff-team-manager/deletephone1.png)
