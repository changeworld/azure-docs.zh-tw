---
title: '& insights 的驗證方法使用方式-Azure Active Directory'
description: Azure AD 自助式密碼重設和 Multi-Factor Authentication 驗證方法使用方式的報告
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe8a916f4c3ce8869b6b487e33dcdecc061f8cbf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839585"
---
# <a name="authentication-methods-usage--insights-preview"></a>& insights (preview) 的驗證方法使用方式

使用量 & 見解可讓您瞭解功能（例如 Azure AD Multi-Factor Authentication 和自助式密碼重設）的驗證方法在組織中的運作方式。 這項報告功能可讓您的組織瞭解哪些方法已註冊，以及其使用方式。

## <a name="permissions-and-licenses"></a>許可權與授權

下列角色可以存取使用量和見解：

- 全域管理員
- 安全性讀取者
- 安全性系統管理員
- 報告讀者

不需要額外的授權即可存取使用量和深入解析。 Azure AD Multi-Factor Authentication 和自助式密碼重設 (SSPR) 授權資訊可于 [Azure Active Directory 定價網站](https://azure.microsoft.com/pricing/details/active-directory/)上找到。

## <a name="how-it-works"></a>運作方式

若要存取驗證方法使用方式和見解：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 流覽以 **Azure Active Directory**  >  **密碼重設**  >  **使用 & 見解**。
1. 您可以從 **註冊** 或使用方式的 **總覽** ，選擇開啟預先篩選的報表，以根據您的需求進行篩選。

![使用量 & 見解總覽](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

若要直接存取使用量 & 見解，請移至 [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) 。 此連結會帶您前往註冊總覽。

已註冊的使用者、已啟用使用者，以及支援使用者的磚會顯示下列使用者的註冊資料：

- 已註冊：若使用者 (或系統管理員) 註冊了足夠的驗證方法，以符合您組織的 SSPR 或 Multi-Factor Authentication 原則，則會將使用者視為已註冊。
- 啟用：如果使用者在 SSPR 原則的範圍內，則會被視為已啟用。 如果已針對群組啟用 SSPR，則會將使用者視為已啟用（如果使用者在該群組中）。 如果已為所有使用者啟用 SSPR，則會將租使用者中 (不包括來賓) 的所有使用者視為已啟用。
- 具備：如果使用者已註冊且啟用，則會被視為能夠使用。 此狀態表示可以在需要時隨時執行 SSPR。

按一下任何圖格或其中顯示的見解，將會帶您前往預先篩選的註冊詳細資料清單。

[**註冊**] 索引標籤上的 **[註冊] 圖表會** 顯示驗證方法註冊成功和失敗的次數。 [**使用** 方式] 索引標籤上的 [**重設**] 圖表會顯示驗證方法在密碼重設流程期間的成功和失敗的驗證次數。

按一下任一圖表會帶您前往預先篩選的註冊或重設事件清單。

您可以使用右上角的控制項，變更註冊中所顯示之審核資料的日期範圍，並將圖表重設為24小時、7天或30天。

### <a name="registration-details"></a>註冊詳細資料

按一下 [ **已註冊的使用者**]、[ **已啟用** 的使用者] 或具有 **使用者能力** 的磚或見解，將會帶您前往註冊詳細資料。

[註冊詳細資料] 報表會顯示每個使用者的下列資訊：

- 名稱
- 使用者名稱
- 註冊狀態 (全部、已註冊、未註冊) 
- 啟用狀態 (全部、已啟用、未啟用) 
- 可 (所有、功能、無法) 的可用狀態
-  (代理程式更新、應用程式程式碼、電話、SMS、電子郵件、安全性問題的方法) 

您可以使用清單頂端的控制項，根據所顯示的資料行來搜尋使用者並篩選使用者清單。

### <a name="reset-details"></a>重設詳細資料

按一下註冊或重設圖表會帶您前往重設詳細資料。

[重設詳細資料] 報表會顯示過去30天內的註冊和重設事件，包括：

- 名稱
- 使用者名稱
- 功能 (全部、註冊、重設) 
- 驗證方法 (代理程式更新、應用程式程式碼、通話、辦公室電話、SMS、電子郵件、安全性問題) 
- 狀態 ([全部]、[成功]、[失敗]) 

您可以使用清單頂端的控制項，根據所顯示的資料行來搜尋使用者並篩選使用者清單。

## <a name="limitations"></a>限制

這些報表中顯示的資料最多會延遲60分鐘。 [上次重新整理] 欄位存在於 Azure 入口網站中，以識別您的資料最近的程度。

使用方式和見解資料不會取代 Azure AD 的 Multi-Factor Authentication 活動報告或 Azure AD 登入報告中所包含的資訊。

目前無法篩選報表以排除外部使用者。

## <a name="next-steps"></a>後續步驟

- [使用驗證方法使用方式報表 API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [為您的組織選擇驗證方法](concept-authentication-methods.md)
- [合併的註冊體驗](concept-registration-mfa-sspr-combined.md)