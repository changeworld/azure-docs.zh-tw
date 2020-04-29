---
title: 如何從啟動/停止 Vm 解決方案查詢記錄
description: 本文說明如何從 Azure 監視器查詢啟動/停止 Vm 解決方案所產生的記錄資料。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472f3762ca18f71ba95053576daf025d8477fee9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604734"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>如何從啟動/停止 Vm 解決方案查詢記錄

Azure 自動化將兩種類型的記錄轉送至連結的 Log Analytics 工作區：作業記錄和作業串流。 這項資料可用於 Azure 監視器中的[查詢](../azure-monitor/log-query/log-query-overview.md)。

## <a name="job-logs"></a>作業記錄

|屬性 | 描述|
|----------|----------|
|呼叫者 |  起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。|
|類別 | 資料類型的分類。 對自動化來說，該值是 JobLogs。|
|CorrelationId | Runbook 作業之相互關聯識別碼的 GUID。|
|JobId | Runbook 作業之識別碼的 GUID。|
|operationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。|
|resourceId | 指定 Azure 中的資源類型。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
|ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。 對自動化來說，此值是 Azure 自動化。|
|ResourceType | 指定 Azure 中的資源類型。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|resultType | Runbook 作業的狀態。 可能的值包括：<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Succeeded|
|resultDescription | 說明 Runbook 作業的結果狀態。 可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed|
|RunbookName | 指定 Runbook 的名稱。|
|SourceSystem | 指定所提交資料的來源系統。 對自動化來說，該值是 OpsManager|
|StreamType | 指定事件的類型。 可能的值包括：<br>- Verbose<br>- Output<br>- Error (錯誤)<br>- Warning (警告)|
|SubscriptionId | 指定作業的訂用帳戶 ID。
|時間 | Runbook 作業的執行日期和時間。|

## <a name="job-streams"></a>作業串流

|屬性 | 描述|
|----------|----------|
|呼叫者 |  起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。|
|類別 | 資料類型的分類。 對自動化來說，該值是 JobStreams。|
|JobId | Runbook 作業之識別碼的 GUID。|
|operationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。|
|ResourceGroup | 指定 Runbook 作業的資源群組名稱。|
|resourceId | 指定 Azure 中的資源識別碼。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|ResourceProvider | 指定 Azure 服務，以提供您可部署及管理的資源。 對自動化來說，此值是 Azure 自動化。|
|ResourceType | 指定 Azure 中的資源類型。 對自動化來說，該值是與 Runbook 相關聯的自動化帳戶。|
|resultType | Runbook 作業在產生事件時的結果。 可能的值為：<br>- InProgres|
|resultDescription | 包含來自 Runbook 的輸出串流。|
|RunbookName | Runbook 的名稱。|
|SourceSystem | 指定所提交資料的來源系統。 對自動化來說，該值是 OpsManager。|
|StreamType | 作業串流的類型。 可能的值包括：<br>- Progress (進度)<br>- Output<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose|
|時間 | Runbook 作業的執行日期和時間。|

當您執行的記錄搜尋傳回 **JobLogs** 或 **JobStreams** 的類別記錄時，您可以選取 **JobLogs** 或 **JobStreams** 檢視，其中會顯示一組彙總搜尋所傳回更新的圖格。

## <a name="sample-log-searches"></a>記錄搜尋範例

下表提供此方案所收集之作業記錄的記錄檔搜尋範例。

|查詢 | 描述|
|----------|----------|
|尋找 ScheduledStartStop_Parent Runbook 已順利完成的作業 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|尋找尚未成功完成之 runbook ScheduledStartStop_Parent 的作業 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|尋找 SequencedStartStop_Parent Runbook 已順利完成的作業 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|尋找尚未成功完成之 runbook SequencedStartStop_Parent 的作業 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>後續步驟

**「停機期間啟動/停止 Vm」** 解決方案不包含預先定義的一組警示。 請參閱使用 Azure 監視器[建立記錄警示](../azure-monitor/platform/alerts-log.md)，以瞭解如何建立作業失敗的警示，以支援您的 DevOps 或操作進程和程式。