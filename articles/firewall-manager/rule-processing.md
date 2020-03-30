---
title: Azure 防火牆規則處理邏輯
description: 了解 Azure 防火牆規則處理邏輯
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518199"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火牆規則處理邏輯

Azure 防火牆具有 NAT 規則、網路規則和應用程式規則。 規則的處理依據是規則類型。

## <a name="network-rules-and-applications-rules"></a>網路規則和應用程式規則

首先會套用網路規則，然後套用應用程式規則。 之後規則就會終止。 因此，如果在網路規則中找到相符項目，就不會處理應用程式規則。  如果沒有符合的網路規則，而且如果封包通訊協定是 HTTP/HTTPS，則封包會由應用程式規則評估。 如果仍然找不到符合的規則，那麼封包會根據基礎結構規則集合進行評估。 如果仍然沒有相符項目，則封包預設會遭到拒絕。

## <a name="nat-rules"></a>NAT 規則

若要啟用輸入連線，請設定目的地網路位址轉譯 (DNAT)，如[教學課程：使用 Azure 入口網站以 Azure 防火牆 DNAT 篩選輸入流量](../firewall/tutorial-firewall-dnat.md)所述。 DNAT 規則會先套用。 如果找到相符項目，就會新增隱含的對應網路規則，以允許已轉譯的流量。 若要覆寫這個行為，您可以明確地使用符合已轉譯流量的拒絕規則來新增網路規則集合。 這些連線不會套用任何應用程式規則。

## <a name="inherited-rules"></a>繼承的規則

從父策略繼承的網路規則集合始終優先于定義為新策略一部分的網路規則集合之上。 相同的邏輯也適用于應用程式規則集合。 但是，無論繼承如何，網路規則集合始終在應用程式規則集合之前進行處理。

預設情況下，您的策略繼承其父策略威脅智慧模式。 您可以通過在策略設置頁中將威脅智慧模式設置為其他值來覆蓋此值。 只能用更嚴格的值進行重寫。 例如，如果父策略設置為*僅警報*，則可以將此本地策略配置為 *"警報"和"拒絕*"，但不能將其關閉。

## <a name="next-steps"></a>後續步驟

- [瞭解有關 Azure 防火牆管理器預覽版的資訊](overview.md)