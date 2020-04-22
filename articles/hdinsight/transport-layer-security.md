---
title: Azure HDInsight 中的傳輸層安全性
description: 傳輸層安全 (TLS) 和安全套接字層 (SSL) 是透過電腦網路提供通訊安全性的加密協定。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771959"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight 中的傳輸層安全性

通過公共群集終結點`https://CLUSTERNAME.azurehdinsight.net`連接到 HDInsight 群集的連接通過群集網關節點進行近用。 這些連接使用稱為 TLS 的協議進行保護。 在閘道上強制實施更高版本的 TLS 可提高這些連接的安全性。 有關為什麼要使用較新版本 TLS 的詳細資訊,請參閱[解決 TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)。

默認情況下,Azure HDInsight 群集接受公共 HTTPS 終結點上的 TLS 1.2 連接,以及舊版本,以便向後相容。 在群集創建期間,可以使用 Azure 門戶或資源管理器範本控制網關節點上支援的最小 TLS 版本。 對於門戶,在群集創建期間從 **「安全 + 網路**」選項卡中選擇 TLS 版本。 對於部署時的資源管理器範本,請使用 **「最小支援 TlsVersion」** 屬性。 有關範例樣本,請參閱[HDInsight 最小 TLS 1.2 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 此屬性支援三個值:"1.0"、"1.1"和"1.2",分別對應於 TLS 1.0°、TLS 1.1+ 和 TLS 1.2+。

> [!IMPORTANT]
> 從 2020 年 6 月 30 日開始,Azure HDInsight 將對所有 HTTPS 連接強制實施 TLS 1.2 或更高版本。 我們建議您確保所有用戶端都準備好處理 TLS 1.2 或更高版本。 有關詳細資訊,請參閱[Azure HDInsight TLS 1.2 強制](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)。

## <a name="next-steps"></a>後續步驟

* [為 Azure HDInsight 規劃虛擬網路](./hdinsight-plan-virtual-network-deployment.md)
* [為 Azure HDInsight 叢集建立虛擬網路](hdinsight-create-virtual-network.md)。
* [網路安全群組](../virtual-network/security-overview.md)。
