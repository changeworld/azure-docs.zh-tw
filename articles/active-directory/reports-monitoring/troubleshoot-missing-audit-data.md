---
title: 排除活動日誌中缺少的資料的故障 |微軟文檔
description: 為您提供 Azure Active Directory 活動記錄中遺漏資料的解決方案。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c335a4d30846f7c1b4dbd6b6aedc4d100a9b43a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014280"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>對 Azure Active Directory 活動記錄中的遺漏資料進行疑難排解 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>我在 Azure 入口網站中找不到近期動作的稽核記錄

### <a name="symptoms"></a>徵狀

我在 Azure 入口網站中執行某些動作，並預期要在 `Activity logs > Audit Logs` 刀鋒視窗中查看這些動作的稽核記錄，但卻找不到。

 ![報告](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>原因

動作不會立即出現在活動記錄中。 下表列舉出我們活動記錄的延遲數據。 

| Report | &nbsp; | 延遲 (P95) | 延遲 (P99) |
|--------|--------|---------------|---------------|
| 目錄稽核 | &nbsp; | 2 分鐘 | 5 分鐘 |
| 登入活動 | &nbsp; | 2 分鐘 | 5 分鐘 | 

### <a name="resolution"></a>解決方案

等候 15 分鐘到兩小時的時間，確認動作是否出現在記錄中。 如果在兩小時後仍看不到記錄，請[提出支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我們將加以探討。

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>在 Azure Active Directory 登入活動記錄中找不到最近的使用者登入

### <a name="symptoms"></a>徵狀

我最近曾登入 Azure 入口網站，而預期應會在 `Activity logs > Sign-ins` 刀鋒視窗中看到這些動作的登入記錄，但卻找不到。

 ![報告](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>原因

動作不會立即出現在活動記錄中。 下表列舉出我們活動記錄的延遲數據。 

| Report | &nbsp; | 延遲 (P95) | 延遲 (P99) |
|--------|--------|---------------|---------------|
| 目錄稽核 | &nbsp; | 2 分鐘 | 5 分鐘 |
| 登入活動 | &nbsp; | 2 分鐘 | 5 分鐘 | 

### <a name="resolution"></a>解決方案

等候 15 分鐘到兩小時的時間，確認動作是否出現在記錄中。 如果在兩小時後仍看不到記錄，請[提出支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我們將加以探討。

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>我無法在 Azure 入口網站中檢視超過 30 天的報告資料

### <a name="symptoms"></a>徵狀

我無法從 Azure 入口網站檢視超過 30 天的登入和稽核資料。 原因為何？ 

 ![報告](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>原因

視您的授權而定，Azure Active Directory 動作會儲存下列期間的活動報告︰

| Report           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| 目錄稽核  | &nbsp; |   7 天     | 30 天             | 30 天             |
| 登入活動 | &nbsp; | 無法使用。 您可以從個別使用者設定檔刀鋒視窗中存取您自己 7 天內的登入資料 | 30 天 | 30 天             |

如需詳細資訊，請參閱 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。  

### <a name="resolution"></a>解決方案

您有兩個選項可用來保留超過 30 天的資料。 您可以使用 [Azure AD 報告 API](concept-reporting-api.md) 以程式設計方式擷取資料，並將其儲存在資料庫中。 或者，您可以將稽核記錄整合到第三方 SIEM 系統中，例如 Splunk 或 SumoLogic。

## <a name="next-steps"></a>後續步驟

* [Azure AD 報告保留](reference-reports-data-retention.md)。
* [Azure 活動目錄報告延遲](reference-reports-latencies.md)。
* [Azure 活動目錄報告常見問題 。](reports-faq.md)

