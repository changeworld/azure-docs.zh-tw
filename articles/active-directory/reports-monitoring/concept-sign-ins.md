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
ms.openlocfilehash: 81daa7696c645c1353cdd5e6d3cbca90ac4f03da
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388113"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的登入活動報告

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **審核記錄**  - [Audit 記錄](concept-audit-logs.md)提供使用者和群組管理、受控應用程式和目錄活動的相關系統活動資訊。
- **安全性** 
    - 有**風險**的登入-有風險的登[入](concept-risky-sign-ins.md)是指不是使用者帳戶合法擁有者的人登入嘗試的指標。
    - **標示有風險的使用者**-有[風險的使用者](concept-user-at-risk.md)是可能遭盜用之使用者帳戶的指標。

本文提供登入報告的總覽。

## <a name="prerequisites"></a>必要條件

### <a name="who-can-access-the-data"></a>誰可以存取資料？

* 安全性系統管理員、安全性讀取者、全域讀取者和報告讀取者角色中的使用者
* 全域系統管理員
* 任何使用者 (非系統管理員) 都可以存取自己的登入 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>您需要哪項 Azure AD 授權才能存取登入活動？

- [所有版本的 Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)都提供登入活動報告。

- 如果您想要使用 API 存取登入資料，您的租使用者必須有相關聯的[Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md)授權。



## <a name="sign-ins-report"></a>登入報告

使用者登入報告可回答下列問題：

* 使用者的登入模式為何？
* 一週內有多少使用者登入？
* 這些登入的狀態為何？

在 [ [Azure 入口網站](https://portal.azure.com)] 功能表上，選取 [ **Azure Active Directory**]，或搜尋並從任何頁面選取 [ **Azure Active Directory** ]。

![選取 Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

在 [**監視**] 底下，選取 [登**入**] 以開啟登[入報告](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)。

![登入活動](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "登入活動")

最多可能需要兩個小時，才會在入口網站中顯示某些登入記錄。

> [!IMPORTANT]
> 登入報告只會顯示使用者透過使用者名稱和密碼以手動方式登入的**互動式**登入。 非互動式登入 (例如服務對服務驗證) 不會顯示在登入報告中。 

登入記錄的預設清單檢視會顯示︰

- 登入日期
- 相關的使用者
- 使用者已登入的應用程式
- 登入狀態
- 風險偵測的狀態
- 多重要素驗證 (MFA) 需求的狀態

![登入活動](./media/concept-sign-ins/sign-in-activity.png "登入活動")

您可以按一下工具列中的 [資料**行**] 來自訂清單視圖。

![登入活動](./media/concept-sign-ins/19.png "登入活動")

[資料**行**] 對話方塊可讓您存取可選取的屬性。 在登入報告中，針對指定的登入要求，您不能有一個以上的值做為資料行。 例如，針對驗證詳細資料、條件式存取資料和網路位置為 true。   

![登入活動](./media/concept-sign-ins/columns.png "登入活動")

選取清單檢視中的項目，即可取得更詳細的資訊。

![登入活動](./media/concept-sign-ins/basic-sign-in.png "登入活動")

> [!NOTE]
> 客戶現在可以透過所有登入報告來針對條件式存取原則進行疑難排解。 藉由按一下登入記錄的 [**條件式存取**] 索引標籤，客戶可以檢查條件式存取狀態，並深入探索套用至每個原則登入和結果的原則詳細資料。
> 如需詳細資訊，請參閱[所有登入中的 CA 資訊相關常見問題集](reports-faq.md#conditional-access)。



## <a name="filter-sign-in-activities"></a>篩選登入活動

首先，將報告的資料縮小至適合您的層級。 第二，使用 [日期] 欄位作為預設篩選準則來篩選登入資料。 Azure AD 提供您可設定的各種額外篩選準則：

![登入活動](./media/concept-sign-ins/04.png "登入活動")

**要求識別碼**-您關心的要求識別碼。

**使用者**-您關心的使用者名稱或使用者主體名稱（UPN）。

**應用程式**-目標應用程式的名稱。
 
**狀態**-您關心的登入狀態：

- Success

- 失敗

- 會


**Ip 位址**-用來連線到您的租使用者之裝置的 ip 位址。

**位置**-連接起始的位置：

- 城市

- 州/省

- 國家/地區


**資源**-用來登入的服務名稱。


**資源識別碼**-用於登入之服務的識別碼。


**用戶端應用程式**-用來連接到您租使用者的用戶端應用程式類型：

![用戶端應用程式篩選器](./media/concept-sign-ins/client-app-filter.png)


|名稱|新式驗證|描述|
|---|:-:|---|
|已驗證的 SMTP| |由 POP 和 IMAP 用戶端用來傳送電子郵件訊息。|
|發現| |供 Outlook 和 EAS 用戶端用來尋找並聯機到 Exchange Online 中的信箱。|
|Exchange ActiveSync| |此篩選會顯示已嘗試 EAS 通訊協定的所有登入嘗試。|
|瀏覽器|![勾選](./media/concept-sign-ins/check.png)|使用網頁瀏覽器顯示使用者的所有登入嘗試|
|Exchange ActiveSync| | 顯示使用者在使用 Exchange ActiceSync 連線至 Exchange Online 時，使用用戶端應用程式進行的所有登入嘗試|
|Exchange Online PowerShell| |用來透過遠端 PowerShell 連接到 Exchange Online。 如果您封鎖 Exchange Online PowerShell 的基本驗證，您需要使用 Exchange Online PowerShell 模組來連接。 如需相關說明，請參閱[使用多重要素驗證連線至 Exchange Online PowerShell](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。|
|Exchange Web 服務| |Outlook、Outlook for Mac 和協力廠商應用程式所使用的程式設計介面。|
|IMAP4| |使用 IMAP 抓取電子郵件的舊版郵件用戶端。|
|透過 HTTP 的 MAPI| |由 Outlook 2010 和更新版本使用。|
|行動裝置應用程式和桌面用戶端|![勾選](./media/concept-sign-ins/check.png)|顯示使用者使用行動應用程式和桌面用戶端的所有登入嘗試。|
|離線通訊錄| |Outlook 下載並使用的地址清單集合複本。|
|Outlook Anywhere （RPC over HTTP）| |由 Outlook 2016 和更早版本使用。|
|Outlook 服務| |適用于 Windows 10 的郵件及行事曆應用程式。|
|POP3| |使用 POP3 抓取電子郵件的舊版郵件用戶端。|
|報表 Web 服務| |用來在 Exchange Online 中取出報表資料。|
|其他用戶端| |顯示用戶端應用程式未包含或不明的使用者進行的所有登入嘗試。|



**作業系統**-裝置上執行的作業系統，用來登入您的租使用者。 


**裝置瀏覽器**-如果連接是從瀏覽器起始，此欄位可讓您依瀏覽器名稱篩選。


相互**關聯識別碼**-活動的相互關聯識別碼。




**條件存取**-套用的條件式存取規則狀態

- **未**套用：在登入期間不會將原則套用至使用者和應用程式。

- **成功**：在登入期間套用至使用者和應用程式的一或多個條件式存取原則（但不一定是其他條件）。 

- **失敗**：登入符合至少一個條件式存取原則的使用者和應用程式條件，且未滿足或未將授與控制設定為封鎖存取。









## <a name="download-sign-in-activities"></a>下載登入活動

按一下 [**下載**] 選項，以建立最新250000記錄的 CSV 或 JSON 檔案。 如果您想要在 Azure 入口網站外使用，請從[下載登入資料](quickstart-download-sign-in-report.md)開始。  

![下載](./media/concept-sign-ins/71.png "下載")

> [!IMPORTANT]
> 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。  


## <a name="sign-ins-data-shortcuts"></a>登入資料捷徑

Azure AD 和 Azure 入口網站都提供登入資料的額外進入點：

- 身分識別安全性保護概觀
- 使用者
- 群組
- 企業應用程式

### <a name="users-sign-ins-data-in-identity-security-protection"></a>身分識別安全性保護中的使用者登入資料

[身分**識別安全性保護**] 總覽頁面中的使用者登入圖表會顯示每週的登入匯總。時間週期的預設值為30天。

![登入活動](./media/concept-sign-ins/06.png "登入活動")

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
- 應用程式
- Client
- Location
- IP 位址
- Date
- 需要 MFA
- 登入狀態

> [!NOTE]
> IP 位址的發出方式如下：IP 位址與該位址實際所在的電腦之間沒有任何明確的連線。 對應 IP 位址之所以複雜，是因為行動提供者和 VPN 會從中央集區發出 IP 位址，而中央集區通常距離用戶端裝置的實際使用位置非常遠。 目前在 Azure AD 報告中，根據追蹤、登錄資料、反向查詢和其他資訊，將 IP 位址轉換為實體位置的效果最佳。

在 [使用者]**** 頁面上，按一下 [活動]**** 區段中的 [登入]****，即可取得所有使用者登入的完整概觀。

![登入活動](./media/concept-sign-ins/08.png "登入活動")

## <a name="usage-of-managed-applications"></a>受控應用程式的使用情況

利用登入資料以應用程式為主的檢視，您可以回答下列問題︰

* 誰在使用我的應用程式？
* 貴組織中的前三個應用程式是什麼？
* 我的最新應用程式如何執行？

此資料的進入點是組織中的前三個應用程式。 資料會包含在 [**企業應用程式**] 底下 [**總覽**] 區段的 [最近30天] 報表中。

![登入活動](./media/concept-sign-ins/10.png "登入活動")

應用程式使用方式會在指定的時間週期內，針對您前三個應用程式的每週登入匯總進行匯總。 時間週期的預設值是 30 天。

![登入活動](./media/concept-sign-ins/graph-chart.png "登入活動")

如果您想要，您可以將焦點設在特定的應用程式。

![報告](./media/concept-sign-ins/single-app-usage-graph.png "報告")

當您按一下應用程式使用圖中的某一天時，您會取得登入活動的詳細清單。

[登入] **** 選項會提供您的應用程式的所有登入事件的完整概觀。

## <a name="office-365-activity-logs"></a>Office 365 活動記錄

您可以從[Microsoft 365 系統管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)觀看 Office 365 活動記錄。 請考慮 Office 365 活動和 Azure AD 活動記錄共用大量目錄資源的時間點。 只有 Microsoft 365 系統管理中心才會提供 Office 365 活動記錄的完整觀點。 

您也可以使用[office 365 管理 api](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)，以程式設計方式存取 office 365 活動記錄。

## <a name="next-steps"></a>後續步驟

* [登入活動報告錯誤碼](reference-sign-ins-error-codes.md)
* [Azure AD 資料保留原則](reference-reports-data-retention.md)
* [Azure AD 報告延遲](reference-reports-latencies.md)

