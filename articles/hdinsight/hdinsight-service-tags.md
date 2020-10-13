---
title: Azure HDInsight 的網路安全性群組 (NSG) 服務標記
description: 使用 HDInsight 服務標籤，允許來自健康情況和管理服務節點的輸入流量進入您的叢集，而不將 IP 位址新增至您的 Nsg。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 112f915f533627ccdc0ac6efe38caacc80b254bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89399951"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight 的 NSG 服務標記

Azure HDInsight 網路安全性群組的服務標籤 (Nsg) 是健康情況和管理服務的 IP 位址群組。 這些群組有助於將建立安全性規則的複雜性降至最低。 [服務標記](../virtual-network/security-overview.md#service-tags) 允許來自特定 ip 的輸入流量，而不需在 nsg 中輸入每個 [管理 IP 位址](hdinsight-management-ip-addresses.md) 。

HDInsight 服務會管理這些服務標記。 您無法建立自己的服務標記或修改現有的標記。 Microsoft 會管理與服務標記相符的位址前置詞，並隨著位址變更自動更新服務標籤。

如果您想要使用特定區域，且此頁面尚未記載服務標籤，您可以使用 [服務標記探索 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 來尋找您的服務標記。 您也可以下載 [服務標記 JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 檔案，並搜尋您想要的區域。

## <a name="get-started-with-service-tags"></a>開始使用服務標記

您有兩個選項可在網路安全性群組中使用服務標記：

- **使用單一全域 HDInsight 服務**標籤：此選項會開啟您的虛擬網路，以供 HDInsight 服務用來監視所有區域間叢集的所有 IP 位址。 此選項是最簡單的方法，但如果您有嚴格的安全性需求，則可能不適合。

- **使用多個區域服務**標籤：此選項只會將您的虛擬網路開啟至 HDInsight 在該特定區域中使用的 IP 位址。 不過，如果您使用多個區域，則需要將多個服務標記新增至您的虛擬網路。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用單一全域 HDInsight 服務標記

開始搭配 HDInsight 叢集使用服務標記最簡單的方式，就是將全域標記新增 `HDInsight` 至 NSG 規則。

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您的網路安全性群組。

1. 在 [ **設定**] 底下，選取 [ **輸入安全性規則**]，然後選取 [ **+ 新增**]。

1. 從 [ **來源** ] 下拉式清單中，選取 [ **服務標記**]。

1. 從 [ **來源服務標記** ] 下拉式清單中，選取 [ **HDInsight**]。

    ![從 Azure 入口網站新增服務標記](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

此標記包含 HDInsight 可用之所有區域的健康情況和管理服務的 IP 位址。 此標籤可確保您的叢集可以與所需的健康情況和管理服務進行通訊，無論其建立所在位置為何。

## <a name="use-regional-hdinsight-service-tags"></a>使用區域 HDInsight 服務標記

如果全域標記選項無法運作，因為您需要更嚴格的許可權，您可以僅允許適用于您區域的服務標籤。 根據您的叢集建立所在的區域，可能會有多個服務標記。

若要找出要為您的區域新增的服務標籤，請閱讀本文的下列各節。

### <a name="use-a-single-regional-service-tag"></a>使用單一區域服務標記

如果您的叢集位於下表所列的區域中，您只需要將單一區域服務標籤新增至您的 NSG。

| Country | 區域 | 服務標籤 |
| ---- | ---- | ---- |
| 澳大利亞 | 澳大利亞東部 | AustraliaEast |
| &nbsp; | 澳大利亞東南部 | AustraliaSoutheast |
| &nbsp; | 澳大利亞中部 | AustraliaCentral |
| 中國 | 中國東部 2 | ChinaEast2 |
| &nbsp; | 中國北部 2 | ChinaNorth2 |
| 美國 | 美國中北部 | NorthCentralUS |
| &nbsp; | 美國西部 2 | WestUS2 |
| &nbsp; | 美國中西部 | WestCentralUS |
| Canada | 加拿大東部 | CanadaEast |
| 巴西 | 巴西南部 | BrazilSouth |
| 南韓 | 南韓中部 | KoreaCentral |
| &nbsp; | 南韓南部 | KoreaSouth |
| 印度 | 印度中部 | CentralIndia |
| &nbsp; | 印度南部 | SouthIndia |
| 日本 | 日本西部 | JapanWest |
| 法國 | 法國中部| FranceCentral |
| 英國 | 英國南部 | UKSouth |
| Azure Government | US DoD 中部 | USDoDCentral |
| &nbsp; | USGov 德州 | USGovTexas |
| &nbsp; | UsDoD 東部 | USDoDEast |
| &nbsp; | USGov 亞歷桑那 | USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>使用多個區域服務標記

如果您的叢集建立所在的區域未列于上表中，則您需要允許多個區域服務標記。 使用一個以上的需求是因為不同區域的資源提供者相片順序有所差異。

其餘區域會根據其使用的區域服務標籤劃分成群組。

#### <a name="group-1"></a>群組 1

如果您的叢集是在下表中的其中一個區域建立，請允許服務標籤 `HDInsight.WestUS` 和 `HDInsight.EastUS` 。 此外，也會列出區域服務標記。 本節中的區域需要三個服務標記。

例如，如果您的叢集是在區域中建立的 `East US 2` ，您必須將下列服務標籤新增至您的網路安全性群組：

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | 區域 | 服務標籤 |
| ---- | ---- | ---- |
| 美國 | 美國東部 2 | EastUS2 |
| &nbsp; | 美國中部 | CentralUS |
| &nbsp; | NorthCentral 我們 | HDInsight. NorthCentralUS |
| &nbsp; | 美國中南部 | SouthCentralUS |
| &nbsp; | 美國東部 | EastUS |
| &nbsp; | 美國西部 | WestUS |
| 日本 | 日本東部 | JapanEast |
| 歐洲 | 北歐 | NorthEurope |
| &nbsp; | 西歐| WestEurope |
| Asia | 東亞 | EastAsia |
| &nbsp; | 東南亞 | SoutheastAsia |
| 澳大利亞 | 澳大利亞東部 | AustraliaEast |

#### <a name="group-2"></a>群組 2

*中國北部*和*中國東部*區域中的叢集需要允許兩個服務標記： `HDInsight.ChinaNorth` 和 `HDInsight.ChinaEast` 。

#### <a name="group-3"></a>群組 3

*US Gov 愛荷華州*和*US Gov 維吉尼亞州*區域中的叢集需要允許兩個服務標記： `HDInsight.USGovIowa` 和 `HDInsight.USGovVirginia` 。

#### <a name="group-4"></a>群組 4

*德國中部*和*德國東北部*區域中的叢集必須允許兩個服務標記： `HDInsight.GermanyCentral` 和 `HDInsight.GermanyNortheast` 。

## <a name="next-steps"></a>後續步驟

- [網路安全性群組：服務標記](../virtual-network/security-overview.md#security-rules)
- [建立 Azure HDInsight 叢集的虛擬網路](hdinsight-create-virtual-network.md)
