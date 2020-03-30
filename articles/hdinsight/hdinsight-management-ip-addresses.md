---
title: Azure HDInsight 管理 IP 位址
description: 瞭解必須允許來自哪些 IP 位址的 IP 位址，以便正確配置網路安全性群組和使用者定義的路由，以便使用 Azure HDInsight 進行虛擬網路。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 5e8f39b58f258742108fe323d9395efd87bc288f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271820"
---
# <a name="hdinsight-management-ip-addresses"></a>HDInsight 管理 IP 位址

> [!Important]
> 在大多數情況下，您現在可以對網路安全性群組使用[服務標記](hdinsight-service-tags.md)功能，而不是手動添加 IP 位址。 將僅為服務標記添加新區域，靜態 IP 位址最終將被棄用。

如果使用網路安全性群組 （NSG） 或使用者定義的路由 （UDR） 來控制到 HDInsight 群集的入站流量，則必須確保群集可以與關鍵的 Azure 運行狀況和管理服務進行通信。  這些服務的某些 IP 位址特定于區域，其中一些位址適用于所有 Azure 區域。 如果您不是使用自訂 DNS，您可能也需要允許來自 Azure DNS 服務的流量。

以下各節討論必須允許的特定 IP 位址。

## <a name="azure-dns-service"></a>Azure DNS 服務

如果使用 Azure 提供的 DNS 服務，則允許在埠 53 上從__168.63.129.16__進行訪問。 有關詳細資訊，請參閱 VM[和角色實例的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文檔。 如果您使用的是自訂 DNS，請跳過此步驟。

## <a name="health-and-management-services-all-regions"></a>衛生和管理服務：所有地區

允許 Azure HDInsight 運行狀況和管理服務的以下 IP 位址的流量，這些服務適用于所有 Azure 區域：

| 來源 IP 位址 | Destination  | 方向 |
| ---- | ----- | ----- |
| 168.61.49.99 | \*:443 | 輸入 |
| 23.99.5.239 | \*:443 | 輸入 |
| 168.61.48.131 | \*:443 | 輸入 |
| 138.91.141.162 | \*:443 | 輸入 |

## <a name="health-and-management-services-specific-regions"></a>衛生和管理服務：特定區域

允許來自資源所在的特定 Azure 區域中為 Azure HDInsight 運行狀況和管理服務列出的 IP 位址的流量：

> [!IMPORTANT]  
> 如果未列出正在使用的 Azure 區域，則對網路安全性群組使用[服務標記](hdinsight-service-tags.md)功能。

| Country | 區域 | 允許的來源 IP 位址 | 允許的目的地 | 方向 |
| ---- | ---- | ---- | ---- | ----- |
| Asia | 東亞 | 23.102.235.122</br>52.175.38.134 | \*:443 | 輸入 |
| &nbsp; | 東南亞 | 13.76.245.160</br>13.76.136.249 | \*:443 | 輸入 |
| 澳大利亞 | 澳大利亞東部 | 104.210.84.115</br>13.75.152.195 | \*:443 | 輸入 |
| &nbsp; | 澳大利亞東南部 | 13.77.2.56</br>13.77.2.94 | \*:443 | 輸入 |
| 巴西 | 巴西南部 | 191.235.84.104</br>191.235.87.113 | \*:443 | 輸入 |
| Canada | 加拿大東部 | 52.229.127.96</br>52.229.123.172 | \*:443 | 輸入 |
| &nbsp; | 加拿大中部 | 52.228.37.66</br>52.228.45.222 |\*: 443 | 輸入 |
| 中國 | 中國北部 | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*:443 | 輸入 |
| &nbsp; | 中國東部 | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*:443 | 輸入 |
| &nbsp; | 中國北部 2 | 40.73.37.141</br>40.73.38.172 | \*:443 | 輸入 |
| &nbsp; | 中國東部 2 | 139.217.227.106</br>139.217.228.187 | \*:443 | 輸入 |
| 歐洲 | 北歐 | 52.164.210.96</br>13.74.153.132 | \*:443 | 輸入 |
| &nbsp; | 西歐| 52.166.243.90</br>52.174.36.244 | \*:443 | 輸入 |
| 法國 | 法國中部| 20.188.39.64</br>40.89.157.135 | \*:443 | 輸入 |
| 德國 | 德國中部 | 51.4.146.68</br>51.4.146.80 | \*:443 | 輸入 |
| &nbsp; | 德國東北部 | 51.5.150.132</br>51.5.144.101 | \*:443 | 輸入 |
| 印度 | 印度中部 | 52.172.153.209</br>52.172.152.49 | \*:443 | 輸入 |
| &nbsp; | 印度南部 | 104.211.223.67<br/>104.211.216.210 | \*:443 | 輸入 |
| 日本 | 日本東部 | 13.78.125.90</br>13.78.89.60 | \*:443 | 輸入 |
| &nbsp; | 日本西部 | 40.74.125.69</br>138.91.29.150 | \*:443 | 輸入 |
| 南韓 | 南韓中部 | 52.231.39.142</br>52.231.36.209 | \*:443 | 輸入 |
| &nbsp; | 南韓南部 | 52.231.203.16</br>52.231.205.214 | \*:443 | 輸入
| United Kingdom | 英國西部 | 51.141.13.110</br>51.141.7.20 | \*:443 | 輸入 |
| &nbsp; | 英國南部 | 51.140.47.39</br>51.140.52.16 | \*:443 | 輸入 |
| 美國 | 美國中部 | 13.89.171.122</br>13.89.171.124 | \*:443 | 輸入 |
| &nbsp; | 美國東部 | 13.82.225.233</br>40.71.175.99 | \*:443 | 輸入 |
| &nbsp; | 美國中北部 | 157.56.8.38</br>157.55.213.99 | \*:443 | 輸入 |
| &nbsp; | 美國中西部 | 52.161.23.15</br>52.161.10.167 | \*:443 | 輸入 |
| &nbsp; | 美國西部 | 13.64.254.98</br>23.101.196.19 | \*:443 | 輸入 |
| &nbsp; | 美國西部 2 | 52.175.211.210</br>52.175.222.222 | \*:443 | 輸入 |
| &nbsp; | 阿拉伯聯合大公國北部 | 65.52.252.96</br>65.52.252.97 | \*:443 | 輸入 |

如需用於 Azure Government 之 IP 位址的資訊，請參閱 [Azure Government Intelligence + Analytics](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) 文件。

有關詳細資訊，請參閱[控制網路流量](hdinsight-plan-virtual-network-deployment.md#networktraffic)部分。

如果使用使用者定義的路由 （UDR），則應指定路由並允許從虛擬網路到上述 IP 的出站流量，下一個躍點設置為"Internet"。

## <a name="next-steps"></a>後續步驟

* [為 Azure HDInsight 群集創建虛擬網路](hdinsight-create-virtual-network.md)
* [Azure HDInsight 的網路安全性群組 （NSG） 服務標記](hdinsight-service-tags.md)
