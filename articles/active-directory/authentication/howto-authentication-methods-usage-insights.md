---
title: 驗證方法使用 & 深入解析-Azure Active Directory
description: 報告 Azure AD 自助式密碼重設和多重要素驗證驗證方法使用方式
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053b052abf6f23c385dc7447639aa40b6c2c58a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680195"
---
# <a name="authentication-methods-usage--insights-preview"></a>驗證方法使用 & insights （預覽）

使用量 & 深入解析可讓您瞭解功能的驗證方法，例如 Azure 多重要素驗證和自助式密碼重設如何在您的組織中運作。 這項報告功能可讓您的組織瞭解哪些方法已註冊，以及其使用方式。

## <a name="permissions-and-licenses"></a>許可權和授權

下列角色可以存取使用方式和深入解析：

- 全域管理員
- 安全性讀取者
- 安全性系統管理員
- 報告讀者

不需要額外的授權，即可存取使用方式和深入解析。 您可以在[Azure Active Directory 定價網站](https://azure.microsoft.com/pricing/details/active-directory/)上找到 Azure 多因素驗證和自助式密碼重設（SSPR）授權資訊。

## <a name="how-it-works"></a>運作方式

若要存取驗證方法的使用方式和深入解析：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory**  >  **密碼重設**  >  **使用 & 深入**解析。
1. 在**註冊**或使用方式的**總覽**中，您可以選擇開啟預先篩選的報表，以根據您的需求進行篩選。

![使用量 & 深入解析總覽](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

若要直接存取使用量 & 深入解析，請移至 [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) 。 此連結將會帶您前往註冊總覽。

[已註冊的使用者]、[啟用的使用者] 和 [支援使用者] 磚會顯示使用者的下列註冊資料：

- 已註冊：如果使用者（或系統管理員）註冊了足夠的驗證方法，以符合您組織的 SSPR 或多重要素驗證原則，則會將其視為已註冊。
- 已啟用：如果使用者在 SSPR 原則的範圍內，則會將其視為已啟用。 如果已啟用群組的 SSPR，則會將使用者視為已啟用（如果他們在該群組中）。 如果已為所有使用者啟用 SSPR，則會將租使用者中的所有使用者（不包括來賓）視為已啟用。
- 可支援：如果使用者已註冊並啟用，則會被視為能夠使用。 此狀態表示它們可以隨時視需要執行 SSPR。

按一下其中任何磚或其中顯示的深入解析，就會帶您前往預先篩選的註冊詳細資料清單。

[**註冊**] 索引標籤上的**註冊**圖表會依驗證方法顯示驗證方法註冊的成功和失敗次數。 [**使用量**] 索引標籤上的 [**重設**] 圖表會顯示驗證方法在密碼重設流程期間成功和失敗的驗證次數。

按一下任一圖表會帶您前往預先篩選的註冊或重設事件清單。

使用右上角的控制項，您可以變更註冊所顯示的審核資料的日期範圍，並將圖表重設為24小時、7天或30天。

### <a name="registration-details"></a>註冊詳細資料

按一下 [**已註冊的使用者**]、[**已啟用使用者**] 或 [**使用者可支援**] 磚或深入解析，將會帶您前往註冊詳細資料。

[註冊詳細資料] 報表會顯示每個使用者的下列資訊：

- Name
- 使用者名稱
- 註冊狀態（全部、已註冊、未註冊）
- 已啟用狀態（全部、已啟用、未啟用）
- 可支援的狀態（全部、功能、不支援）
- 方法（代理程式更新、應用程式代碼、通話、SMS、電子郵件、安全性問題）

您可以使用清單頂端的控制項來搜尋使用者，並根據顯示的資料行篩選使用者清單。

### <a name="reset-details"></a>重設詳細資料

按一下 [註冊] 或 [重設] 圖表將會帶您前往 [重設詳細資料]。

[重設詳細資料] 報表會顯示過去30天內的註冊和重設事件，包括：

- Name
- 使用者名稱
- 功能（全部、註冊、重設）
- 驗證方法（代理程式更新、應用程式代碼、通話、Office 通話、SMS、電子郵件、安全性問題）
- 狀態（全部、成功、失敗）

您可以使用清單頂端的控制項來搜尋使用者，並根據顯示的資料行篩選使用者清單。

## <a name="limitations"></a>限制

這些報告中顯示的資料最多會延遲60分鐘。 [上次重新整理] 欄位存在於 Azure 入口網站中，用以識別資料的最近程度。

[使用量] 和 [深入解析] 資料不是 Azure 多因素驗證活動報告或 Azure AD 登入報告中所包含資訊的取代。

目前無法將報表篩選成排除外部使用者。

## <a name="next-steps"></a>後續步驟

- [使用驗證方法使用報告 API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [選擇貴組織的驗證方法](concept-authentication-methods.md)
- [合併的註冊體驗](concept-registration-mfa-sspr-combined.md)
