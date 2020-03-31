---
title: Azure HDInsight 中的阿帕奇安巴里陳舊警報
description: 討論和分析 HDInsight 中 Apache Ambari 陳舊警報的可能原因和解決方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539105"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Ambari 陳舊警報

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

在 Apache Ambari UI 中，您可能會看到如下所示的警報：

![阿帕奇·安巴里陳舊警報示例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

安巴里特工持續監測許多資源的健康。 可以配置*警報*來通知您特定的群集屬性是否在預定閾值內。 每次資源檢查運行後，如果滿足警示準則，Ambari 代理將狀態報表回 Ambari 伺服器並觸發警報。 如果未根據其警報設定檔中的時間間隔檢查警報，伺服器將觸發*Ambari 伺服器過時警報*。

運行狀況檢查可能無法在其定義的時間間隔運行的原因有多種：

* 主機使用量很大（CPU 使用率高），因此 Ambari 代理無法獲得足夠的系統資源以按時運行警報。

* 群集正忙於在負載沉重的時期執行許多作業或服務。

* 群集中的少量主機承載許多元件，因此需要運行許多警報。 如果元件數很大，則警報作業可能會錯過其計畫的時間間隔。

## <a name="resolution"></a>解決方案

請嘗試以下方法解決 Ambari 陳舊警報的問題。

### <a name="increase-the-alert-interval-time"></a>增加警報間隔時間

您可以根據群集的回應時間和負載增加單個警報間隔的值：

1. 在 Apache Ambari UI 中，選擇 **"警報"** 選項卡。
1. 選擇所需的警報定義名稱。
1. 從定義中，選擇 **"編輯**"。
1. 增加**檢查間隔**值，然後選擇 **"保存**"。

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>增加 Ambari 伺服器警報的警報間隔時間

1. 在 Apache Ambari UI 中，選擇 **"警報"** 選項卡。
1. 從 **"組**"下拉清單中，選擇**AMBARI 預設值**。
1. 選擇**Ambari 伺服器警報**。
1. 從定義中，選擇 **"編輯**"。
1. 增加**檢查間隔**值。
1. 增加**間隔乘數**值，然後選擇 **"保存**"。

### <a name="disable-and-reenable-the-alert"></a>禁用並重新啟用警報

要丟棄過期警報，請禁用並重新啟用它：

1. 在 Apache Ambari UI 中，選擇 **"警報"** 選項卡。
1. 選擇所需的警報定義名稱。
1. 從定義中選擇 UI 最右側的 **"已啟用**"。
1. 在 **"確認**"快顯視窗中，選擇 **"確認禁用**"。
1. 等待幾秒鐘，清除頁面上顯示的所有警報"實例"。
1. 從定義中選擇 UI 最右側的 **"禁用**"。
1. 在 **"確認**"快顯視窗中，選擇 **"確認啟用**"。

### <a name="increase-the-alert-grace-period"></a>增加警報寬限期

在 Ambari 代理報告配置的警報未按其計畫之前，有一個寬限期。 如果警報未達到計畫時間，但在寬限期內運行，則不會生成過期警報。

預設值`alert_grace_period`為 5 秒。 您可以在 /etc/ambari 代理/conf/ambari 代理中配置此設置。 對於定期發生過期警報的主機，請嘗試將該值增加到 10。 然後，重新開機 Ambari 代理。

## <a name="next-steps"></a>後續步驟

如果您的問題在這裡未被提及，或者您無法解決，請訪問以下管道之一，獲得更多支援：

* 從[Azure 社區支援](https://azure.microsoft.com/support/community/)的 Azure 專家處獲取答案。

* 在推特上聯繫[@AzureSupport](https://twitter.com/azuresupport)。 這是用於改善客戶體驗的官方 Microsoft Azure 帳戶。 它將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，請從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 要達到此點，請從門戶功能表中選擇"説明 **"**？ **Help + support** 有關詳細資訊，請參閱[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 

  Microsoft Azure 訂閱中包含對訂閱管理和計費的支援。 技術支援可通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)獲得。
