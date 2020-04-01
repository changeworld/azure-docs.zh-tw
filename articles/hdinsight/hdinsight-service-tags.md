---
title: Azure HDInsight 的網路安全群組 (NSG) 服務標記
description: 使用 HDInsight 服務標記允許從運行狀況和管理服務節點向叢集的入站流量,而無需向 NSG 添加 IP 位址。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: 34ec05a8362f5947cb61924b19c6b1a52e5d91a4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437681"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight 的 NSG 服務標記

用於網路安全組 (NSG) 的 Azure HDInsight 服務標記是運行狀況和管理服務的 IP 位址組。 這些組有助於將安全規則創建的複雜性降至最低。 [服務標記](../virtual-network/security-overview.md#service-tags)提供一種替代方法,用於允許來自特定 IP 位址的入站流量,而無需輸入 NSG 中的每個[管理 IP 位址](hdinsight-management-ip-addresses.md)。

HDInsight 服務管理這些服務標記。 不能創建自己的服務標記或修改現有標記。 Microsoft 管理與服務標記匹配的位址首碼,並在位址更改時自動更新服務標記。

## <a name="get-started-with-service-tags"></a>開始使用服務標記

在網路安全群組中使用服務標記有兩個選項:

- **使用單一個全域 HDInsight 服務標記**:此選項將虛擬網路開啟到 HDInsight 服務用於監視所有區域群集的所有 IP 位址。 此選項是最簡單的方法,但如果有限制性的安全要求,則可能不合適。

- **使用多個區域服務標記**:此選項將虛擬網路僅打開 HDInsight 在特定區域使用的 IP 位址。 但是,如果您使用的是多個區域,則需要向虛擬網路添加多個服務標記。

## <a name="use-a-single-global-hdinsight-service-tag"></a>使用單一個全域 HDInsight 服務標籤

開始將服務標記與 HDInsight 群集一起使用的最簡單方法是將全域標`HDInsight`記 添加到 NSG 規則。

1. 從[Azure 門戶](https://portal.azure.com/)中選擇網路安全組。

1. 在 **「設定」** 下,選擇 **「入站安全規則**」,然後選擇 **「添加**」 。。

1. 從 **「來源**」 下拉清單中, 選擇**服務標記**。

1. 從 **「源服務標籤**」 下拉清單中,選擇**HDInsight**。

    ![從 Azure 門戶新增服務標記](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

此標記包含所有可用 HDInsight 的區域的運行狀況和管理服務的 IP 位址。 該標記將確保群集可以與必要的運行狀況和管理服務通信,無論在何處創建。

## <a name="use-regional-hdinsight-service-tags"></a>使用區域 HDInsight 服務標籤

如果全域標記選項由於需要更嚴格的許可權而不起作用,則只能允許適用於您所在地區的服務標記。 可能有一個、兩個或三個適用的服務標記,具體取決於創建群集的區域。

要瞭解要為區域添加的服務標記,請閱讀本文的以下部分。

### <a name="use-a-single-regional-service-tag"></a>使用單一區域服務標記

如果您更喜歡使用區域服務標記,並且群集位於此表中列出的區域之一,則只需向網路安全組添加單個區域服務標記。

| Country | 區域 | 服務標籤 |
| ---- | ---- | ---- |
| 澳大利亞 | 澳大利亞東部 | HDInsight.澳大利亞東部 |
| &nbsp; | 澳大利亞東南部 | HDInsight.澳大利亞東南 |
| &nbsp; | 澳大利亞中部 | HDInsight.澳大利亞中部 |
| 中國 | 中國東部 2 | HDInsight.中國東方2 |
| &nbsp; | 中國北部 2 | HDInsight.中國北2 |
| 美國 | 美國中北部 | HDInsight.北中 |
| &nbsp; | 美國西部 2 | HDInsight.WestUS2 |
| &nbsp; | 美國中西部 | HDInsight.西中環 |
| Canada | 加拿大東部 | HDInsight.加拿大東部 |
| 巴西 | 巴西南部 | HDInsight.巴西南方 |
| 南韓 | 南韓中部 | HDInsight.韓國中央 |
| &nbsp; | 南韓南部 | HDInsight.韓國 |
| 印度 | 印度中部 | HDInsight.印度中部 |
| &nbsp; | 印度南部 | HDInsight.南印度 |
| 日本 | 日本西部 | HDInsight.日本西部 |
| 法國 | 法國中部| HDInsight.法國中央 |
| 英國 | 英國南部 | HDInsight.UKSouth |
| Azure Government | USDoD 中心 | HDInsight.USDoDCentral |
| &nbsp; | 美國政府德克薩斯州 | HDInsight.USGov德克薩斯 |
| &nbsp; | 烏斯多德東 | HDInsight.USDoDEast |
| &nbsp; | 美國政府亞利桑那州 | HDInsight.USGov亞利桑那 |

### <a name="use-multiple-regional-service-tags"></a>使用多個區域服務標記

如果您更喜歡使用區域服務標記,但創建群集的區域未列在上表中,則需要允許多個區域服務標記。 需要使用一個以上是由於各區域的資源提供者的安排不同。

其餘區域根據其使用的區域服務標記分為多個組。

#### <a name="group-1"></a>群組 1

如果您的群集是在下表中的一個區域中創建的,則允許服務標記`HDInsight.WestUS``HDInsight.EastUS`以及 列出的區域服務標記。 本節中的區域需要三個服務標記。

例如,如果群集是在`East US 2`該區域中創建的,則需要向網路安全組添加以下服務標記:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | 區域 | 服務標籤 |
| ---- | ---- | ---- |
| 美國 | 美國東部 2 | HDInsight.EastUS2 |
| &nbsp; | 美國中部 | HDInsight.中央 |
| &nbsp; | 美國中北部 | HDInsight。 北中 |
| &nbsp; | 美國中南部 | HDInsight.南中烏斯 |
| &nbsp; | 美國東部 | HDInsight.EastUS |
| &nbsp; | 美國西部 | HDInsight.WestUS |
| 日本 | 日本東部 | HDInsight.日本東方 |
| 歐洲 | 北歐 | HDInsight.北歐 |
| &nbsp; | 西歐| HDInsight.西歐 |
| Asia | 東亞 | HDInsight.東亞 |
| &nbsp; | 東南亞 | HDInsight.東南亞 |
| 澳大利亞 | 澳大利亞東部 | HDInsight.澳大利亞東部 |

#### <a name="group-2"></a>群組 2

*華北*與*華東*地區的群集需要允許兩個服務標籤`HDInsight.ChinaNorth`:`HDInsight.ChinaEast`和 。

#### <a name="group-3"></a>群組 3

*美國愛荷華州州長*和*弗吉尼亞州州長*地區的集群需要允許兩個`HDInsight.USGovIowa`服務`HDInsight.USGovVirginia`標籤: 和 。

#### <a name="group-4"></a>群組 4

*在德國中部*和*德國東北部*地區的群集需要允許兩個服務`HDInsight.GermanyCentral`標`HDInsight.GermanyNortheast`記: 和 。

## <a name="next-steps"></a>後續步驟

- [網路安全組:服務標記](../virtual-network/security-overview.md#security-rules)
- [為 Azure HDInsight 叢集建立虛擬網路](hdinsight-create-virtual-network.md)
