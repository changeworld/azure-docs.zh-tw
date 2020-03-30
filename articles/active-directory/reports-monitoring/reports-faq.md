---
title: Azure Active Directory 報告常見問題集 | Microsoft Docs
description: 圍繞 Azure 活動目錄報表的常見問題。
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266503"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>關於 Azure Active Directory 報告的常見問題集

本文會回答有關 Azure Active Directory (Azure AD) 報告的常見問題。 如需詳細資訊，請參閱 [Azure Active Directory 報告](overview-reports.md)。 

## <a name="getting-started"></a>開始使用 

**問：我當前使用`https://graph.windows.net/<tenant-name>/reports/`終結點 API 以程式設計方式將 Azure AD 審核和集成應用程式使用方式報告拉至我們的報告系統中。我應該切換到什麼？**

**答：** 請查閱 [API 參考](https://developer.microsoft.com/graph/)，查看您可以如何使用 API 來存取[活動報告](concept-reporting-api.md)。 此終結點有兩個報告 （**審核**和**登錄**）， 它們提供您在舊 API 終結點中得到的所有資料。 這個新端點也有 Azure AD Premium 授權的登入報告，您可以用它來取得應用程式使用方式、裝置使用方式，以及使用者登入資訊。

---

**問：我當前使用`https://graph.windows.net/<tenant-name>/reports/`終結點 API 以程式設計方式將 Azure AD 安全報告（特定類型的檢測，如洩露的憑據或來自匿名 IP 位址的登錄）拉入我們的報告系統。我應該切換到什麼？**

**答：** 您可以使用 [身份保護風險檢測 API](../identity-protection/graph-get-started.md) 通過 Microsoft 圖形訪問安全檢測。 這種新格式在如何查詢資料方面提供了更大的靈活性，包括高級篩選、欄位選擇等，並將風險檢測標準化為一種類型，以便更輕鬆地集成到 SIEM 和其他資料收集工具中。 由於資料的格式不同，您無法以新查詢替換舊查詢。 不過，[新 API 會使用 Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)，這是 O365 或 Azure AD 這類 API 的 Microsoft 標準格式。 因此，所需的工作可以擴展您當前的 Microsoft 圖形投資，或者説明您開始過渡到這個新的標準平臺。

---

**問：我要如何取得進階授權？**

**答：** 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。

---

**問：在取得進階授權之後，我應該多快能看見活動資料？**

**答：** 如果您在使用免費授權時即已經有活動資料，您便可以立即看見該資料。 如果您沒有任何資料，則必須花一或兩天的時間，資料才會顯示在報告中。

---

**問：在取得 Azure AD 進階授權之後，我能否看見上個月的資料？**

**答：** 如果您最近已切換到高級版（包括試用版），則最初最多可以看到 7 天的資料。 當資料累積起來時，您便可以看到過去 30 天的資料。

---

**問：我是否必須是全域系統管理員，才能看到登入 Azure 入口網站的活動，或是透過 API 取得資料？**

**答：** 不必，如果您是租用戶的**安全性讀取者**或**安全性系統管理員**，則也可以透過入口網站或透過 API 存取報告資料。 當然，**全域系統管理員**也可存取此資料。

---


## <a name="activity-logs"></a>活動記錄


**問：Azure 入口網站中活動記錄 (稽核和登入) 的資料保留期是多長？** 

**答：** 下表列出活動記錄的資料保留期間。 如需詳細資訊，請參閱 [Azure AD 報告的資料保留原則](reference-reports-data-retention.md)。

| Report                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 稽核記錄             | 7 天        | 30 天             | 30 天             |
| 登入               | N/A           | 30 天             | 30 天             |
| Azure MFA 使用        | 30 天       | 30 天             | 30 天             |

---

**問：完成任務後，需要多長時間才能看到活動資料？**

**答：** 稽核記錄的延遲時間為 15 分鐘到 1 小時。 針對某些記錄，登入活動記錄可能需要 15 分鐘到 2 小時。

---

**問：我是否可以透過 Azure 入口網站取得 Office 365 活動記錄資訊？**

**答：** 即使 Office 365 活動和 Azure AD 活動日誌共用大量目錄資源，但如果需要 Office 365 活動日誌的完整視圖，則應轉到 Microsoft [365 管理中心](https://admin.microsoft.com)以獲取 Office 365 活動日誌資訊。

---

**問：我應該使用哪些 API 來取得 Office 365 活動記錄的相關資訊？**

**答：** 使用[Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)通過 API 訪問 Office 365 活動日誌。

---

**問：我可以從 Azure 入口網站下載多少記錄？**

**答：** 您可以從 Azure 入口網站最多下載 5000 筆記錄。 這些記錄會依「時間上最近」** 方式來排序，而根據預設，您會取得最近的 5000 筆記錄。

---

## <a name="risky-sign-ins"></a>有風險的登入

**問：身份保護中有風險檢測，但我在登錄報告中看不到相應的登錄。這是預料之中的嗎？**

**答：** 是，Identity Protection 會評估所有驗證流程的風險，無論是互動式或非互動式。 不過，所有登入只會報告顯示互動式登入。

---

**問：如何知道為何在 Azure 入口網站中登入或使用者會標示為風險？**

**答：** 如果您有 Azure **AD 高級**訂閱，則可以通過在**標記為風險的使用者**中選擇使用者或在**風險登錄**報告中選擇記錄來瞭解有關基本風險檢測的更多資訊。 如果您有**免費**或**基本**訂閱，則可以查看有風險的使用者和有風險的登錄報告，但看不到潛在的風險檢測資訊。

---

**問：如何在登入和有風險的登入報告中計算 IP 位址？**

**答：** IP 位址的發出方式如下：IP 位址與該位址實際所在的電腦之間沒有任何明確的連線。 對應 IP 位址會因為某些因素而變得更加復雜，例如行動提供者和從中央集區發出 IP 位址的 VPN 通常距離實際使用用戶端裝置的位置非常遠。 目前在 Azure AD 報告中，根據追蹤、登錄資料、反向查詢和其他資訊，將 IP 位址轉換為實體位置的效果最佳。 

---

**問：風險檢測"檢測到額外風險的登錄"是什麼意思？**

**答：** 為讓您深入了解您環境中所有具有風險的登入，「登入時偵測到其他風險」可作為 Azure AD Identity Protection 訂閱者專屬偵測的登入預留位置。

---

## <a name="conditional-access"></a>條件式存取

**問：這項功能有什麼新增功能？**

**答：** 客戶現在可以通過所有登錄報告對條件訪問策略進行故障排除。 客戶可以查看條件訪問狀態，並深入瞭解應用於登錄的策略的詳細資訊以及每個策略的結果。

**問：如何開始使用？**

**答：** 開始進行之前：

* 巡覽至 [Azure 入口網站](https://portal.azure.com)中的登入報告。
* 按一下您想要進行疑難排解的登入。
* 導航到**條件訪問**選項卡。在這裡，您可以查看影響每個策略登錄的所有策略和結果。 
    
**問：條件訪問狀態的可能值是什麼？**

**答：** 條件訪問狀態可以具有以下值：

* **不適用**：這表示沒有任何 CA 原則包含範圍中的使用者和應用程式。 
* **成功**：這表示有一個 CA 原則包含範圍中的使用者和應用程式，且成功地符合 CA 原則。 
* **失敗**：這表示有一個 CA 原則包含範圍中的使用者和應用程式，且不符合 CA 原則。 
    
**問：條件訪問策略結果的可能值是什麼？**

**答：** 條件訪問策略可以具有以下結果：

* **成功**：已成功地符合原則。
* **失敗**：不符合原則。
* **不適用**：這可能是因為不符合原則條件。
* **未啟用**：這是因為原則處於停用狀態。 
    
**問：所有登錄報告中的策略名稱與 CA 中的策略名稱不匹配。為什麼？**

**答：** 所有登入報告中的原則名稱是以登入時的 CA 原則名稱為基礎。 如果您稍後 (也就是登入之後) 更新原則名稱，這可能會與 CA 中的原則名稱不一致。

**問：由於條件訪問策略，我的登錄被阻止，但登錄活動報告顯示登錄成功。為什麼？**

**答：** 當前，當應用條件訪問時，登錄報告可能無法顯示 Exchange ActiveSync 方案的準確結果。 在某些情況下，報表中的登錄結果顯示成功登錄，但登錄實際上由於條件訪問策略而失敗。 
