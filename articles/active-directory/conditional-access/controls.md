---
title: Azure AD 條件訪問中的自訂控制項
description: 瞭解 Azure 活動目錄條件訪問中的自訂控制項的工作原理。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050541"
---
# <a name="custom-controls-preview"></a>自訂控制項 (預覽)

自訂控制項是 Azure 活動目錄的預覽功能。 使用自訂控制項時，將使用者重定向到相容的服務，以滿足 Azure 活動目錄之外的身份驗證要求。 為了滿足此控制項，使用者的瀏覽器被重定向到外部服務，執行任何必需的身份驗證，然後重定向回 Azure 活動目錄。 Azure 活動目錄驗證回應，如果使用者已成功進行身份驗證或驗證，則使用者將繼續在條件訪問流中。

> [!NOTE]
> 有關我們計畫對自訂控制項功能進行更改的詳細資訊，請參閱 2020 年 2 月[新增更新](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)。

## <a name="creating-custom-controls"></a>建立自訂控制項

自訂控制項適用于一組有限的已批准身份檢查器提供者。 若要建立自訂控制項，請先連絡您想要使用的提供者。 每個非 Microsoft 供應商都有自己的註冊、訂閱或以其他方式成為服務的一部分的過程和要求，並指示您希望與條件訪問集成。 屆時，提供者會提供您 JSON 格式的資料區塊。 此資料允許提供程式和條件訪問為租戶協同工作，創建新控制項，並定義條件訪問如何判斷使用者是否已成功與提供程式執行驗證。

複製 JSON 資料並貼到相關文字方塊中。 除非您明確瞭解您所做的更改，否則不要對 JSON 進行任何更改。 進行變更可能會讓提供者與 Microsoft 之間的連線中斷，進而可能將您的帳戶鎖定，讓您和您的使用者無法使用。

創建自訂控制項的選項位於 **"條件訪問**"頁的 **"管理**"部分中。

![控制](./media/controls/82.png)

按一下 [新增自訂控制項]**** 隨即會開啟刀鋒視窗，裡面有文字方塊可供輸入控制項的 JSON 資料。  

![控制](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>刪除自訂控制項

要刪除自訂控制項，必須首先確保它不在任何條件訪問策略中使用。 完成之後：

1. 移至 [自訂控制項] 清單
1. 按一下 [...]  
1. 選擇 **"刪除**"。

## <a name="editing-custom-controls"></a>編輯自訂控制項

若要編輯自訂控制項，您必須先刪除目前的控制項，然後使用更新後的資訊建立新的控制項。

## <a name="known-limitations"></a>已知限制

自訂控制項不能與需要 Azure 多重要素驗證、Azure AD 自助服務密碼重設 （SSPR）、滿足多重要素驗證聲明要求或提升特權角色中的標識保護自動化一起使用標識管理器 （PIM）。

## <a name="next-steps"></a>後續步驟

- [條件訪問通用策略](concept-conditional-access-policy-common.md)

- [僅限報告模式](concept-conditional-access-report-only.md)

- [使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)
