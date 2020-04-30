---
title: 當您開啟 Microsoft Azure Automation 的案例時所要收集的資料 |Microsoft Docs
description: 本文說明在您開啟 Azure 自動化 Microsoft Azure 支援的案例之前，應該先收集的一些資訊。
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
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679411"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>當您開啟 Microsoft Azure 自動化案例時要收集的資料

本文說明在您開啟 Azure 自動化 Microsoft Azure 支援的案例之前，應該先收集的一些資訊。 開啟案例不需要這種資訊。 不過，它可以協助 Microsoft 更快速地解決您的問題。 此外，在您開啟案例之後，支援工程師可能會要求您提供這項資料。

## <a name="basic-data"></a>基本資料

收集知識庫文章[4034605-如何捕捉 Azure 自動化腳本的診斷](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中所述的基本資料。

## <a name="data-for-update-management-issues-on-linux"></a>Linux 上的更新管理問題資料

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中所列的專案，請執行下列記錄收集工具：

   [OMS Linux 代理程式記錄檔收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 壓縮 **/var/opt/microsoft/omsagent/run/automationworker/** 資料夾的內容，然後將壓縮檔案傳送至 Azure 支援。
 
3. 確認適用于 Linux 的 Log Analytics 代理程式所報告的工作區識別碼，與受監視更新的工作區識別碼相同。

## <a name="data-for-update-management-issues-on-windows"></a>Windows 上更新管理問題的資料

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中所列專案的資料。

2. 將下列事件記錄檔匯出為 .EVTX 格式：

   * System (系統)
   * Application
   * 安全性
   * Operations Manager
   * Microsoft-SMA/營運

3. 確認代理程式所報告的工作區識別碼與 Windows 更新所監視之工作區的識別碼相同。

## <a name="data-for-job-issues"></a>作業問題的資料

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中所列專案的資料。

2. 收集具有問題之作業的作業識別碼：

   1. 在 Azure 入口網站中，移至 [**自動化帳戶**]。
   2. 選取您要進行疑難排解的自動化帳戶，並記下該名稱。
   3. 選取 [**作業**]。
   4. 選擇要進行疑難排解的作業。
   5. 在 [作業摘要] 窗格中，尋找 [**作業識別碼**] 中的 GUID 值。

   ![作業摘要窗格中的作業識別碼](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 收集您正在執行之腳本的範例。

4. 收集記錄檔：

   1. 在 Azure 入口網站中，移至 [**自動化帳戶**]。
   2. 選取您要進行疑難排解的自動化帳戶。
   3. 選取 [**作業**]。
   4. 選擇要進行疑難排解的作業。
   5. 選取 [**所有記錄**]。
   6. 在產生的窗格中，收集資料。

   ![列在 [所有記錄] 底下的資料](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>模組問題的資料

除了[基本資料項目](#basic-data)之外，也請收集下列資訊：

* 您所遵循的步驟，可以重現問題。
* 任何錯誤訊息的螢幕擷取畫面。
* 目前模組及其版本號碼的螢幕擷取畫面。

## <a name="next-steps"></a>後續步驟

如果您需要更多協助：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶交流，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援和專家。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。
