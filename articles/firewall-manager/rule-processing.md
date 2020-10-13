---
title: Azure 防火牆管理員規則處理邏輯
description: 了解 Azure 防火牆規則處理邏輯
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9184bf7baa85420e067edb4c0aafccb7e6711225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512175"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure 防火牆規則處理邏輯

Azure 防火牆具有 NAT 規則、網路規則和應用程式規則。 規則的處理依據是規則類型。

## <a name="network-rules-and-applications-rules"></a>網路規則和應用程式規則

首先會套用網路規則，然後套用應用程式規則。 之後規則就會終止。 因此，如果在網路規則中找到相符項，則不會處理應用程式規則。  如果沒有符合的網路規則，而且如果封包通訊協定是 HTTP/HTTPS，則封包會由應用程式規則進行評估。 如果仍然找不到符合的規則，那麼封包會根據基礎結構規則集合進行評估。 如果仍然沒有相符的，則預設會拒絕封包。

## <a name="nat-rules"></a>NAT 規則

若要啟用輸入連線，請設定目的地網路位址轉譯 (DNAT)，如[教學課程：使用 Azure 入口網站以 Azure 防火牆 DNAT 篩選輸入流量](../firewall/tutorial-firewall-dnat.md)所述。 DNAT 規則會先套用。 如果找到相符項目，就會新增隱含的對應網路規則，以允許已轉譯的流量。 若要覆寫這個行為，您可以明確地使用符合已轉譯流量的拒絕規則來新增網路規則集合。 這些連線不會套用任何應用程式規則。

## <a name="inherited-rules"></a>繼承的規則

繼承自父原則的網路規則集合一律優先于定義為新原則一部分的網路規則集合。 相同的邏輯也適用於應用程式規則集合。 然而，不管繼承，網路規則集合一律比應用程式規則集合優先處理。

根據預設，您的原則會繼承其父原則威脅情報模式。 您可以在 [原則設定] 頁面中，將威脅情報模式設定為不同的值來覆寫此設定。 您只能使用更嚴格的值覆寫。 例如，如果您的父原則設定為 [ *僅警示*]，您可以將此本機原則設定為 [ *警示] 和 [拒絕*]，但您無法將它關閉。

## <a name="next-steps"></a>接下來的步驟

- [深入瞭解 Azure 防火牆管理員](overview.md)