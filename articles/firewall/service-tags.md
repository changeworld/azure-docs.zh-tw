---
title: Azure 防火牆服務標籤概觀
description: 服務標籤表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168684"
---
# <a name="azure-firewall-service-tags"></a>Azure 防火牆服務標籤

服務標籤表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。 您無法建立自己的服務標籤，或指定標籤中包含哪些 IP 位址。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

防火牆服務標籤可以用於網路規則目的地的欄位。 可以使用這些標籤來取代特定的 IP 位址。

## <a name="supported-service-tags"></a>支援的服務標籤

有關可用於 Azure 防火牆網路規則的服務標記清單，請參閱[安全性群組](../virtual-network/security-overview.md#service-tags)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 防火牆規則，請參閱 [Azure 防火牆規則處理邏輯](rule-processing.md)。