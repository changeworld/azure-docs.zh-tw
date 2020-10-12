---
title: Azure VMware Solution by CloudSimple-公用 IP 位址
description: 深入瞭解 Azure VMware Solution by CloudSimple 的公用 IP 位址及其優點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77024971"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 公用 IP 位址總覽

公用 IP 位址可讓網際網路資源以私人 IP 位址，向私人雲端資源進行輸入通訊。 私人 IP 位址是虛擬機器或私用雲端 vCenter 上的軟體負載平衡器。 公用 IP 位址可讓您將私人雲端上執行的服務公開給網際網路。

公用 IP 位址專用於私人 IP 位址，直到您將其取消指派為止。 公用 IP 位址只能指派給一個私人 IP 位址。

與公用 IP 位址相關聯的資源一律會使用公用 IP 位址來存取網際網路。 根據預設，公用 IP 位址上只允許輸出網際網路存取。  公用 IP 位址上的連入流量會遭到拒絕。  若要允許輸入流量，請將公用 IP 位址的防火牆規則建立到特定埠。

## <a name="benefits"></a>優點

使用公用 IP 位址來與輸入通訊，提供：

* 分散式阻斷服務 (DDoS) 攻擊防護。 此保護會自動針對公用 IP 位址啟用。
* 對常見網路層級攻擊的 alwayson 流量監視和即時緩和措施。 這些防禦措施是 Microsoft 線上服務所使用的相同防禦。
* Azure 全球網路的整個規模。 您可以使用網路來分散和減緩跨區域的攻擊流量。  

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [配置公用 IP 位址](public-ips.md)