---
title: 解決條件訪問登錄問題的疑難排解 - Azure 活動目錄
description: 本文介紹了條件訪問策略導致意外結果時應該怎麼做
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337437"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>解決條件訪問登錄問題的疑難排解

本文中的資訊可用於使用錯誤訊息和 Azure AD 登錄日誌解決與條件訪問相關的意外登錄結果。

## <a name="conditional-access-sign-in-interrupt"></a>條件訪問登錄中斷

第一種方法是查看顯示的錯誤訊息。 對於使用 Web 瀏覽器時登錄的問題，錯誤頁本身包含詳細資訊。 僅此資訊可以描述問題所在，並可能建議解決問題。

![需要登錄錯誤 - 符合設備的要求](./media/troubleshoot-conditional-access/image1.png)

在上述錯誤中，消息指出，應用程式只能從符合公司的行動裝置管理原則的設備或用戶端應用程式訪問。 在這種情況下，應用程式和設備不符合該策略。

## <a name="azure-ad-sign-in-events"></a>Azure AD 登錄事件

獲取有關登錄中斷的詳細資訊的第二種方法是查看 Azure AD 登錄事件，以查看應用了哪些條件訪問策略或策略以及原因。

按一下初始錯誤頁中的 **"更多詳細資訊**"，可以找到有關此問題的詳細資訊。 按一下 **"更多詳細資訊**"將顯示在搜索 Azure AD 登錄事件時有關使用者看到的特定故障事件或打開 Microsoft 支援事件時有用的故障排除資訊。

![來自條件訪問的更多詳細資訊中斷了 Web 瀏覽器登錄。](./media/troubleshoot-conditional-access/image2.png)

瞭解應用了哪些條件訪問策略或策略，以及為什麼執行以下操作。

1. 以全域管理員、安全管理員或全域讀取器身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **登錄**。
1. 查找要查看的登錄事件。 添加或刪除篩選器和列以篩選出不必要的資訊。
   1. 添加篩選器以縮小範圍：
      1. 當您要調查特定事件時 **，關聯 ID。**
      1. **條件訪問**以查看策略失敗和成功。 將篩選器的範圍範圍範圍，以僅顯示限制結果的失敗。
      1. **使用者名**以查看與特定使用者相關的資訊。
      1. **日期**範圍為相關時間範圍。

   ![在登錄日誌中選擇條件訪問篩選器](./media/troubleshoot-conditional-access/image3.png)

1. 找到與使用者登錄失敗對應的登錄事件後，請選擇 **"條件訪問**"選項卡。"條件訪問"選項卡將顯示導致登錄中斷的特定策略或策略。
   1. **"故障排除和支援**"選項卡中的資訊可能清楚地說明登錄失敗的原因，例如不符合合規性要求的設備。
   1. 要進一步調查，請點擊**策略名稱**，深入瞭解策略的配置。 按一下 **"策略名稱"** 將顯示所選策略的"策略配置"使用者介面，以便進行審閱和編輯。
   1. 用於條件訪問策略評估的**用戶端使用者****和設備詳細資訊**也可在登錄事件的 **"基本資訊**、**位置**、**設備資訊**、**身份驗證詳細資訊**"和 **"其他詳細資訊**"選項卡中提供。

   ![登錄事件條件訪問選項卡](./media/troubleshoot-conditional-access/image5.png)

如果事件中的資訊不足以瞭解登錄結果或調整策略以獲得預期結果，則可能會打開支援事件。 導航到該登錄事件的**疑難排解和支援**選項卡，然後選擇 **"創建新的支援請求**"。

![登錄事件的故障排除和支援選項卡](./media/troubleshoot-conditional-access/image6.png)

提交事件時，請在事件提交詳細資訊中提供請求 ID 以及登錄事件的時間和日期。 此資訊將允許 Microsoft 支援找到您關注的事件。

### <a name="conditional-access-error-codes"></a>條件訪問錯誤代碼

| 登錄錯誤代碼 | 錯誤字串 |
| --- | --- |
| 53000 | 設備不符合要求 |
| 53001 | 設備未進入 |
| 53002 | 應用程式使用isnotan批准應用程式 |
| 53003 | 阻止條件訪問 |
| 53004 | 校對 |

## <a name="next-steps"></a>後續步驟

- [Azure Active Directory 入口網站中的登入活動報告](../reports-monitoring/concept-sign-ins.md)
- [使用"如果"工具對條件訪問進行故障排除](troubleshoot-conditional-access-what-if.md)
- [Azure 活動目錄中的條件訪問](best-practices.md)最佳做法
