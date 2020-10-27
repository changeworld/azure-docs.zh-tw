---
title: REST API 在 Azure HDInsight 中查詢 Apache HBase
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 361eefa9b0dce811533bb4da9c236c207e31c61c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547872"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API 在 Azure HDInsight 中查詢 Apache HBase

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

使用 Apache HBase REST 介面查詢預設值以外的命名空間下的資料表，會導致執行階段錯誤 (HTTP 狀態 500) 。

## <a name="cause"></a>原因

只有在使用預設的命名空間時，才支援 HBase REST API。 這是有關使用 HBase 命名空間的已知問題，或對具有 HDInsight 上 REST 伺服器之資料行系列的資料行進行參考的已知問題。 這是因為 HDInsight 閘道有安全性問題。 使用 API 建立具有命名空間的資料表時，透過資料行系列存取資料行時，您需要指定 `:` 字元，這會被視為 IIS 閘道模組中的安全性問題。

## <a name="mitigation"></a>降低

將您的應用程式部署在與 HDInsight 叢集位於相同 Azure VNet 中的 VM 上，以略過閘道/REST 伺服器。 然後，您可以直接透過 RPC 與 HBase 通訊 (略過 REST 伺服器) ，或在背景工作節點上執行的個別 REST 伺服器略過 HDInsight 閘道。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。