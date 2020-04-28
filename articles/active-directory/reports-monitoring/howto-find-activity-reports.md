---
title: 在 Azure 入口網站中尋找使用者活動報告 |Microsoft Docs
description: 了解 Azure Active Directory 使用者活動報告在 Azure 入口網站中的位置。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74008220"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>在 Azure 入口網站中尋找活動報告

在本文中，您將了解如何在 Azure 入口網站中尋找 Azure Active Directory (Azure AD) 使用者活動報告。

## <a name="audit-logs-report"></a>稽核記錄報告

稽核記錄報告會將應用程式活動的多個相關報告結合到單一檢視中，以取得有相關內容可參考的報告。 若要存取稽核記錄報告：

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]。
2. 從右上角選取您的目錄，然後從左側導覽窗格中選取 [Azure Active Directory]**** 刀鋒視窗。
3. 您可以從 [Azure Active Directory] 刀鋒視窗的 [活動]**** 區段中選取 [稽核記錄]****。 

    ![稽核記錄](./media/howto-find-activity-reports/482.png "稽核記錄")

稽核記錄報告會彙總下列報告：

* 稽核報告
* 密碼重設活動
* 密碼重設註冊活動
* 自助式群組活動
* Office365 群組名稱變更
* 帳戶佈建活動
* 密碼變換狀態
* 帳戶佈建錯誤

### <a name="filtering-on-audit-logs"></a>篩選稽核記錄

您可以在 [審查] 報告中使用 [advanced filter] 來存取特定類別的 audit data，方法是在 [**類別目錄**] 篩選準則中指定它。 例如，若要查看所有與使用者相關的活動，請選取 [ **usermanagement program.cs** ] 類別。 

分類包括：

- 全部
- AdministrativeUnit
- ApplicationManagement
- 驗證
- 授權
- 連絡人
- 裝置
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- 其他
- 原則
- ResourceManagement
- RoleManagement
- Usermanagement program.cs

您也可以使用 [**服務**] 下拉式清單篩選器來篩選特定的服務。 例如，若要取得與自助式密碼管理相關的所有 audit 事件，請選取 [**自助式密碼管理**] 篩選準則。

服務包括：

- 全部
- 存取權檢閱
- 帳戶佈建 
- 應用程式 SSO
- 驗證方法
- B2C
- 條件式存取
- 核心目錄
- 權利管理
- 身分識別保護
- 受邀的使用者
- PIM
- 自助式群組管理
- 自助式密碼管理
- 使用條款

## <a name="sign-ins-report"></a>登入報告 

**登入**檢視包含所有使用者登入，以及**應用程式使用量**報告。 您也可以在 [企業應用程式]**** 概觀的 [管理]**** 區段中檢視應用程式使用量資訊。

若要存取登入報告：

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]。
2. 從右上角選取您的目錄，然後從左側導覽窗格中選取 [Azure Active Directory]**** 刀鋒視窗。
3. 您可以從 [Azure Active Directory] 刀鋒視窗的 [活動]**** 區段中選取 [Signins]****。 

    ![登入視圖](./media/howto-find-activity-reports/483.png "登入視圖")


### <a name="filtering-on-application-name"></a>篩選應用程式名稱

您可以使用登入報告來檢視應用程式使用量的相關詳細資料，只要篩選使用者名稱或應用程式名稱即可。

![篩選登入事件頁面](./media/howto-find-activity-reports/07.png "篩選登入事件頁面")

## <a name="security-reports"></a>安全性報告

### <a name="anomalous-activity-reports"></a>異常活動報告

異常活動報告提供 Azure AD 可以偵測和報告的安全性相關風險偵測資訊。

下表列出 Azure 入口網站中的 Azure AD 異常活動安全性報告，以及對應的風險偵測類型。 如需詳細資訊，請參閱 [Azure Active Directory 風險偵測](concept-risk-events.md)。  


| Azure AD 異常活動報告 |  身分識別保護風險偵測類型|
| :--- | :--- |
| 認證外洩的使用者 | 認證外洩 |
| 異常的登入活動 | 不可能到達非典型位置的移動 |
| 從可能受感染的裝置登入 | 從受感染的裝置登入|
| 從不明來源登入 | 從匿名 IP 位址登入 |
| 從具有可疑活動的 IP 位址登入 | 從具有可疑活動的 IP 位址登入 |
| - | 從不熟悉的位置登入 |

下列 Azure AD 異常活動安全性報告並未包含在 Azure 入口網站的風險偵測中：

* 在多次失敗後登入
* 從多個地理區域登入


### <a name="detected-risk-detections"></a>偵測到的風險偵測

您可以在[Azure 入口網站](https://portal.azure.com)的 [ **Azure Active Directory** ] 分頁的 [**安全性**] 區段中，存取偵測到之風險偵測的相關報告。 偵測到的風險偵測會在下列報告中進行追蹤：   

- [有風險的使用者](concept-user-at-risk.md)
- [有風險的登入](concept-risky-sign-ins.md)

    ![安全性報告](./media/howto-find-activity-reports/04.png "安全性報告")

## <a name="troubleshoot-issues-with-activity-reports"></a>使用活動報告來進行問題疑難排解

### <a name="missing-data-in-the-downloaded-activity-logs"></a>已下載的活動記錄中遺漏資料

#### <a name="symptoms"></a>徵兆 

我已下載活動記錄 (稽核或登入)，卻沒看到我所選擇時間的所有記錄。 原因為何？ 

 ![報告](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>原因

當您下載 Azure 入口網站中的活動記錄時，我們會將縮放比例限制為250000筆記錄（依最新的第一個排序）。 

#### <a name="resolution"></a>解決方法

您可以利用 [Azure AD 報告 API](concept-reporting-api.md) 在任何指定時間點擷取最多一萬筆記錄。

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Azure 入口網站中遺漏最近動作的稽核記錄

#### <a name="symptoms"></a>徵兆

我在 Azure 入口網站中執行某些動作，並預期要在 `Activity logs > Audit Logs` 刀鋒視窗中查看這些動作的稽核記錄，但卻找不到。

 ![報告](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>原因

動作不會立即出現在活動記錄中。 下表列舉出我們活動記錄的延遲數據。 

| Report | &nbsp; | 延遲 (P95) | 延遲 (P99) |
|--------|--------|---------------|---------------|
| 目錄稽核 | &nbsp; | 2 分鐘 | 5 分鐘 |
| 登入活動 | &nbsp; | 2 分鐘 | 5 分鐘 | 

#### <a name="resolution"></a>解決方法

等候 15 分鐘到兩小時的時間，確認動作是否出現在記錄中。 如果在兩小時後仍看不到記錄，請[提出支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我們將加以探討。

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Azure AD 登入活動記錄中遺漏最近使用者登入的記錄

#### <a name="symptoms"></a>徵兆

我最近曾登入 Azure 入口網站，而預期應會在 `Activity logs > Sign-ins` 刀鋒視窗中看到這些動作的登入記錄，但卻找不到。

 ![報告](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>原因

動作不會立即出現在活動記錄中。 下表列舉出我們活動記錄的延遲數據。 

| Report | &nbsp; | 延遲 (P95) | 延遲 (P99) |
|--------|--------|---------------|---------------|
| 目錄稽核 | &nbsp; | 2 分鐘 | 5 分鐘 |
| 登入活動 | &nbsp; | 2 分鐘 | 5 分鐘 | 

#### <a name="resolution"></a>解決方法

等候 15 分鐘到兩小時的時間，確認動作是否出現在記錄中。 如果在兩小時後仍看不到記錄，請[提出支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我們將加以探討。

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>我無法在 Azure 入口網站中檢視超過 30 天的報告資料

#### <a name="symptoms"></a>徵兆

我無法從 Azure 入口網站檢視超過 30 天的登入和稽核資料。 原因為何？ 

 ![報告](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>原因

視您的授權而定，Azure Active Directory 動作會儲存下列期間的活動報告︰

| Report           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| 目錄稽核  | &nbsp; |   7 天     | 30 天             | 30 天             |
| 登入活動 | &nbsp; | 無法使用。 您可以從個別使用者設定檔刀鋒視窗中存取您自己 7 天內的登入資料 | 30 天 | 30 天             |

如需詳細資訊，請參閱 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。  

#### <a name="resolution"></a>解決方法

您有兩個選項可用來保留超過 30 天的資料。 您可以使用 [Azure AD 報告 API](concept-reporting-api.md) 以程式設計方式擷取資料，並將其儲存在資料庫中。 或者，您可以將稽核記錄整合到第三方 SIEM 系統中，例如 Splunk 或 SumoLogic。

## <a name="next-steps"></a>後續步驟

* [稽核記錄概觀](concept-audit-logs.md)
* [登入概觀](concept-sign-ins.md)
* [風險事件概觀](concept-risk-events.md)
