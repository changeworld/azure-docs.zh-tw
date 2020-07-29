---
title: 依 CloudSimple 的 Azure VMware 解決方案-公用 IP 位址
description: 瞭解公用 IP 位址及其在 Azure VMware Solution by CloudSimple 的優點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024971"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 公用 IP 位址總覽

公用 IP 位址可讓網際網路資源在私人 IP 位址上與私人雲端資源進行輸入通訊。 私人 IP 位址是私人雲端 vCenter 上的虛擬機器或軟體負載平衡器。 公用 IP 位址可讓您將在私人雲端上執行的服務公開至網際網路。

公用 IP 位址專用於私人 IP 位址，直到您將它取消指派為止。 公用 IP 位址只能指派給一個私人 IP 位址。

與公用 IP 位址相關聯的資源一律會使用公用 IP 位址來存取網際網路。 根據預設，只允許在公用 IP 位址上進行輸出網際網路存取。  公用 IP 位址上的連入流量遭到拒絕。  若要允許輸入流量，請針對特定埠建立公用 IP 位址的防火牆規則。

## <a name="benefits"></a>優點

使用公用 IP 位址來通訊輸入提供：

* 分散式阻斷服務（DDoS）攻擊防護。 公用 IP 位址會自動啟用此保護。
* 一般網路層級攻擊的 alwayson 流量監視和即時緩和。 這些防禦措施是 Microsoft 線上服務所使用的相同防禦。
* Azure 全球網路的整個規模。 網路可以用來分散和緩和跨區域的攻擊流量。  

## <a name="next-steps"></a>後續步驟

* 瞭解如何[配置公用 IP 位址](public-ips.md)