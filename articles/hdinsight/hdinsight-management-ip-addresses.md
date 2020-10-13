---
title: Azure HDInsight 管理 IP 位址
description: 瞭解您必須允許輸入流量的 IP 位址，才能使用 Azure HDInsight 適當地設定虛擬網路的網路安全性群組和使用者定義的路由。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: f9e52d931f8873cebf42534fd6bf03b144e61e23
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974663"
---
# <a name="hdinsight-management-ip-addresses"></a>HDInsight 管理 IP 位址

本文列出 Azure HDInsight 健康情況和管理服務所使用的 IP 位址。 如果您使用網路安全性群組 (Nsg) 或使用者定義路由 (Udr) 您可能需要將其中一些 IP 位址新增至輸入網路流量的允許清單。

## <a name="introduction"></a>簡介
 
> [!Important]
> 在大多數情況下，您現在可以使用網路安全性群組的 [服務](hdinsight-service-tags.md) 標籤，而不是手動新增 IP 位址。 系統將不會為新的 Azure 區域發佈 IP 位址，而且只會有已發佈的服務標記。 管理 IP 位址的靜態 IP 位址最終將會被取代。

如果您使用網路安全性群組 (Nsg) 或使用者定義的路由 (Udr) 控制您 HDInsight 叢集的輸入流量，您必須確定您的叢集可以與重要的 Azure 健康狀態和管理服務通訊。  這些服務的部分 IP 位址是區域專屬的，有些則適用于所有 Azure 區域。 如果您不是使用自訂 DNS，您可能也需要允許來自 Azure DNS 服務的流量。

如果您需要此處未列出之區域的 IP 位址，您可以使用 [服務標記探索 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 來尋找您區域的 ip 位址。 如果您無法使用 API，請下載 [服務標記 JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 檔案，並搜尋您想要的區域。

下列各節將討論必須允許的特定 IP 位址。

## <a name="azure-dns-service"></a>Azure DNS 服務

如果您使用 Azure 提供的 DNS 服務，允許在埠53上從 __168.63.129.16__ 存取。 如需詳細資訊，請參閱 [vm 的名稱解析和角色實例](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 檔。 如果您是使用自訂 DNS，請略過此步驟。

## <a name="health-and-management-services-all-regions"></a>健全狀況和管理服務：所有區域

允許來自下列 IP 位址的流量，以適用于所有 Azure 區域的 Azure HDInsight 健康情況和管理服務：

| 來源 IP 位址 | 目的地  | 方向 |
| ---- | ----- | ----- |
| 168.61.49.99 | \*：443 | 連入 |
| 23.99.5.239 | \*：443 | 連入 |
| 168.61.48.131 | \*：443 | 連入 |
| 138.91.141.162 | \*：443 | 輸入 |

## <a name="health-and-management-services-specific-regions"></a>健全狀況和管理服務：特定區域

在您的資源所在的特定 Azure 區域中，允許針對 Azure HDInsight 健康情況和管理服務列出的 IP 位址的流量：

> [!IMPORTANT]  
> 如果未列出您使用的 Azure 區域，請使用網路安全性群組的 [服務標記](hdinsight-service-tags.md) 功能。

| Country | 區域 | 允許的來源 IP 位址 | 允許的目的地 | Direction |
| ---- | ---- | ---- | ---- | ----- |
| Asia | 東亞 | 23.102.235.122</br>52.175.38.134 | \*：443 | 輸入 |
| &nbsp; | 東南亞 | 13.76.245.160</br>13.76.136.249 | \*：443 | 輸入 |
| 澳大利亞 | 澳大利亞東部 | 104.210.84.115</br>13.75.152.195 | \*：443 | 輸入 |
| &nbsp; | 澳大利亞東南部 | 13.77.2.56</br>13.77.2.94 | \*：443 | 輸入 |
| 巴西 | 巴西南部 | 191.235.84.104</br>191.235.87.113 | \*：443 | 輸入 |
| Canada | 加拿大東部 | 52.229.127.96</br>52.229.123.172 | \*：443 | 輸入 |
| &nbsp; | 加拿大中部 | 52.228.37.66</br>52.228.45.222 |\*：443 | 輸入 |
| 中國 | 中國北部 | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*：443 | 輸入 |
| &nbsp; | 中國東部 | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*：443 | 輸入 |
| &nbsp; | 中國北部 2 | 40.73.37.141</br>40.73.38.172 | \*：443 | 輸入 |
| &nbsp; | 中國東部 2 | 139.217.227.106</br>139.217.228.187 | \*：443 | 輸入 |
| 歐洲 | 北歐 | 52.164.210.96</br>13.74.153.132 | \*：443 | 輸入 |
| &nbsp; | 西歐| 52.166.243.90</br>52.174.36.244 | \*：443 | 輸入 |
| 法國 | 法國中部| 20.188.39.64</br>40.89.157.135 | \*：443 | 輸入 |
| 德國 | 德國中部 | 51.4.146.68</br>51.4.146.80 | \*：443 | 輸入 |
| &nbsp; | 德國東北部 | 51.5.150.132</br>51.5.144.101 | \*：443 | 輸入 |
| 印度 | 印度中部 | 52.172.153.209</br>52.172.152.49 | \*：443 | 輸入 |
| &nbsp; | 印度南部 | 104.211.223.67<br/>104.211.216.210 | \*：443 | 輸入 |
| 日本 | 日本東部 | 13.78.125.90</br>13.78.89.60 | \*：443 | 輸入 |
| &nbsp; | 日本西部 | 40.74.125.69</br>138.91.29.150 | \*：443 | 輸入 |
| 南韓 | 南韓中部 | 52.231.39.142</br>52.231.36.209 | \*：443 | 輸入 |
| &nbsp; | 南韓南部 | 52.231.203.16</br>52.231.205.214 | \*：443 | 輸入
| 英國 | 英國西部 | 51.141.13.110</br>51.141.7.20 | \*：443 | 輸入 |
| &nbsp; | 英國南部 | 51.140.47.39</br>51.140.52.16 | \*：443 | 輸入 |
| 美國 | 美國中部 | 13.89.171.122</br>13.89.171.124 | \*：443 | 輸入 |
| &nbsp; | 美國東部 | 13.82.225.233</br>40.71.175.99 | \*：443 | 輸入 |
| &nbsp; | 美國中北部 | 157.56.8.38</br>157.55.213.99 | \*：443 | 輸入 |
| &nbsp; | 美國中西部 | 52.161.23.15</br>52.161.10.167 | \*：443 | 輸入 |
| &nbsp; | 美國西部 | 13.64.254.98</br>23.101.196.19 | \*：443 | 輸入 |
| &nbsp; | 美國西部 2 | 52.175.211.210</br>52.175.222.222 | \*：443 | 輸入 |
| &nbsp; | 阿拉伯聯合大公國北部 | 65.52.252.96</br>65.52.252.97 | \*：443 | 輸入 |
| &nbsp; | 阿拉伯聯合大公國中部 | 20.37.76.96</br>20.37.76.99 | \*：443 | 輸入 |

如需用於 Azure Government 之 IP 位址的資訊，請參閱 [Azure Government Intelligence + Analytics](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) 文件。

如需詳細資訊，請參閱 [控制網路流量](./control-network-traffic.md)。

如果您 (Udr) 使用使用者定義的路由，則應該指定路由，並允許從虛擬網路到上述 Ip 的輸出流量，並將下一個躍點設定為 [網際網路]。

## <a name="next-steps"></a>後續步驟

* [建立 Azure HDInsight 叢集的虛擬網路](hdinsight-create-virtual-network.md)
* [Azure HDInsight 的網路安全性群組 (NSG) 服務標記](hdinsight-service-tags.md)
