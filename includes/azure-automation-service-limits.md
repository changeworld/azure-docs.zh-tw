---
title: 包含檔案
description: 包含檔案
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334913"
---
#### <a name="process-automation"></a>程序自動化

| 資源 | 限制 |備忘錄|
| --- | --- |---|
| 每個 Azure 自動化帳戶每隔30秒可提交的新作業數上限（nonscheduled 作業） |100 |達到此限制時，後續建立作業的要求將會失敗。 用戶端會收到錯誤回應。|
| 每個自動化帳戶在相同時間實例上並存執行的作業數目上限（nonscheduled 作業） |200 |達到此限制時，後續建立作業的要求將會失敗。 用戶端會收到錯誤回應。|
| 30天累積期間內作業中繼資料的最大儲存體大小 | 10 GB （大約4000000個工作）|達到此限制時，後續建立作業的要求將會失敗。 |
| 最大作業串流限制|1MB|單一資料流程不可大於 1 MB。|
| 每個自動化帳戶每隔30秒可匯入的模組數目上限 |5 ||
| 模組的大小上限 |100 MB ||
| 作業執行時間，免費層 |每個訂用帳戶每行事曆月份 500 分鐘 ||
| 每個沙箱所允許的最大磁碟空間量<sup>1</sup> |1 GB |僅適用于 Azure 沙箱。|
| 提供給沙箱的記憶體數量上限<sup>1</sup> |400 MB |僅適用于 Azure 沙箱。|
| 每個沙箱所允許的網路通訊端數目上限<sup>1</sup> |1,000 |僅適用于 Azure 沙箱。|
| 每個 runbook 允許的執行時間上限<sup>1</sup> |3 小時 |僅適用于 Azure 沙箱。|
| 訂用帳戶中的自動化帳戶數目上限 |沒有限制 ||
| 每個自動化帳戶的混合式背景工作角色群組數目上限|4,000||
|可在單一混合式 Runbook 背景工作角色上執行的並行作業數目上限|50 ||
| Runbook 作業參數大小上限   | 512 kb||
| Runbook 參數數目上限   | 50|如果您達到 50-參數的限制，您可以將 JSON 或 XML 字串傳遞給參數，並使用 runbook 加以剖析。|
| Webhook 承載大小上限 |  512 kb|
| 保留工作資料的最長天數|30 天|
| PowerShell 工作流程狀態大小上限 |5 MB| 適用于檢查點工作流程時的 PowerShell 工作流程 runbook。|

<sup>1</sup>沙箱是可供多個作業使用的共用環境。 使用相同沙箱的作業會受限於沙箱的資源限制。

#### <a name="change-tracking-and-inventory"></a>變更追蹤與詳細目錄

下表顯示每一部電腦的追蹤專案限制，以進行變更追蹤。

| **Resource** | **限制**| **注意事項** |
|---|---|---|
|檔案|500||
|登錄|250||
|Windows 軟體|250|不包含軟體更新。|
|Linux 套件|1250||
|服務|250||
|精靈|250||

#### <a name="update-management"></a>更新管理

下表顯示更新管理的限制。

| **Resource** | **限制**| **注意事項** |
|---|---|---|
|每個更新部署的機器數目|1000||