---
title: Azure 防火牆管理員預覽部署概觀
description: 了解 Azure 防火牆管理員預覽所需的概略部署步驟
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443120"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure 防火牆管理員預覽部署概觀

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

有多種方法可以部署 Azure 防火牆管理員預覽，但建議使用下列一般程序。

## <a name="general-deployment-process"></a>一般部署程序

### <a name="hub-virtual-networks"></a>中樞虛擬網路

1.  建立防火牆原則

    - 建立新的原則
<br>*or*<br>
    - 衍生基底原則及自訂本機原則
<br>*or*<br>
    - 從現有的 Azure 防火牆匯入規則。 請務必從應套用到多個防火牆的原則中移除 NAT 規則
1. 建立中樞和輪輻架構
   - 使用 Azure 防火牆管理員建立中樞虛擬網路，並使用虛擬網路對等互連將輪輻虛擬網路對等互連至中樞虛擬網路
<br>*or*<br>
    - 建立虛擬網路及新增虛擬網路連線，並使用虛擬網路對等互連將輪輻虛擬網路對等互連至該虛擬網路

3. 選取安全性提供者並建立防火牆原則的關聯。 目前，只有 Azure 防火牆是支援的提供者。

   - 這會在您建立中樞虛擬網路時完成
<br>*or*<br>
    - 將現有虛擬網路轉換為中樞虛擬網路。 此外，也可能轉換多個虛擬網路。

4. 設定 [使用者定義路由]，以將流量路由傳送至您的中樞虛擬網路防火牆。


### <a name="secured-virtual-hubs"></a>安全虛擬中樞

1. 建立中樞和輪輻架構

   - 使用 Azure 防火牆管理員建立安全虛擬中樞，並新增虛擬網路連線。<br>*or*<br>
   - 建立虛擬 WAN 中樞並新增虛擬網路連線。
2. 選取安全性提供者

   - 在建立安全虛擬中樞時完成。<br>*or*<br>
   - 將現有虛擬 WAN 中樞轉換成安全虛擬中樞。
3. 建立防火牆原則，並將它與您的中樞建立關聯

   - 僅在使用 Azure 防火牆時才適用。
   - 協力廠商安全性即服務 (SECaaS) 原則是透過合作夥伴管理體驗來設定。
4. 設定路由設定以將流量路由傳送至安全中樞

   - 使用 [安全虛擬中樞路由設定] 頁面，輕鬆地將流量路由傳送到安全中樞，以進行篩選和記錄，而不需要使用者定義路由 (UDR) 在輪輻虛擬網路上。

> [!NOTE]
> - 每個區域的每個虛擬 WAN 上不能有超過一個中樞。 但是，您可以在區域中新增多個虛擬 WAN 來達成此目的。
> - vWAN 中的中樞不能有重疊的 IP 空間。
> - 您的中樞 VNet 連線必須與中樞位於相同的區域。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure 入口網站以 Azure 防火牆管理員預覽來保護您的雲端網路](secure-cloud-network.md)