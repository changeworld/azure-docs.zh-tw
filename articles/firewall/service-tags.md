---
title: Azure 防火牆服務標籤概觀
description: 服務標籤表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031574"
---
# <a name="azure-firewall-service-tags"></a>Azure 防火牆服務標籤

服務標籤表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。 您無法建立自己的服務標籤，或指定標籤中包含哪些 IP 位址。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

防火牆服務標籤可以用於網路規則目的地的欄位。 可以使用這些標籤來取代特定的 IP 位址。

## <a name="supported-service-tags"></a>支援的服務標籤

如需可在 Azure 防火牆網路規則中使用的服務標記清單，請參閱 [虛擬網路服務](../virtual-network/service-tags-overview.md#available-service-tags) 標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 防火牆規則，請參閱 [Azure 防火牆規則處理邏輯](rule-processing.md)。
