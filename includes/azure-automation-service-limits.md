---
title: 包含檔案
description: 包含檔案
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 06/29/2020
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: aa7b997fe112be5d974d0e82597e786fa219e1f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85570157"
---
#### <a name="process-automation"></a>程序自動化

| 資源 | 限制 |注意|
| --- | --- |---|
| 每個 Azure 自動化帳戶每隔 30 秒可以送出的新作業數組態上限 (非排程作業) |100 |達到此限制時，後續的建立作業要求會失敗。 用戶端會收到錯誤回應。|
| 每個自動化帳戶在相同時間點的並行執行作業數目上限 (非排程作業) |200 |達到此限制時，後續的建立作業要求會失敗。 用戶端會收到錯誤回應。|
| 30 天累積期間內作業中繼資料的儲存體大小上限 | 10 GB (約 4 百萬個作業)|達到此限制時，後續的建立作業要求會失敗。 |
| 最大作業串流限制|1 MiB|單一資料流不可大於 1 MB。|
| 每個自動化帳戶每隔 30 秒可匯入的模組數組態上限 |5 ||
| 模組的大小上限 |100 MB ||
| 工作執行時間，免費層 |每個訂用帳戶每行事曆月份 500 分鐘 ||
| 每個沙箱所允許的磁碟空間數量上限<sup>1</sup> |1 GB |僅適用於 Azure 沙箱。|
| 提供給沙箱的記憶體大小上限<sup>1</sup> |400 MB |僅適用於 Azure 沙箱。|
| 每個沙箱所允許的網路通訊端數目上限<sup>1</sup> |1,000 |僅適用於 Azure 沙箱。|
| 每個 Runbook 所允許的執行階段上限<sup>1</sup> |3 個小時 |僅適用於 Azure 沙箱。|
| 訂用帳戶中自動化帳戶的最大數目 |沒有限制 ||
| 每個自動化帳戶的混合式背景工作角色群組數目上限|4,000||
|在單一混合式 Runbook 背景工作角色上執行的並行作業最大數目|50 ||
| Runbook 作業參數大小上限   | 512 KB||
| Runbook 參數上限   | 50|如果達到 50 個參數限制，您可以將 JSON 或 XML 字串傳遞至參數並使用 Runbook 進行剖析。|
| Webhook 裝載大小上限 |  512 KB|
| 保留作業資料的天數上限|30 天|
| PowerShell 工作流程狀態的大小上限 |5 MB| 適用於檢查點檢查工作流程時的 PowerShell 工作流程 Runbook。|

<sup>1</sup>沙箱是可供多個作業使用的共用環境。 使用相同沙箱的作業均受限於該沙箱的資源限制。

#### <a name="change-tracking-and-inventory"></a>變更追蹤與詳細目錄

下表顯示對於變更追蹤所追蹤的每個機器項目限制。

| **Resource** | **限制**| **備註** |
|---|---|---|
|檔案|500||
|登錄|250||
|Windows 軟體|250|不包含軟體更新。|
|Linux 套件|1,250||
|服務|250||
|精靈|250||

#### <a name="update-management"></a>更新管理

下表顯示更新管理的限制。

| **Resource** | **限制**| **備註** |
|---|---|---|
|每個更新部署的電腦數目|1000||