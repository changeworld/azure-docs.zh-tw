---
title: Azure HDInsight 的網路安全性群組（NSG）服務標記
description: 使用 HDInsight 服務標籤，允許從 HDInsight 健全狀況和管理服務節點到您叢集的輸入流量，而不需明確地將 IP 位址新增到您的網路安全性群組。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/05/2019
ms.openlocfilehash: 24ecf90c2ffc88415afbf84f54af3efa7d5f4a39
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435464"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight 的網路安全性群組（NSG）服務標記

適用于網路安全性群組（Nsg）的 HDInsight 服務標記是健全狀況和管理服務的 IP 位址群組。 這些群組有助於降低建立安全性規則的複雜性。 [服務](../virtual-network/security-overview.md#service-tags)標籤提供一種替代方法，可允許來自特定 IP 位址的輸入流量，而不需輸入網路安全性群組中的每個[管理 IP 位址](hdinsight-management-ip-addresses.md)。

這些服務標記是由 HDInsight 服務所建立和管理。 您無法建立自己的服務標籤，或修改現有的標記。 Microsoft 會管理符合服務標籤的位址前置詞，並在位址變更時自動更新服務標記。

## <a name="getting-started-with-service-tags"></a>服務標記入門

您有兩個選項可在您的網路安全性群組中使用服務標籤：

1. 使用單一 HDInsight 服務標籤-此選項會將您的虛擬網路開啟至 HDInsight 服務用來監視所有區域之叢集的所有 IP 位址。 此選項是最簡單的方法，但如果您有嚴格的安全性需求，則可能不適合。

1. 使用多個區域服務標籤-此選項只會將您的虛擬網路開啟至該特定區域中 HDInsight 使用的 IP 位址。 不過，如果您使用多個區域，則必須將多個服務標籤新增至您的虛擬網路。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用單一全域 HDInsight 服務標記

開始搭配 HDInsight 叢集使用服務標籤的最簡單方式，就是將全域標記 `HDInsight` 新增至網路安全性群組規則。

1. 從 [ [Azure 入口網站](https://portal.azure.com/)] 中，選取您的網路安全性群組。

1. 在 [**設定**] 底下，選取 [**輸入安全性規則**]，然後選取 [ **+ 新增**]。

1. 從 [**來源**] 下拉式清單中，選取 [**服務標記**]。

1. 從 [**來源服務標記**] 下拉式清單中，選取 [ **HDInsight**]。

    ![Azure 入口網站新增服務標記](./media/hdinisght-service-tags/azure-portal-add-service-tag.png)

此標籤包含適用于 HDInsight 的所有區域之健全狀況和管理服務的 IP 位址，並確保您的叢集可以與所需的健康狀態和管理服務進行通訊，而不論其建立位置為何。

## <a name="use-regional-hdinsight-service-tags"></a>使用區域 HDInsight 服務標籤

如果選項1無法使用，因為您需要更嚴格的許可權，您可以只允許適用于您的區域的服務標記。 適用的服務標籤可能是一個、兩個或三個服務標籤，視您叢集的建立區域而定。

若要找出要為您的區域新增哪些服務標籤，請閱讀檔的下列各節。

### <a name="use-a-single-regional-service-tag"></a>使用單一區域服務標記

如果您偏好使用服務標籤選項2，且您的叢集位於此表格中所列的其中一個區域，則您只需要將單一區域服務標籤新增至您的網路安全性群組。

| 國家/地區 | 地區 | 服務標記 |
| ---- | ---- | ---- |
| 澳洲 | 澳大利亞東部 | AustraliaEast |
| &nbsp; | 澳大利亞東南部 | AustraliaSoutheast |
| &nbsp; | 澳大利亞中部 | AustraliaCentral |
| 中國 | 中國東部 2 | ChinaEast2 |
| &nbsp; | 中國北部 2 | ChinaNorth2 |
| 美國 | 美國中北部 | NorthCentralUS |
| &nbsp; | 美國西部 2 | WestUS2 |
| &nbsp; | 美國中西部 | WestCentralUS |
| 加拿大 | 加拿大東部 | CanadaEast |
| 巴西 | 巴西南部 | BrazilSouth |
| 南韓 | 南韓中部 | KoreaCentral |
| &nbsp; | 南韓南部 | KoreaSouth |
| 印度 | 印度中部 | CentralIndia |
| &nbsp; | 印度南部 | SouthIndia |
| 日本 | 日本西部 | JapanWest |
| 法國 | 法國中部| FranceCentral |
| 英國 | 英國南部 | UKSouth |
| Azure 政府機構 | USDoD 中部   | USDoDCentral |
| &nbsp; | 美國政府德克薩斯州 | USGovTexas |
| &nbsp; | UsDoD 東部 | USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>使用多個區域服務標記

如果您偏好使用服務標籤選項2，而且未列出您的叢集建立所在的區域，則您需要允許多個區域服務標記。 使用一個以上的需求，是因為不同區域的資源提供者的相片順序有所差異。

其餘的區域會根據其使用的區域服務標籤分成群組。

#### <a name="group-1"></a>群組 1

如果您的叢集是在下表的其中一個區域中建立，除了列出的地區服務標籤之外，允許 `HDInsight.WestUS` 和 `HDInsight.EastUS` 的服務標記。 本節中的區域需要三個服務標記。

例如，如果您的叢集是在 `East US 2` 區域中建立，則您必須將下列服務標籤新增至您的網路安全性群組：

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| 國家/地區 | 地區 | 服務標記 |
| ---- | ---- | ---- |
| 美國 | 美國東部 2 | EastUS2 |
| &nbsp; | 美國中部 | CentralUS |
| &nbsp; | NorthCentral 我們 | HDInsight. NorthCentralUS |
| &nbsp; | 美國中南部 | SouthCentralUS |
| &nbsp; | 美國東部 | EastUS |
| &nbsp; | 美國西部 | WestUS |
| 日本 | 日本東部 | JapanEast |
| 歐洲 | 歐洲北部 | NorthEurope |
| &nbsp; | 歐洲西部| WestEurope |
| 亞洲 | 東亞 | EastAsia |
| &nbsp; | 東南亞 | SoutheastAsia |
| 澳洲 | 澳大利亞東部 | AustraliaEast |

#### <a name="group-2"></a>群組 2

**中國北部**和**中國東部**區域中的叢集需要允許兩個服務標記： `HDInsight.ChinaNorth` 和 `HDInsight.ChinaEast`。

#### <a name="group-3"></a>群組 3

**US Gov 愛荷華州**和**US Gov 維吉尼亞州**區域中的叢集需要允許兩個服務標記： `HDInsight.USGovIowa` 和 `HDInsight.USGovVirginia`。

#### <a name="group-4"></a>群組 4

位於**德國中部**和**德國東北部**區域的叢集需要允許兩個服務標記： `HDInsight.GermanyCentral` 和 `HDInsight.GermanyNorthEast`。

## <a name="next-steps"></a>後續步驟

- [網路安全性群組-服務標籤](../virtual-network/security-overview.md#security-rules)
- [建立 Azure HDInsight 叢集的虛擬網路](hdinsight-create-virtual-network.md)
