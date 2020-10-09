---
title: Azure HDInsight 中的 'hbase hbck' 命令逾時
description: 修正區域指派時，' hbase hbck ' 命令發生超時問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887184"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 ' hbase hbck ' 命令逾時

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

`hbase hbck`修正區域指派時，命令會發生超時。

## <a name="cause"></a>原因

使用 `hbck` 命令時所發生的逾時問題，可能是由於數個區域長時間處於「轉換中」狀態所造成。 您可以在 HBase Master UI 中看到這些區域處於離線狀態。 由於有大量的區域嘗試轉換，HBase Master 可能會過期，而且無法讓這些區域重新上線。

## <a name="resolution"></a>解決方案

1. 使用 SSH 登入 HDInsight HBase 叢集。

1. 執行 `hbase zkcli` 命令以使用 Apache ZooKeeper shell 進行連接。

1. 執行 `rmr /hbase/regions-in-transition` 或 `rmr /hbase-unsecure/regions-in-transition` 命令。

1. `hbase zkcli`使用命令從 shell 結束 `exit` 。

1. 從 Apache Ambari UI 中，重新開機 Active HBase Master 服務。

1. 執行 `hbase hbck -fixAssignments` 命令。

1. 監視 HBase Master UI 「轉換中的區域」這一節，以確定不會停滯任何區域。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

- 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

- 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
