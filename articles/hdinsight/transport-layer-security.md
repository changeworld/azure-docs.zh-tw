---
title: Azure HDInsight 中的傳輸層安全性
description: 傳輸層安全性 (TLS) 和安全通訊端層 (SSL) 是透過電腦網路提供通訊安全性的密碼編譯通訊協定。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 6e46cca28c049a794db617797d5f09e9f3b5720d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006886"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight 中的傳輸層安全性

透過公用叢集端點連接至 HDInsight 叢集， `https://CLUSTERNAME.azurehdinsight.net` 是透過叢集閘道節點進行 proxy 處理。 這些連接是使用稱為 TLS 的通訊協定來保護。 在閘道上強制較高版本的 TLS 可改善這些連線的安全性。 如需有關為何應該使用較新的 TLS 版本的詳細資訊，請參閱 [解決 tls 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)。

根據預設，Azure HDInsight 叢集會接受公用 HTTPS 端點上的 TLS 1.2 連線，以及舊版以提供回溯相容性。 您可以使用 Azure 入口網站或 Resource Manager 範本，在叢集建立期間控制閘道節點所支援的最低 TLS 版本。 針對入口網站，在叢集建立期間選取 [ **安全性 + 網路** ] 索引標籤中的 TLS 版本。 針對在部署階段的 Resource Manager 範本，請使用 **minSupportedTlsVersion** 屬性。 如需範例範本，請參閱 [HDInsight 最小的 TLS 1.2 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 這個屬性支援三個值：分別對應至 TLS 1.0 +、TLS 1.1 + 和 TLS 1.2 + 的 "1.0"、"1.1" 和 "1.2"。


## <a name="next-steps"></a>接下來的步驟

* [為 Azure HDInsight 規劃虛擬網路](./hdinsight-plan-virtual-network-deployment.md)
* [建立 Azure HDInsight 叢集的虛擬網路](hdinsight-create-virtual-network.md)。
* [網路安全性群組](../virtual-network/security-overview.md)。
