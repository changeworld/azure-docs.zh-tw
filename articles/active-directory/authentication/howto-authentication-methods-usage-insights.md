---
title: 認證方法使用&見解 - Azure 的項目
description: 報告 Azure AD 自助服務密碼重置和多重身份驗證方法使用
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
ms.openlocfilehash: 1730939de399cacd13c62988259904ba84ee78ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654137"
---
# <a name="authentication-methods-usage--insights-preview"></a>認證方法使用&見解(預覽)

使用&見解使您能夠瞭解 Azure 多重身份驗證和自助服務密碼重置等功能的身份驗證方法在組織中的工作方式。 此報告功能為您的組織提供了瞭解正在註冊的方法及其使用方式的方法。

## <a name="permissions-and-licenses"></a>權限和授權

以下角色可以存取使用方式和見解:

- 全域管理員
- 安全性讀取者
- 安全性系統管理員
- 報告讀者

無需額外的許可即可訪問使用方式和見解。 Azure 多重身份驗證和自助服務密碼重置 (SSPR) 許可資訊可在[Azure 活動目錄定價網站上](https://azure.microsoft.com/pricing/details/active-directory/)找到。

## <a name="how-it-works"></a>運作方式

要存取認證方法的使用與見解:

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽到**Azure 的目錄** > **密碼重置** > **使用此情況&見解**。
1. 從 **「註冊**或使用 **」** 概述中,您可以選擇打開預篩選的報告,以便根據您的需求進行篩選。

![使用方式&見解概述](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

要直接存取使用方式&見解,請[https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)造訪 。 此連結將帶您查看註冊概述。

已註冊的使用者、啟用了使用者磁貼和啟用「使用者」磁貼會顯示使用者的以下註冊數據:

- 已註冊:如果使用者(或管理員)註冊了足夠的身份驗證方法以滿足組織的 SSPR 或多重身份驗證策略,則被視為已註冊使用者。
- 已啟用:如果使用者在 SSPR 策略範圍內,則視為已啟用使用者。 如果為組啟用了 SSPR,則如果用戶位於該組中,則認為使用者已啟用。 如果為所有使用者啟用了 SSPR,則租戶中的所有使用者(不包括來賓)將被視為已啟用。
- 支援:如果用戶同時註冊並啟用,則認為該使用者已啟用。 此狀態意味著他們可以隨時執行 SSPR(如果需要)。

按下其中任何一個磁貼或其中顯示的見解將帶您進入預先篩選的註冊詳細資訊清單。

「**註冊**」 選項卡上的「**註冊**」 圖表顯示透過身份驗證方法成功和失敗的身份驗證方法註冊的數量。 **"使用方式**"選項卡上的 **"重置"** 圖表顯示通過身份驗證方法在密碼重置流期間成功和失敗的身份驗證數。

按一下任一圖表將帶您進入預先篩選的註冊或重置事件清單。

使用右上角的控制項,您可以將「註冊和重置」圖表中顯示的審核資料的日期範圍更改為 24 小時、7 天或 30 天。

### <a name="registration-details"></a>註冊詳情

按一下**已註冊的使用者**、**啟用了使用者**或**支援使用者的**磁貼或見解,將為您提供註冊詳細資訊。

註冊詳細資訊報告顯示每個使用者的以下資訊:

- 名稱
- [使用者名稱]
- 註冊狀態(全部、已註冊、未註冊)
- 開啟狀態(所有、已開啟、未啟用)
- 功能狀態(全部、可、無能力)
- 方法(應用程式通知、應用程式代碼、電話呼叫、簡訊、電子郵件、安全問題)

使用清單頂部的控制項,可以搜尋使用者並根據顯示的列篩選使用者清單。

### <a name="reset-details"></a>重置詳細資訊

按下「註冊」或「重置」圖表將為您提供重置詳細資訊。

重置詳細資訊報告顯示過去 30 天的註冊和重置事件,包括:

- 名稱
- [使用者名稱]
- 功能(全部、註冊、重置)
- 認證方法(應用程式通知、應用程式碼、電話呼叫、辦公室呼叫、簡訊、電子郵件、安全問題)
- 狀態(所有、成功、失敗)

使用清單頂部的控制項,可以搜尋使用者並根據顯示的列篩選使用者清單。

## <a name="limitations"></a>限制

這些報告中顯示的數據將延遲長達60分鐘。 Azure 門戶中存在"上次刷新"欄位,用於標識數據最近的情況。

使用方式和見解數據不能替代 Azure 多重身份驗證活動報告或 Azure AD 登錄報表中包含的資訊。

## <a name="next-steps"></a>後續步驟

- [使用認證方法使用報告 API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [為您的組織選擇認證方法](concept-authentication-methods.md)
- [合併註冊體驗](concept-registration-mfa-sspr-combined.md)
