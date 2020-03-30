---
title: Azure VMware 解決方案（按雲簡單 - 公共 IP 位址）
description: 瞭解公共 IP 位址及其在 Azure VMware 解決方案上的優勢（通過雲簡單
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024971"
---
# <a name="cloudsimple-public-ip-address-overview"></a>雲簡單公共 IP 位址概述

公共 IP 位址允許互聯網資源通過私人 IP 位址向私有雲資源通信。 私人 IP 位址是私有雲 vCenter 上的虛擬機器或軟體負載等化器。 公共 IP 位址允許您將在私有雲上運行的服務公開給互聯網。

公共 IP 位址專用於私人 IP 位址，直到您取消分配它。 公共 IP 位址只能分配給一個私人 IP 位址。

與公共 IP 位址關聯的資源始終使用公共 IP 位址進行 Internet 訪問。 預設情況下，公共 IP 位址只允許訪問出站互聯網。  公共 IP 位址上的傳入流量被拒絕。  要允許入站流量，請為特定埠的公共 IP 位址創建防火牆規則。

## <a name="benefits"></a>優點

使用公共 IP 位址來通信入站提供：

* 分散式阻斷服務 （DDoS） 攻擊防護。 將自動為公共 IP 位址啟用此保護。
* 始終打開流量監控並即時緩解常見網路級攻擊。 這些防禦與微軟線上服務使用的防禦相同。
* Azure 全域網路的整個規模。 該網路可用於跨區域分發和緩解攻擊流量。  

## <a name="next-steps"></a>後續步驟

* 瞭解如何[分配公共 IP 位址](public-ips.md)