---
title: 配置安全性以授予資料訪問 - Azure 時間序列見解預覽 |微軟文檔
description: 瞭解如何在 Azure 時間序列預覽環境中配置安全性、許可權和管理資料訪問策略。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254348"
---
# <a name="grant-data-access-to-an-environment"></a>授與環境的資料存取權

本文討論兩種 Azure 時間序列深入解析預覽存取原則。

> [!TIP]
> 讀取 Azure 活動目錄應用註冊步驟的[身份驗證和授權](time-series-insights-authentication-and-authorization.md)。

## <a name="sign-in-to-time-series-insights"></a>登錄時間序列見解

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 找到您的時間序列深入解析環境。 在 [搜尋]**** 方塊中，輸入 `Time Series`。 選取搜尋結果中的 [時間序列環境]****。
1. 從清單中選取 Time Series Insights 環境。

## <a name="grant-data-access"></a>授與資料存取

依照下列步驟來授與使用者主體的資料存取權。

1. 選取 [資料存取原則]****，然後選取 [+新增]****。

    [![選擇並添加資料訪問策略](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. 選擇 [選取使用者]****。 搜尋使用者名稱或電子郵件地址，以找出您想要新增的使用者。 **選擇"選擇"** 以確認所選內容。

    [![選擇要添加的使用者](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. 選擇 [選取角色]****。 為使用者選擇適當的存取角色：

    * 如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]****。

    * 否則，選取 [讀取者]****，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。

   選取 [確定]**** 以確認角色選擇。

    [![確認所選角色](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. 在 [選取使用者角色]**** 頁面中選取 [確定]****。

    [![在"選擇使用者角色"頁上選擇"確定"](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. 確認 [資料存取原則]**** 頁面列出的使用者和每個使用者的角色。

    [![驗證正確的使用者和角色](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>提供來自其他 Azure AD 租戶的來賓存取

該`Guest`角色不是管理角色。 這個詞彙指的是從一個租用戶邀請到另一個租用戶的帳戶。 在邀請來賓帳戶至租用戶目錄後，就可以套用與任何其他帳戶一樣的存取控制。 您可以使用存取控制 (IAM) 刀鋒視窗，授與時間序列深入解析環境管理存取權。 或者，您可以透過 [資料存取原則] 刀鋒視窗授與環境中資料的存取權。 如需有關 Azure Active Directory (Azure AD) 租用戶來賓存取權的詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

依照這些步驟將對「時間序列深入解析」環境的存取權授與來自另一個租用戶的 Azure AD 使用者。

1. 選取 [資料存取原則]****，然後選取 [+ 邀請]****。

    [![選擇資料訪問員警，然後 + 邀請](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. 輸入您要邀請之使用者的電子郵件地址。 此電子郵件地址必須與 Azure AD 相關聯。 (選擇性) 您可以在邀請中包括個人訊息。

    [![輸入電子郵件地址以查找所選使用者](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. 尋找顯示在畫面上的確認泡泡訊息。

    [![查找要顯示的確認氣泡](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. 選擇 [選取使用者]****。 搜尋您邀請之來賓使用者的電子郵件地址，以尋找您要新增的使用者。 然後，**選擇**以確認所選內容。

    [![選擇使用者並確認選擇](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. 選擇 [選取角色]****。 為來賓使用者選擇適當的存取角色：

    * 如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]****。

    * 否則，選取 [讀取者]****，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。

   選取 [確定]**** 以確認角色選擇。

    [![確認角色選擇](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. 在 [選取使用者角色]**** 頁面中選取 [確定]****。

1. 確認 [資料存取原則]**** 頁面列出的來賓使用者與每個來賓使用者的角色。

    [![驗證使用者和角色是否正確分配](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. 現在，來賓使用者將在上面指定的電子郵件地址收到邀請電子郵件。 來賓使用者將選擇 **"開始"** 以確認其接受程度並連接到 Azure 雲。

    [![來賓選擇"開始接受"](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. 選擇 **"開始"** 後，來賓使用者將顯示一個與管理員組織關聯的許可權框。 在通過選擇 **"接受"** 授予許可權後，他們將登錄。

    [![來賓審核許可權並接受](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. 管理員與其來賓[共用環境 URL。](time-series-insights-parameterized-urls.md)

1. 在來賓使用者登錄到您用來邀請他們的電子郵件地址，並且他們接受邀請後，他們將被定向到 Azure 門戶。 

1. 來賓現在可以使用管理員提供的環境 URL 訪問共用環境。 他們可以將該 URL 輸入到其 Web 瀏覽器中，以便立即訪問。

1. 在時間序列資源管理器的右上角選擇其設定檔圖示後，管理員的租戶將顯示給來賓使用者。

    [![insights.azure.com上的阿凡達選擇](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    來賓使用者選擇管理員的租戶後，他們將能夠選擇共用的時間序列見解環境。 
    
    它們現在具有與您**在步驟 5**中提供的角色相關的所有功能。

    [![來賓使用者從下拉清單中選擇 Azure 租戶](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>後續步驟

* 了解[如何將 Azure 事件中樞的事件來源新增](./time-series-insights-how-to-add-an-event-source-eventhub.md)至時間序列深入解析環境。

* [將事件傳送到事件來源](./time-series-insights-send-events.md)。

* [在時間序列深入解析預覽總管中檢視您的環境](./time-series-insights-update-explorer.md)。
