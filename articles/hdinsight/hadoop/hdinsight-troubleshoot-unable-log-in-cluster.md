---
title: 無法登入 Azure HDInsight 叢集
description: 疑難排解為什麼無法登入 Azure HDInsight 中的 Apache Hadoop 叢集
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894062"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>案例：無法登入 Azure HDInsight 叢集

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

無法登入 Azure HDInsight 叢集。

## <a name="cause"></a>原因

原因可能有所不同。 請記住，當登入叢集或應用程式儀表板時，請使用您的「叢集登入」或 HTTP 認證。 從遠端連線時，請使用您的安全殼層 (SSH) 或遠端桌面認證。

## <a name="resolution"></a>解決方法

若要解決常見的問題，請嘗試下列步驟。

* 請在隱私權模式下，於新瀏覽器索引標籤中嘗試開啟 [叢集儀表板]。

* 如果您不記得您的 SSH 認證，您可以在[AMBARI UI 中重設認證](../hdinsight-administer-use-portal-linux.md#change-passwords)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
