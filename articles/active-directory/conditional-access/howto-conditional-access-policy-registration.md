---
title: 條件式存取 - 合併的安全性資訊 - Azure Active Directory
description: 針對安全性資訊註冊建立自訂條件式存取原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61f655d459592022a2e34b87adc50e3d49b03dc9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935762"
---
# <a name="conditional-access-securing-security-info-registration"></a>條件式存取：保護安全性資訊註冊

使用條件式存取原則中的使用者動作，即可保護使用者註冊 Azure AD Multi-Factor Authentication 和自助式密碼重設的時間和方式。 此預覽功能適用於已啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織。 如果組織想要使用受信任網路位置之類的條件來限制註冊 Azure AD Multi-Factor Authentication 和自助式密碼重設 (SSPR) ，則可能會啟用這項功能。 如需可用條件的詳細資訊，請參閱[條件式存取：條件](concept-conditional-access-conditions.md)。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立原則來要求從信任位置註冊

下列原則會套用到所有選取的使用者，其嘗試使用合併的註冊體驗進行註冊，如果他們不是從標示為信任網路的位置進行連線，則會封鎖他們的存取權。

1. 在 **Azure 入口網站** 中，瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 在 [名稱] 中，輸入此原則的名稱。 例如，**受信任網路上的合併安全性資訊註冊**。
1. 在 [指派] 底下，選取 [使用者和群組]，然後選取您要對其套用此原則的使用者和群組。

   > [!WARNING]
   > 使用者必須能使用[合併註冊](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 在 [雲端應用程式或動作] 底下選取 [使用者動作]，然後勾選 [註冊安全性資訊]。
1. 在 [條件] > [位置] 底下。
   1. 設定 [是]。
   1. 包含 [任何位置]。
   1. 排除 [所有信任的位置]。
   1. 選取 [位置] 分頁上的 [完成]。
   1. 選取 [條件] 分頁上的 [完成]。
1. 在 [條件] > [用戶端應用程式 (預覽)] 中，將 [設定] 設定為 [是]，然後選取 [完成]。
1. 在 [存取控制] > [授與] 底下。
   1. 選取 [封鎖存取]。
   1. 然後按一下 [選取] 。
1. 將 [啟用原則] 設定為 [開啟]。
1. 然後選取 [儲存]。

在此原則的步驟 6 中，組織會有他們可以決定的選擇。 上述原則需要從信任的網路位置進行註冊。 組織可以選擇利用任何可用條件來取代 [位置]。 請記住，此原則是封鎖原則，因此會封鎖包含的任何項目，而允許不符合內含項目的任何項目。 

有些人可能會選擇使用裝置狀態，而不是上述步驟 6 中的位置：

6. 在 [條件] > [裝置狀態 (預覽)] 底下。
   1. 設定 [是]。
   1. 包含 [所有裝置狀態]。
   1. 排除 [已加入混合式 Azure AD 的裝置] 及/或 [標示為符合規範的裝置]
   1. 選取 [位置] 分頁上的 [完成]。
   1. 選取 [條件] 分頁上的 [完成]。

> [!WARNING]
> 如果您使用裝置狀態作為原則中的條件，這可能會影響目錄中的來賓使用者。 [報告專用模式](concept-conditional-access-report-only.md)有助於判斷原則決策的影響。
> 請注意，僅限報表模式不適用於具有「使用者動作」範圍的條件式存取原則。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-insights-reporting.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
