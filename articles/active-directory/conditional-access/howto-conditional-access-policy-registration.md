---
title: 條件訪問 - 組合安全資訊 - Azure 活動目錄
description: 為安全資訊註冊創建自訂條件訪問策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f69a94e17155ff93510d09f666bce12f628274f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295166"
---
# <a name="conditional-access-securing-security-info-registration"></a>條件訪問：保護安全資訊註冊

現在可以通過條件訪問策略中的使用者操作來保護使用者註冊 Azure 多重要素驗證和自助服務密碼重設的時以及如何。 此預覽功能可供啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織使用。 在希望使用受信任網路位置等條件來限制註冊 Azure 多重要素驗證和自助服務密碼重設 （SSPR） 的組織中，可以啟用此功能。 有關可用條件的詳細資訊，請參閱文章["條件訪問：條件](concept-conditional-access-conditions.md)"。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>創建策略以需要從受信任位置進行註冊

以下策略適用于所有選擇的使用者，這些使用者嘗試使用組合註冊體驗進行註冊，並阻止訪問，除非他們從標記為受信任網路的位置進行連接。

1. 在**Azure 門戶**中，流覽到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 選取 [新增原則]****。
1. 在"名稱"中，輸入此策略的名稱。 例如，**在受信任的網路上合併安全資訊註冊**。
1. 在 **"分配"** 下，選擇 **"使用者和組**"，然後選擇要應用於此策略的使用者和組。

   > [!WARNING]
   > 必須為[組合註冊預覽](../authentication/howto-registration-mfa-sspr-combined.md)啟用使用者。

1. 在 **"雲應用"或"操作**"下，選擇 **"使用者操作"，** 檢查**註冊安全資訊（預覽）。**
1. 在**條件** > **位置**。
   1. 配置**是**。
   1. 包括**任何位置**。
   1. 排除**所有受信任的位置**。
   1. 在"位置"邊欄選項卡上選擇 **"完成**"。
   1. 在"條件"邊欄選項卡上選擇 **"完成**"。
1. 在**條件** > **用戶端應用（預覽）** 下，將 **"配置配置為** **"，** 然後選擇 **"完成**"。
1. 在**存取控制** > **下授予**。
   1. 選取 [封鎖存取]****。
   1. 然後按一下 [選取] ****。
1. 將 [啟用原則]**** 設定為 [開啟]****。
1. 然後選擇 **"保存**"。

在本政策的步驟 6 中，組織可以選擇。 上述策略需要從受信任的網路位置進行註冊。 組織可以選擇利用任何可用的條件代替**地點**。 請記住，此策略是一個塊策略，因此任何包含內容都被阻止，並且允許任何與包含不匹配的東西。 

在上述步驟 6 中，有些人可能會選擇使用設備狀態而不是位置：

6. 在**條件** > **設備狀態（預覽）** 下。
   1. 配置**是**。
   1. 包括**所有設備狀態**。
   1. 排除**設備混合 Azure AD 聯接**和/或**標記為符合要求的設備**
   1. 在"位置"邊欄選項卡上選擇 **"完成**"。
   1. 在"條件"邊欄選項卡上選擇 **"完成**"。

> [!WARNING]
> 如果在策略中使用設備狀態作為條件，可能會影響目錄中的來賓使用者。 [僅報告模式](concept-conditional-access-report-only.md)可説明確定策略決策的影響。

## <a name="next-steps"></a>後續步驟

[條件訪問通用策略](concept-conditional-access-policy-common.md)

[使用僅條件訪問報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)
