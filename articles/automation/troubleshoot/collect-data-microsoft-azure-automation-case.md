---
title: 打開 Microsoft Azure 自動化案例時要收集的資料*微軟文檔
description: 本文介紹了在使用 Microsoft Azure 支援為 Azure 自動化打開案例之前應收集的一些基本資訊。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849372"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>當您開啟 Microsoft Azure 自動化案例時要收集的資料

本文介紹了在使用 Microsoft Azure 支援為 Azure 自動化打開案例之前應收集的一些基本資訊。 打開案例不需要此資訊。 但是，它可以説明 Microsoft 更快地解決您的問題。 此外，在打開案例後，支援工程師可能會要求您提供此資料。

## <a name="collect-more-information"></a>收集更多資訊

在為 Microsoft Azure 自動化支援打開案例之前，我們建議您收集以下資訊。

### <a name="basic-data-collection"></a>基本資料收集

收集以下知識庫文章中描述的資料：

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)如何捕獲 Azure 自動化腳本診斷

## <a name="collect-data-depending-on-issue"></a>根據問題收集資料
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>對於影響 Linux 上更新管理的問題

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的項外，請運行以下日誌收集工具：

   [OMS Linux 代理日誌收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 壓縮以下資料夾的內容，然後將壓縮檔發送到 Azure 支援：

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. 驗證 OMS Linux 代理報告的工作區 ID 是否與正在監視更新的工作區相同。

### <a name="for-issues-that-affect-update-management-on-windows"></a>對於影響 Windows 上更新管理的問題

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的項外，執行以下操作：

1. 將以下事件日誌匯出為 EVTX 格式：

   * 系統
   * Application
   * 安全性
   * Operations Manager
   * 微軟SMA/運營

2. 驗證代理報告的工作區 ID 與 Windows 更新監視的工作區相同。

### <a name="for-issues-that-affect-a-job"></a>對於影響作業的問題

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的專案外，還收集以下資訊：

1. 收集**JobID**編號（對於遇到問題的作業）：

   1. 在 Azure 門戶，轉到 **"自動化帳戶"** 邊欄選項卡。
   2. 選擇要疑難排解的**自動化帳戶**。
   3. 選擇**作業**。
   4. 選擇要疑難排解的作業。
   5. 在**作業摘要**下，查找**作業 ID** （GUID）。

   ![作業摘要窗格中的作業 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. 收集帳戶名稱：

   1. 在 Azure 門戶，轉到 **"自動化帳戶"** 邊欄選項卡。
   2. 獲取正在故障排除的**自動化帳戶**的名稱。

3. 收集正在運行的腳本的示例。

4. 收集日誌檔：

   1. 在 Azure 門戶，轉到 **"自動化帳戶"** 邊欄選項卡。
   2. 選擇要疑難排解的**自動化帳戶**。
   3. 選擇**作業**。
   4. 選擇要疑難排解的作業。
   5. 選擇**所有日誌**。
   6. 在生成的邊欄選項卡上，收集資料。

   !["所有日誌"下列出的資料](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>對於影響模組的問題

除了"基本資料收集"項下的專案外，還收集以下資訊：

* 您所遵循的步驟，以便重現問題。
* 任何錯誤訊息的螢幕截圖。
* 當前模組及其版本號的螢幕截圖。

## <a name="next-steps"></a>後續步驟

如果本文中的任何一點都需要更多説明，請與[MSDN Azure 和堆疊溢位論壇上的](https://azure.microsoft.com/support/forums/)Azure 專家聯繫。

或者，提交 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。
