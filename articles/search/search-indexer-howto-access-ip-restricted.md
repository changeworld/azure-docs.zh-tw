---
title: 允許存取索引子 IP 範圍
titleSuffix: Azure Cognitive Search
description: 設定 IP 防火牆規則，以允許 Azure 認知搜尋索引子的資料存取。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0be69b72cc068d017202b0694e24fb4573172dba
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101387"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>設定 IP 防火牆規則，以允許 (Azure 認知搜尋) 的索引子連接

Azure 資源（例如儲存體帳戶、Cosmos DB 帳戶和 Azure SQL Server）上的 IP 防火牆規則，只允許來自特定 IP 範圍的流量存取資料。

本文說明如何透過 Azure 入口網站為儲存體帳戶設定 IP 規則，讓 Azure 認知搜尋索引子可以安全地存取資料。 在 Azure 儲存體的特定情況下，此方法也適用于使用 IP 防火牆規則來保護資料存取的其他 Azure 資源。

> [!NOTE]
> 只有當儲存體帳戶和搜尋服務位於不同區域時，儲存體帳戶的 IP 防火牆規則才會生效。 如果您的安裝程式不允許這樣做，建議您使用 [ [信任的服務例外](search-indexer-howto-access-trusted-service-exception.md)狀況] 選項。

## <a name="get-the-ip-address-of-the-search-service"></a>取得搜尋服務的 IP 位址

取得搜尋服務 (FQDN) 的完整功能變數名稱。 這看起來會像這樣 `<search-service-name>.search.windows.net` 。 您可以查閱 Azure 入口網站上的搜尋服務，以找出 FQDN。

   ![取得服務 FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "取得服務 FQDN")

您可以藉由執行 `nslookup` (或 FQDN 的) 來取得搜尋服務的 IP 位址 `ping` 。 在下列範例中，您會將 "10.50.10.50" 新增至 Azure 儲存體防火牆上的輸入規則。

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>取得 "AzureCognitiveSearch" 服務標記的 IP 位址範圍

額外的 IP 位址會用於源自索引子之 [多租使用者執行環境](search-indexer-securing-resources.md#indexer-execution-environment)的要求。 您可以從服務標記取得此 IP 位址範圍。

服務標籤的 IP 位址範圍 `AzureCognitiveSearch` 可以透過 [探索 API (preview) ](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 或 [可下載的 JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)檔案取得。

針對此逐步解說，假設搜尋服務是 Azure 公用雲端，則應該下載 [Azure 公用 JSON](https://www.microsoft.com/download/details.aspx?id=56519) 檔案。

   ![下載 JSON 檔案](media\search-indexer-howto-secure-access\service-tag.png "下載 JSON 檔案")

從 JSON 檔案中，假設搜尋服務位於美國中西部中，則多租使用者索引子執行環境的 IP 位址清單如下所示。

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

針對/32 IP 位址，請卸載 "/32" (52.253.133.74/32 > 52.253.133.74) ，可以逐字使用其他專案。

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>將 IP 位址範圍新增至 IP 防火牆規則

若要將 IP 位址範圍新增至儲存體帳戶的防火牆規則，最簡單的方式是透過 Azure 入口網站。 在入口網站上找出儲存體帳戶，並流覽至 [ **防火牆和虛擬網路** ] 索引標籤。

   ![防火牆和虛擬網路](media\search-indexer-howto-secure-access\storage-firewall.png "防火牆和虛擬網路")

新增先前為搜尋服務 IP (1 取得的三個 IP 位址，在 `AzureCognitiveSearch` 位址範圍中為服務標籤) 2，然後選取 [ **儲存**]。

   ![防火牆 IP 規則](media\search-indexer-howto-secure-access\storage-firewall-ip.png "防火牆 IP 規則")

防火牆規則需要5-10 分鐘的時間才會更新，在這之後，索引子應該能夠存取儲存體帳戶中的資料。

## <a name="next-steps"></a>後續步驟

- [設定 Azure 儲存體防火牆](../storage/common/storage-network-security.md)
- [設定 Cosmos DB 的 IP 防火牆](../cosmos-db/firewall-support.md)
- [設定 Azure SQL Server 的 IP 防火牆](../azure-sql/database/firewall-configure.md)