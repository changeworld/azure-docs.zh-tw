---
title: Azure HDInsight 中的 'hbase hbck' 命令逾時
description: 修復區域分配時，"hbase hbck"命令的超時問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887184"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中使用"hbase hbck"命令逾時

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

修復區域分配時遇到`hbase hbck`命令逾時。

## <a name="cause"></a>原因

使用 `hbck` 命令時所發生的逾時問題，可能是由於數個區域長時間處於「轉換中」狀態所造成。 您可以在 HBase Master UI 中看到這些區域處於離線狀態。 由於大量區域正在嘗試過渡，因此 HBase Master 可能會超時，無法將這些區域重新連線。

## <a name="resolution"></a>解決方案

1. 使用 SSH 登錄到 HDInsight HBase 群集。

1. 運行`hbase zkcli`命令以連接到 Apache 動物園守護者外殼。

1. 運行`rmr /hbase/regions-in-transition`或`rmr /hbase-unsecure/regions-in-transition`命令。

1. 使用`exit`命令`hbase zkcli`退出 shell。

1. 從 Apache Ambari UI 重新開機活動 HBase 主服務。

1. 執行 `hbase hbck -fixAssignments` 命令。

1. 監視 HBase 主 UI"過渡區域"該部分，以確保沒有區域被卡住。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

- 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

- 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
