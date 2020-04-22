---
title: 使用「我的員工」(預覽版)管理密碼和電話號碼 - Azure AD |微軟文件
description: 通過「我的員工」管理使用者的密碼和電話號碼
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770858"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>將使用者管理委派為「我的員工」(預覽版)

您的組織可以使用 **「我的員工」** 將使用者管理任務委派給授權人員(如商店經理或團隊領導),以説明其員工訪問所需的應用程式。 如果團隊成員由於忘記了密碼而無法訪問應用程式,則工作效率將丟失。 這還會推高支援成本,並導致管理過程中的瓶頸。  使用「我的員工」,無法訪問其帳戶的團隊成員只需單擊幾下即可重新獲得訪問許可權,無需管理員説明。

## <a name="manage-your-staff-in-my-staff"></a>在我的員工中管理您的員工

管理"我的員工"中的團隊成員非常簡單。 要開始,[請轉到"我的員工",](https://aka.ms/mystaff)選擇團隊或位置,然後選擇使用者。 位置的位置和團隊成員由 IT 管理員決定,您無法更改它們。

如果您管理多個位置,則當您轉到"我的員工"時,必須選擇一個位置才能查看分配給該位置的團隊成員。

如果您尚未獲得訪問「我的員工」的足夠許可權,您將看到以下消息「哎呀,此時似乎無權查看我的員工。 有關詳細資訊,請聯繫您的管理員。

### <a name="find-a-staff-member-in-my-staff"></a>在我的員工中查找工作人員

您必須打開工作人員的個人資料,然後才能開始管理他們。

1. [打開「我的員工」](https://aka.ms/mystaff)並根據需要選擇位置。

    ![在「我的員工」中為團隊成員選擇一個位置](media/my-staff-team-manager/allaus.png)

1. 打開團隊成員的個人資料。

    ![在「我的員工」中的位置中選擇一個使用者](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>重設使用者密碼

如果您的組織已授予您許可權,則可以重置員工的密碼。

1. [開啟我的職員](https://aka.ms/mystaff)。
1. 打開員工的個人資料。
1. 選取 [重設密碼]****。

    ![重置「我的員工」中的使用者密碼](media/my-staff-team-manager/resetpassword1.png)

1. 生成或輸入新密碼。 您可能會顯示自動產生的臨時密碼,或者系統可能會要求您輸入使用者的臨時密碼。

    ![在「我的員工」中重置後複製臨時使用者密碼](media/my-staff-team-manager/resetpassword2.png)

重置使用者的密碼後,請為使用者提供臨時密碼。 當使用者使用臨時密碼登錄時,需要更改密碼。

## <a name="manage-a-users-phone-number"></a>管理使用者的電話號碼

如果您的組織已授予您許可權,則可以管理員工的電話號碼。

### <a name="add-a-phone-number"></a>新增電話號碼

1. [開啟我的職員](https://aka.ms/mystaff)。
1. 打開員工的個人資料。
1. 選擇 **「添加電話號碼**」。

    ![在「我的員工」中添加用戶電話號碼](media/my-staff-team-manager/addphone1.png)

1. 添加電話號碼並選擇 **"保存**"。

    ![在「我的員工」中保存添加的使用者電話號碼](media/my-staff-team-manager/addphone2.png)

為用戶註冊電話號碼后,他們可以使用它使用 SMS 登錄、執行兩步驗證或自行重置密碼,具體取決於組織的設置。

![在我的員工中註冊的新電話號碼](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>編輯電話號碼

1. [開啟我的職員](https://aka.ms/mystaff)。
1. 打開員工的個人資料。
1. 選擇 **「編輯電話號碼**」。

    ![從"我的員工"中的使用者配置檔中選擇"編輯"](media/my-staff-team-manager/editphone2.png)

1. 輸入新電話號碼並選擇 **"保存**"。

    ![編輯「我的員工」中的工作人員電話號碼](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>為使用者啟用電話號碼登入

如果在您的組織中啟用了使用電話號碼作為使用者名(SMS登錄)的登錄,則可以將此身份驗證添加到現有用戶電話號碼。

1. [開啟我的職員](https://aka.ms/mystaff)。
1. 打開員工的個人資料。
1. 如果螢幕底部有消息說,使用電話號碼作為使用者名登錄,請選擇"**啟用"** 以開始此過程。 如果使用者已啟用其電話號碼登錄,則會顯示此消息。

    ![在「我的員工」中的位置支援電話登錄時,請參閱該消息](media/my-staff-team-manager/enableforms1.png)

1. 當您完成時，選取 [確定]****。

    ![刪除「我的員工」中的工作人員電話號碼](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>刪除電話號碼

1. [開啟我的職員](https://aka.ms/mystaff)。
1. 打開員工的個人資料。
1. 選擇**刪除電話號碼**。
1. 完成後選擇 **「刪除**」。

    ![刪除「我的員工」中的工作人員電話號碼](media/my-staff-team-manager/deletephone1.png)
