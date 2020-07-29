---
title: 開立 Microsoft Azure 自動化案例時要收集的資料 | Microsoft Docs
description: 本文將說明向 Microsoft Azure 支援開立 Azure 自動化案例之前要先收集的資訊。
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
ms.openlocfilehash: d2f97d0b889186324aef9613847e3eddbfe1eb02
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684854"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>開啟 Microsoft Azure 自動化案例時要收集的資料

本文將說明您向 Microsoft Azure 支援開立 Azure 自動化案例之前，應該先收集的一些資訊。 這些資訊並非開立案例的必要項目。 不過，這些資訊可以協助 Microsoft 更快速地解決您的問題。 此外，在您開立案例之後，支援工程師可能會要求您提供這些資料。

## <a name="basic-data"></a>基本資料

收集知識庫文章 [4034605 - 如何擷取 Azure 自動化指令碼診斷](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中所述的基本資料。

## <a name="data-for-update-management-issues-on-linux"></a>Linux 上更新管理問題的資料

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) 中所列的項目，請執行下列記錄收集工具：

   [OMS Linux 代理程式記錄收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 壓縮 **/var/opt/microsoft/omsagent/run/automationworker/** 資料夾的內容，然後將壓縮檔案傳送至 Azure 支援。
 
3. 確認適用於 Linux 的 Log Analytics 代理程式所報告的工作區識別碼，是否與正在監視其更新狀態的工作區識別碼相同。

## <a name="data-for-update-management-issues-on-windows"></a>Windows 上更新管理問題的資料

1. 收集 [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) 中所列項目的資料。

2. 將下列事件記錄匯出為 EVTX 格式：

   * 系統
   * Application
   * 安全性
   * Operations Manager
   * Microsoft-SMA/Operational

3. 確認代理程式所報告的工作區識別碼，是否與 Windows Update 正在監視的工作區識別碼相同。

## <a name="data-for-job-issues"></a>作業問題的資料

1. 收集 [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) 中所列項目的資料。

2. 收集問題作業的作業識別碼：

   1. 在 Azure 入口網站中，前往**自動化帳戶**。
   2. 選取您要進行疑難排解的自動化帳戶，並記下該名稱。
   3. 選取 [作業]。
   4. 選擇要進行疑難排解的作業。
   5. 在 [作業摘要] 窗格中，尋找**作業識別碼**中的 GUID 值。

   ![作業摘要窗格中的作業識別碼](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 收集您正在執行的指令碼樣本。

4. 收集記錄檔：

   1. 在 Azure 入口網站中，前往**自動化帳戶**。
   2. 選取您要進行疑難排解的自動化帳戶。
   3. 選取 [作業]。
   4. 選擇要進行疑難排解的作業。
   5. 選取 [所有記錄]。
   6. 在產生的窗格中，收集資料。

   ![列在 [所有記錄] 底下的資料](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>模組問題的資料

除了[基本資料項目](#basic-data)以外，請收集下列資訊：

* 您所遵循的步驟，以利重現問題。
* 任何錯誤訊息的螢幕擷取畫面。
* 目前模組及其版本號碼的螢幕擷取畫面。

## <a name="next-steps"></a>後續步驟

如果您需要更多協助：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫，這是專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
