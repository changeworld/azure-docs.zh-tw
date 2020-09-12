---
title: CPU 核心配額增加要求 - Azure HDInsight
description: 了解如何要求增加配置給訂用帳戶的 CPU 核心。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: f8411176e0979bdb894983bcf866abd0e1109e21
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291662"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>要求增加 Azure HDInsight 的配額

CPU 核心配額有助於確保特定 Azure 區域中的所有客戶之間能夠均等使用資源。 但在某些情況下，您有可能因業務而需要比目前配額所允許更多的叢集資源。 在這類情況下，您可以要求增加 CPU 核心配額，以部署符合資料處理需求的叢集。

在達到配額限制後，您即無法部署新的叢集，或藉由新增更多背景工作節點將現有的叢集擴增。 唯一的配額限制是 CPU 核心配額，其存在於每個訂用帳戶區域層級。 例如，您的訂用帳戶在美國東部區域可能有 30 個 CPU 核心的限制，而「美國東部 2」也同樣允許 30 個 CPU 核心。

## <a name="gather-required-information"></a>收集必要資訊

如果您收到指出已達到配額限制的錯誤，請使用本節說明的程序來收集重要資訊，並提交配額增加要求。

1. 確認您所需的叢集 VM 大小、規模和類型。
1. 檢查訂用帳戶目前的配額容量限制。 若要檢查可用的核心，請執行下列步驟：

    1. 登入 [Azure 入口網站](https://portal.azure.com/)。
    1. 瀏覽至 HDInsight 叢集的 [概觀] 頁面。
    1. 在左側功能表上，選取 [配額限制]。 此頁面會顯示使用中的核心數目、可用的核心數目，以及核心總數。

若要要求增加配額，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頁面左下方的 [說明 + 支援]。

    ![說明及支援按鈕](./media/quota-increase-request/help-support-button.png)

1. 選取 [新增支援要求]。
1. 在 [新增支援要求] 頁面的 [基本] 索引標籤上，選取下列選項：

   - **問題類型**：**服務與訂用帳戶限制 (配額)**
   - **訂用帳戶**：您需要修改的訂用帳戶
   - **配額類型**：**HDInsight**

     ![建立支援要求以提高 HDInsight 核心配額](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. 完成時，選取 [下一步:解決方案 >>]。
1. 在 [詳細資料] 頁面中，輸入問題的說明，然後選取問題的嚴重性、您慣用的連絡方法，以及其他必要欄位。 請使用下列範本確實提供必要的資訊。 配額增加要求會由 Azure 容量小組進行評估，而非 HDInsight 產品小組。 您提供的資訊越完整，要求就越有可能獲准。

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![問題詳細資料](./media/quota-increase-request/problem-details.png)

1. 完成時，選取 [下一步:檢閱 + 建立 >>]。
1. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。

> [!NOTE]  
> 如果您需要增加私人區域中的 HDInsight 核心配額，請 [提交核准清單要求](https://aka.ms/canaryintwhitelist)。

您可以[連絡支援人員以要求增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

目前有一些固定的配額限制。 例如，單一 Azure 訂用帳戶最多可以有 10,000 個核心。 如需這些限制的詳細資料，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Hadoop、Spark、Kafka 及其他工具在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何在 HDInsight 中安裝和設定叢集。
* [監視叢集效能](hdinsight-key-scenarios-to-monitor.md)：了解重要情節，以監視可能會影響叢集容量的 HDInsight 叢集。
