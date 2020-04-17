---
title: 條件存取 - 群組安全資訊 ─ Azure 的動作目錄
description: 為安全資訊註冊建立自訂條件存取原則
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
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457904"
---
# <a name="conditional-access-securing-security-info-registration"></a>條件訪問:保護安全資訊註冊

現在可以通過條件訪問策略中的使用者操作來保護用戶註冊 Azure 多重身份驗證和自助服務密碼重置的時以及如何。 此預覽功能可供啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織使用。 在希望使用受信任網路位置等條件來限制註冊 Azure 多重身份驗證和自助服務密碼重置 (SSPR) 的組織中,可以啟用此功能。 有關可用條件的詳細資訊,請參閱文章[「條件訪問:條件](concept-conditional-access-conditions.md)」。。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立策略以需要從受信任位置進行註冊

以下策略適用於所有選擇的使用者,這些用戶嘗試使用組合註冊體驗進行註冊,並阻止訪問,除非他們從標記為受信任網路的位置進行連接。

1. 在**Azure 門戶**中,瀏覽到**Azure 的目錄** > **安全** > **條件存取**。
1. 選取 [新增原則]****。
1. 在"名稱"中,輸入此策略的名稱。 例如,**在受信任的網路上合併安全資訊註冊**。
1. 在 **「分配」** 下,選擇 **「使用者和組**」,然後選擇要應用於此策略的使用者和組。

   > [!WARNING]
   > 必須為[合併註冊](../authentication/howto-registration-mfa-sspr-combined.md)啟用使用者。

1. 在 **"雲應用"或"操作**"下,選擇 **"使用者操作**",檢查**註冊安全資訊**。
1. 在**條件** > **位置**。
   1. 設定**檔 。**
   1. 包括**任何位置**。
   1. 排除**所有信任的位置**。
   1. 在「位置」邊欄選項卡上選擇 **「完成**」。
   1. 在「條件」邊欄選項卡上選擇 **「完成**」。
1. 在**條件** > **客戶端應用(預覽)** 下,將 **「設定配置為** **」,** 然後選擇 **「完成**」。
1. 存**取控制** > **下一個控制 。**
   1. 選取 [封鎖存取]****。
   1. 然後按一下 [選取] ****。
1. 將 [啟用原則]**** 設定為 [開啟]****。
1. 然後選擇 **「保存**」。

在本政策的步驟 6 中,組織可以選擇。 上述策略需要從受信任的網路位置進行註冊。 組織可以選擇使用任何可用的條件代替**地點**。 請記住,此策略是一個塊策略,因此任何包含內容都被阻止,並且允許任何與包含不匹配的東西。 

在上述步驟 6 中,有些人可能會選擇使用設備狀態而不是位置:

6. 在**條件** > **設備狀態(預覽)** 下。
   1. 設定**檔 。**
   1. 包括**所有裝置狀態**。
   1. 排除**裝置混合 Azure AD 聯接**與/或**標記為符合要求的裝置**
   1. 在「位置」邊欄選項卡上選擇 **「完成**」。
   1. 在「條件」邊欄選項卡上選擇 **「完成**」。

> [!WARNING]
> 如果在策略中使用設備狀態作為條件,可能會影響目錄中的來賓使用者。 [僅報告模式](concept-conditional-access-report-only.md)可幫助確定策略決策的影響。

## <a name="next-steps"></a>後續步驟

[條件存取一般原則](concept-conditional-access-policy-common.md)

[使用僅條件存取報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件存取「如果」工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
