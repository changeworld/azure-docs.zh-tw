---
title: Azure HDInsight 中的傳輸層安全性
description: 傳輸層安全性（TLS）和安全通訊端層（SSL）是一種密碼編譯通訊協定，可透過電腦網路提供通訊安全性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183495"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight 中的傳輸層安全性

透過公用叢集端點連線到 HDInsight 叢集的方式 `https://CLUSTERNAME.azurehdinsight.net` ，會經由叢集閘道節點進行代理。 這些連接是使用稱為 TLS 的通訊協定來保護。 在閘道上強制執行較高版本的 TLS 可改善這些連線的安全性。 如需有關為何要使用較新版本 TLS 的詳細資訊，請參閱[解決 tls 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)。

根據預設，Azure HDInsight 叢集會接受公用 HTTPS 端點上的 TLS 1.2 連線，以及較舊版本的回溯相容性。 您可以使用 Azure 入口網站或 Resource Manager 範本，在叢集建立期間控制閘道節點上支援的最低 TLS 版本。 針對入口網站，在叢集建立期間，從 [**安全性 + 網路**] 索引標籤選取 TLS 版本。 如需部署期間的 Resource Manager 範本，請使用**minSupportedTlsVersion**屬性。 如需範例範本，請參閱[HDInsight 最低 TLS 1.2 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 此屬性支援三個值：分別對應到 TLS 1.0 +、TLS 1.1 + 和 TLS 1.2 + 的 "1.0"、"1.1" 和 "1.2"。

> [!IMPORTANT]
> 從2020年6月30日開始，Azure HDInsight 將會強制執行所有 HTTPS 連線的 TLS 1.2 或更新版本。 我們建議您確定您的所有用戶端都已準備好處理 TLS 1.2 或更新版本。 如需詳細資訊，請參閱[AZURE HDINSIGHT TLS 1.2 強制](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)。

## <a name="next-steps"></a>後續步驟

* [為 Azure HDInsight 規劃虛擬網路](./hdinsight-plan-virtual-network-deployment.md)
* [建立 Azure HDInsight 叢集的虛擬網路](hdinsight-create-virtual-network.md)。
* [網路安全性群組](../virtual-network/security-overview.md)。
