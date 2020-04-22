---
title: 打開 Microsoft Azure 自動化案例時要收集的數據*微軟文件
description: 本文介紹了在使用 Microsoft Azure 支援為 Azure 自動化打開案例之前應收集的一些資訊。
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679411"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>當您開啟 Microsoft Azure 自動化案例時要收集的資料

本文介紹了在使用 Microsoft Azure 支援為 Azure 自動化打開案例之前應收集的一些資訊。 打開案例不需要此資訊。 但是,它可以説明 Microsoft 更快地解決您的問題。 此外,在打開案例后,支援工程師可能會要求您提供此數據。

## <a name="basic-data"></a>基本資料

收集知識庫文章[4034605 中描述的基本資料 - 如何捕獲 Azure 自動化腳本診斷](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)。

## <a name="data-for-update-management-issues-on-linux"></a>Linux 上更新管理問題的資料

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的項外,請運行以下日誌收集工具:

   [OMS Linux 代理紀錄收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 壓縮 **/var/opt/microsoft/omsagent/運行/自動化輔助/** 資料夾的內容,然後將壓縮檔發送到 Azure 支援。
 
3. 驗證 Linux 紀錄分析代理報告的工作區的 ID 是否與受監視的工作區的 ID 相同。

## <a name="data-for-update-management-issues-on-windows"></a>用於 Windows 上更新管理問題的資料

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的項目的數據。

2. 將以下事件紀錄匯出為 EVTX 格式:

   * 系統
   * Application
   * 安全性
   * Operations Manager
   * 微軟SMA/運營

3. 驗證代理報告的工作區的 ID 是否與 Windows 更新監視的工作區的 ID 相同。

## <a name="data-for-job-issues"></a>工作問題的資料

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的項目的數據。

2. 收集出現問題的作業的作業 ID:

   1. 在 Azure 門戶中,轉到**自動化帳戶**。
   2. 選擇要排除故障的自動化帳戶,並記下名稱。
   3. 選擇**工作**。
   4. 選擇要排除故障的作業。
   5. 在「作業摘要」窗格中,在**作業 ID**中查找 GUID 值。

   ![工作摘要窗格中的工作 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 收集正在運行的腳本的範例。

4. 收集紀錄檔:

   1. 在 Azure 門戶中,轉到**自動化帳戶**。
   2. 選擇要排除故障的自動化帳戶。
   3. 選擇**工作**。
   4. 選擇要排除故障的作業。
   5. 選擇**所有紀錄**。
   6. 在生成的窗格中,收集數據。

   ![「所有紀錄」下列出的資料](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>模組問題的資料

除了[基本數據項](#basic-data)外,還收集以下資訊:

* 您遵循的步驟,以便可以重現問題。
* 任何錯誤消息的屏幕截圖。
* 當前模組及其版本號的螢幕截圖。

## <a name="next-steps"></a>後續步驟

如果您需要更多說明:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
