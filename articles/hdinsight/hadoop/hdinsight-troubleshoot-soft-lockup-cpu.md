---
title: 來自 Azure HDInsight 群集的看門狗 BUG 軟鎖定 CPU 錯誤
description: 看門狗 BUG 軟鎖定 CPU 出現在 Azure HDInsight 群集的內核系統日誌中
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894113"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>方案："看門狗：BUG：軟鎖定 - CPU"錯誤從 Azure HDInsight 群集

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

內核系統日誌包含錯誤訊息： `watchdog: BUG: soft lockup - CPU`。

## <a name="cause"></a>原因

Linux 內核中的[一](https://bugzilla.kernel.org/show_bug.cgi?id=199437)個錯誤導致 CPU 軟鎖定。

## <a name="resolution"></a>解決方案

應用內核修補程式。 下面的腳本升級linux內核，並在24小時內在不同時間重新開機電腦。 分兩批執行腳本操作。 第一批位於除頭節點之外的所有節點上。 第二批位於頭節點上。 不要同時在頭節點和其他節點上運行。

1. 從 Azure 門戶導航到 HDInsight 群集。

1. 轉到腳本操作。

1. 選擇 **"提交新"** 並輸入輸入，如下所示

    | 屬性 | 值 |
    | --- | --- |
    | 指令碼類型 | -自訂 |
    | 名稱 |修復內核軟鎖問題 |
    | Bash 指令碼 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 節點類型 |工作人員，動物園管理員 |
    | 參數 |N/A |

    如果希望在添加新節點時執行腳本，請選擇 **"保留此腳本操作**"。

1. 選取 [建立]****。

1. 等待執行成功。

1. 按照與步驟 3 相同的步驟在 Head 節點上執行腳本操作，但這次使用節點類型：頭。

1. 等待執行成功。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
