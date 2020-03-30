---
title: 請求訪問包 - Azure AD 授權管理
description: 瞭解如何使用"我的訪問"門戶請求訪問 Azure 活動目錄授權管理中的訪問包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261745"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>請求訪問 Azure AD 授權管理中的訪問包

使用 Azure AD 授權管理，訪問包支援一次性設置資源和策略，從而自動管理訪問包生命週期內的訪問。 

訪問包管理器可以配置策略，以要求使用者訪問包。 需要訪問包的使用者可以提交請求以訪問。 本文介紹如何提交訪問請求。

## <a name="sign-in-to-the-my-access-portal"></a>登錄到"我的訪問"門戶

第一步是登錄到"我的訪問"門戶，您可以在其中請求訪問包。

**先決條件角色：** 請求

1. 查找來自您正在使用的專案或業務經理的電子郵件或消息。 電子郵件應包括指向您需要訪問的訪問包的連結。 連結以 開頭`myaccess`，包括目錄提示，以訪問包 ID 結尾。  （對於美國政府來說，功能變數名稱可能是`https://myaccess.microsoft.us`。
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 打開連結。

1. 登錄到"我的訪問"門戶。

    請務必使用組織（工作或學校）帳戶。 如果您不確定，請諮詢您的專案或業務經理。

## <a name="request-an-access-package"></a>請求訪問包

在"我的訪問"門戶中找到訪問包後，可以提交請求。

**先決條件角色：** 請求

1. 在清單中查找訪問包。  如有必要，可以通過鍵入搜索字串，然後選擇**名稱**、**目錄**或**資源**篩選器進行搜索。

    ![我的訪問門戶 - 資源搜索](./media/entitlement-management-request-access/my-access-resource-search.png)

1. 按一下核取記號以選擇訪問包。

1. 按一下 [要求存取]**** 以開啟 [要求存取] 窗格。

    ![我的存取權入口網站 - 存取套件](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. 如果顯示 **"業務理由"** 框，則鍵入需要訪問的理由。

1. 如果啟用**了特定期間的請求，** 請選擇"**是**"或 **"否**"。

1. 如有必要，指定開始日期和結束日期。

    ![我的存取權入口網站 - 要求存取](./media/entitlement-management-shared/my-access-request-access.png)

1. 完成後，按一下"**提交"** 以提交您的請求。

1. 按一下 **"請求歷史記錄**"以查看請求和狀態的清單。

    如果訪問包需要審批，則請求現在處於掛起的審批狀態。

### <a name="select-a-policy"></a>選取原則

如果請求訪問具有多個應用策略的訪問包，系統可能會要求您選擇策略。 例如，訪問包管理器可能為兩組內部員工配置具有兩群組原則的訪問包。 第一個策略可能允許訪問 60 天，並且需要批准。 第二個策略可能允許訪問 2 天，不需要批准。 如果遇到此方案，則必須選擇要使用的策略。

![我的訪問門戶 - 請求訪問 - 多個策略](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>重新提交請求

當您請求訪問包時，如果核准者不及時回應，您的請求可能會被拒絕，或者您的請求可能會過期。 如果您需要存取權限，可以重試並重新提交請求。 以下過程說明如何重新提交訪問請求：

**先決條件角色：** 請求

1. 登錄到 **"我的訪問"** 門戶。

1. 按一下從左側導航功能表**請求歷史記錄**。

1. 查找您要為其重新提交請求的訪問包。

1. 按一下核取記號以選擇訪問包。

1. 按一下所選訪問包右側的藍色**視圖**連結。
    
    ![選擇訪問包和查看連結](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    右側將打開一個窗格，其中將打開訪問包的請求歷史記錄。
    
    ![選擇重新提交按鈕](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. 按一下窗格底部的 **"重新提交**"按鈕。

## <a name="cancel-a-request"></a>取消請求

如果您提交訪問請求，並且請求仍處於**掛起的審批**狀態，則可以取消該請求。

**先決條件角色：** 請求

1. 在左側的"我的訪問"門戶中，按一下 **"請求歷史記錄**"以查看請求和狀態的清單。

1. 按一下要取消的請求的 **"查看**"連結。

1. 如果請求仍處於**掛起的審批**狀態，則可以按一下 **"取消請求**"以取消請求。

    ![我的訪問門戶 - 取消請求](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. 按一下 **"請求歷史記錄**"以確認請求已取消。

## <a name="next-steps"></a>後續步驟

- [批准或拒絕訪問請求](entitlement-management-request-approve.md)
- [請求流程和電子郵件通知](entitlement-management-process.md)
