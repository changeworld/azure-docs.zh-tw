---
title: 條件式存取-結合的安全性資訊-Azure Active Directory
description: 建立安全性資訊註冊的自訂條件式存取原則
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457904"
---
# <a name="conditional-access-securing-security-info-registration"></a>條件式存取：保護安全性資訊註冊

在條件式存取原則中，您現在可以透過使用者動作來保護使用者註冊 Azure 多重要素驗證和自助式密碼重設的時間和方式。 此預覽功能適用于已啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織。 這項功能可能會在他們想要使用「信任的網路位置」等條件的組織中啟用，以限制註冊 Azure 多重要素驗證和自助式密碼重設（SSPR）的存取權。 如需可用條件的詳細資訊，請參閱[條件式存取：條件](concept-conditional-access-conditions.md)一文。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立要求從信任位置註冊的原則

下列原則適用于所有選取的使用者，其嘗試使用合併的註冊體驗進行註冊，並封鎖存取，除非它們是從標示為受信任的網路的位置進行連接。

1. 在 [ **Azure 入口網站**中，流覽至**Azure Active Directory** > **安全性** > **條件式存取**]。
1. 選取 [新增原則]****。
1. 在 [名稱] 中，輸入此原則的名稱。 例如，**在受信任的網路上註冊結合的安全性資訊**。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]，然後選取您想要套用此原則的使用者和群組。

   > [!WARNING]
   > 使用者必須啟用[合併註冊](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 在 [**雲端應用程式或動作**] 底下，選取 [**使用者動作**]，然後核取 [**註冊安全性資訊**
1. 在 [**條件** > **位置**] 底下。
   1. 設定 **[是]**。
   1. 包含**任何位置**。
   1. 排除**所有信任的位置**。
   1. 在 [位置] 分頁上選取 [**完成**]。
   1. 在 [條件] 分頁上選取 [**完成**]。
1. 在**Conditions** > **[條件用戶端應用程式（預覽）**] 底下，將 [設定]**設為** **[是]**，**然後選取**[
1. 在 [**存取控制** > ]**[授**與] 底下。
   1. 選取 [封鎖存取]****。
   1. 然後按一下 [選取] ****。
1. 將 [啟用原則]**** 設定為 [開啟]****。
1. 然後選取 [儲存]  。

在此原則的步驟6中，組織會有他們可以進行的選擇。 上述原則需要從信任的網路位置進行註冊。 組織可以選擇使用任何可用的條件來取代**位置**。 請記住，此原則是封鎖原則，因此會封鎖包含的任何專案，以及不符合包含的任何專案。 

有些可能會選擇使用裝置狀態，而不是上述步驟6中的位置：

6. 在 [**條件** > ]**[裝置狀態（預覽）**] 底下。
   1. 設定 **[是]**。
   1. 包含**所有裝置狀態**。
   1. 排除已**加入裝置**和/或已**標記為符合規範的裝置**混合式 Azure AD
   1. 在 [位置] 分頁上選取 [**完成**]。
   1. 在 [條件] 分頁上選取 [**完成**]。

> [!WARNING]
> 如果您使用裝置狀態作為原則中的條件，這可能會影響目錄中的來賓使用者。 [僅限報告模式](concept-conditional-access-report-only.md)可協助判斷原則決策的影響。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取僅限報告模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
