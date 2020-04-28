---
title: Azure HDInsight 中區域錯誤的容錯網域不足
description: 叢集建立失敗，因為 Azure HDInsight 的區域中沒有足夠的容錯網域
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8be7e05ac85ce0b1b10edf18d3885a07e016b9ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895019"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>案例：叢集建立失敗， `not sufficient fault domains in region`因為 Azure HDInsight

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

接收類似于嘗試建立`not sufficient fault domains in region` Apache Kafka 叢集時的錯誤訊息。

## <a name="cause"></a>原因

容錯網域是 Azure 資料中心內基礎硬體的邏輯群組。 每個容錯網域會共用通用電源和網路交換器。 實作 HDInsight 叢集內節點的虛擬機器和受控磁碟會分散於這些容錯網域。 此架構會限制實體硬體故障的潛在影響。

每個 Azure 區域有特定數目的容錯網域。 如需網域的清單及其包含的容錯網域數目，請參閱[可用性設定組](../../virtual-machines/windows/manage-availability.md)上的檔。

在 HDInsight 中，Kafka 叢集必須布建在至少有三個容錯網域的區域中。

## <a name="resolution"></a>解決方法

如果您想要建立叢集的區域沒有足夠的容錯網域，請與產品小組聯繫，以允許布建叢集，即使沒有三個容錯網域也一樣。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
