---
title: Azure Active Directory 入口網站中的登入活動報告 | Microsoft Docs
description: 介紹 Azure Active Directory 入口網站中的登入活動報告
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246512"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的登入活動報告

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - [稽核記錄](concept-audit-logs.md)提供有關使用者和組管理、託管應用程式和目錄活動的系統活動資訊。
- **安全性** 
    - **風險**登錄 -[風險登錄](concept-risky-sign-ins.md)是非使用者帳戶合法擁有者的使用者嘗試登錄的指示器。
    - **標記為風險的使用者**-[風險使用者](concept-user-at-risk.md)是可能已洩露的使用者帳戶的指示器。

本文概述了登錄報告。

## <a name="prerequisites"></a>Prerequisites

### <a name="who-can-access-the-data"></a>誰可以存取資料？

* 安全管理員、安全讀取器、全域閱讀器和報表閱讀器角色中的使用者
* 全域系統管理員
* 任何使用者 (非系統管理員) 都可以存取自己的登入 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>您需要哪項 Azure AD 授權才能存取登入活動？

- 登錄活動報告在所有[版本的 Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)中都有可用。

- 如果要使用 API 訪問登錄資料，租戶必須具有與其關聯的[Azure 活動目錄高級](../fundamentals/active-directory-get-started-premium.md)許可證。



## <a name="sign-ins-report"></a>登入報告

使用者登入報告可回答下列問題：

* 使用者的登入模式為何？
* 一週內有多少使用者登入？
* 這些登入的狀態為何？

在[Azure 門戶](https://portal.azure.com)功能表上，選擇**Azure 活動目錄**，或從任何頁面搜索並選擇**Azure 活動目錄**。

![選取 Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

在 **"監視"** 下，選擇**登錄**以打開[登錄報告](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)。

![登錄活動](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "登入活動")

某些登錄記錄最多可能需要兩個小時才能顯示在門戶中。

> [!IMPORTANT]
> 登入報告只會顯示使用者透過使用者名稱和密碼以手動方式登入的**互動式**登入。 非互動式登入 (例如服務對服務驗證) 不會顯示在登入報告中。 

登入記錄的預設清單檢視會顯示︰

- 登入日期
- 相關的使用者
- 使用者已登錄到的應用程式
- 登入狀態
- 風險偵測的狀態
- 多重要素驗證 (MFA) 需求的狀態

![登錄活動](./media/concept-sign-ins/sign-in-activity.png "登入活動")

您可以通過按一下工具列中的**列**來自訂清單視圖。

![登錄活動](./media/concept-sign-ins/19.png "登入活動")

"**列**"對話方塊允許您訪問可選屬性。 在登錄報表中，不能將給定登錄請求具有多個值的欄位作為列。 例如，對於身份驗證詳細資訊、條件訪問資料和網路位置，這是事實。   

![登錄活動](./media/concept-sign-ins/columns.png "登入活動")

選取清單檢視中的項目，即可取得更詳細的資訊。

![登錄活動](./media/concept-sign-ins/basic-sign-in.png "登入活動")

> [!NOTE]
> 客戶現在可以通過所有登錄報告對條件訪問策略進行故障排除。 通過按一下登錄記錄的 **"條件訪問**"選項卡，客戶可以查看條件訪問狀態，並深入瞭解應用於登錄的策略的詳細資訊以及每個策略的結果。
> 如需詳細資訊，請參閱[所有登入中的 CA 資訊相關常見問題集](reports-faq.md#conditional-access)。



## <a name="filter-sign-in-activities"></a>篩選登入活動

首先，將報告的資料縮小到適合您的水準。 其次，使用日期欄位作為預設篩選器篩選登錄資料。 Azure AD 為您提供了可以設置的各種附加篩選器：

![登錄活動](./media/concept-sign-ins/04.png "登入活動")

**請求 ID** - 您關心的請求的 ID。

**使用者**- 您關心的使用者的名稱或使用者主體名稱 （UPN）。

**應用程式**- 目標應用程式的名稱。
 
**狀態**- 您關心的登錄狀態：

- Success

- 失敗

- 打斷


**IP 位址**- 用於連接到租戶的設備的 IP 位址。

**位置**- 連接從以下位置啟動的位置：

- City

- 州/省

- 國家/區域


**資源**- 用於登錄的服務的名稱。


**資源識別碼**- 用於登錄的服務的 ID。


**用戶端應用**- 用於連接到租戶的用戶端應用的類型：

![用戶端應用篩選器](./media/concept-sign-ins/client-app-filter.png)


|名稱|現代身份驗證|描述|
|---|:-:|---|
|經過身份驗證的 SMTP| |POP 和 IMAP 用戶端用於發送電子郵件。|
|自動探索| |Outlook 和 EAS 用戶端用於查找和連接到連線交換中的郵箱。|
|Exchange ActiveSync| |此篩選器顯示嘗試 EAS 協定的所有登錄嘗試。|
|瀏覽器|![勾選](./media/concept-sign-ins/check.png)|顯示使用 Web 瀏覽器的使用者的所有登錄嘗試|
|Exchange ActiveSync| | 使用 Exchange ActiceSync 顯示使用用戶端應用的使用者連接到線上交換的所有登錄嘗試|
|Exchange Online PowerShell| |用於使用遠端 PowerShell 連接到連線交換。 如果阻止 Exchange 線上 PowerShell 的基本驗證，則需要使用 Exchange 線上 PowerShell 模組進行連接。 有關說明，請參閱[使用多重要素驗證連接到交換線上 PowerShell。](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)|
|Exchange Web 服務| |Outlook、Mac Outlook 和協力廠商應用使用的程式設計介面。|
|IMAP4| |使用 IMAP 檢索電子郵件的舊郵件用戶端。|
|通過 HTTP 的 MAPI| |由 Outlook 2010 及更高版本使用。|
|行動裝置應用程式和桌面用戶端|![勾選](./media/concept-sign-ins/check.png)|顯示使用移動應用和桌面用戶端的使用者的所有登錄嘗試。|
|離線通訊錄| |Outlook 下載和使用的地址清單集合的副本。|
|隨時隨地進行展望（通過 HTTP 進行 RPC）| |由 Outlook 2016 及更早版本使用。|
|展望服務| |由 Windows 10 的郵件和日曆應用使用。|
|POP3| |使用 POP3 檢索電子郵件的舊郵件用戶端。|
|報告 Web 服務| |用於在"連線交換"中檢索報表資料。|
|其他用戶端| |顯示未包含用戶端應用或未知使用者的所有登錄嘗試。|



**作業系統**- 設備上運行的作業系統使用登錄到租戶。 


**設備瀏覽器**- 如果連接是從瀏覽器啟動的，則此欄位允許您按瀏覽器名稱進行篩選。


**關聯 ID** - 活動的相關 ID。




**條件訪問**- 應用的條件訪問規則的狀態

- **未應用**：在登錄期間未應用於使用者和應用程式的策略。

- **成功**：在登錄期間應用於使用者和應用程式（但不一定是其他條件）的一個或多個條件訪問策略。 

- **失敗**：應用了一個或多個條件訪問策略，但在登錄期間未滿足。









## <a name="download-sign-in-activities"></a>下載登入活動

按一下 **"下載**"選項可創建最近 250，000 條記錄的 CSV 或 JSON 檔。 如果要在 Azure 門戶之外使用[登錄資料，請從下載登錄資料](quickstart-download-sign-in-report.md)開始。  

![下載](./media/concept-sign-ins/71.png "下載")

> [!IMPORTANT]
> 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。  


## <a name="sign-ins-data-shortcuts"></a>登入資料捷徑

Azure AD 和 Azure 門戶都為您提供登錄資料的其他進入點：

- 身分識別安全性保護概觀
- 使用者
- 群組
- 企業應用程式

### <a name="users-sign-ins-data-in-identity-security-protection"></a>身分識別安全性保護中的使用者登入資料

**標識安全保護**概述頁中的使用者登錄圖顯示登錄的每週聚合。時間段的預設值為 30 天。

![登錄活動](./media/concept-sign-ins/06.png "登入活動")

當您按一下登入圖中的某一天時，會取得當日登入活動的概觀。

登入活動清單中的每一列會顯示：

* 誰已登入？
* 哪個應用程式是登入的目標？
* 登入狀態為何？
* 登入的 MFA 狀態為何？

按一下項目，即可取得有關登入作業的更多詳細資料：

- 使用者識別碼
- User
- 使用者名稱
- 應用程式識別碼
- Application
- Client
- Location
- IP 位址
- Date
- 需要 MFA
- 登入狀態

> [!NOTE]
> IP 位址的發出方式如下：IP 位址與該位址實際所在的電腦之間沒有任何明確的連線。 對應 IP 位址之所以複雜，是因為行動提供者和 VPN 會從中央集區發出 IP 位址，而中央集區通常距離用戶端裝置的實際使用位置非常遠。 目前在 Azure AD 報告中，根據追蹤、登錄資料、反向查詢和其他資訊，將 IP 位址轉換為實體位置的效果最佳。

在 [使用者]**** 頁面上，按一下 [活動]**** 區段中的 [登入]****，即可取得所有使用者登入的完整概觀。

![登錄活動](./media/concept-sign-ins/08.png "登入活動")

## <a name="usage-of-managed-applications"></a>受控應用程式的使用情況

利用登入資料以應用程式為主的檢視，您可以回答下列問題︰

* 誰在使用我的應用程式？
* 您的組織中的三大應用程式是什麼？
* 我的最新應用程式是如何做的？

此資料的切入點是組織中的前三個應用程式。 這些資料包含在**企業應用程式**下的 **"概述"** 部分的最後 30 天報告中。

![登錄活動](./media/concept-sign-ins/10.png "登入活動")

應用使用方式圖表可繪製給定時間段內前三個應用程式的登錄的每週聚合圖。 時間週期的預設值是 30 天。

![登錄活動](./media/concept-sign-ins/graph-chart.png "登入活動")

如果您想要，您可以將焦點設在特定的應用程式。

![報告](./media/concept-sign-ins/single-app-usage-graph.png "報告")

當您按一下應用程式使用圖中的某一天時，您會取得登入活動的詳細清單。

[登入] **** 選項會提供您的應用程式的所有登入事件的完整概觀。

## <a name="office-365-activity-logs"></a>Office 365 活動記錄

您可以查看來自[Microsoft 365 管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)的 Office 365 活動日誌。 請考慮以下幾點：Office 365 活動和 Azure AD 活動日誌共用大量目錄資源。 只有 Microsoft 365 管理中心提供 Office 365 活動日誌的完整視圖。 

您還可以使用[Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)以程式設計方式訪問 Office 365 活動日誌。

## <a name="next-steps"></a>後續步驟

* [登入活動報告錯誤碼](reference-sign-ins-error-codes.md)
* [Azure AD 資料保留原則](reference-reports-data-retention.md)
* [Azure AD 報告延遲](reference-reports-latencies.md)

