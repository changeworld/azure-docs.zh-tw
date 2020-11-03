---
title: Azure VMware 解決方案網路和連線能力
description: Azure VMware 解決方案網路和連線能力描述。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924981"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware 解決方案提供私人雲端環境，可從內部部署和 Azure 型環境或資源存取。 Azure ExpressRoute 和 VPN 連線這類服務會提供連線能力。 這些服務需要特定的網路位址範圍和防火牆連接埠來啟用服務。

部署私人雲端時，會建立用於管理、佈建和 vMotion 的私人網路。 使用這些私人網路來存取 vCenter 和 NSX-T 管理員，以及虛擬機器 vMotion 或部署。  ExpressRoute Global Reach 可用來將私人雲端連線至內部部署環境。 連線需要在您的訂用帳戶中具有 ExpressRoute 線路的虛擬網路。

您可透過 Azure 虛擬 WAN 公用 IP 功能，存取網際網路上的資源，例如 Web 伺服器和虛擬機器。  根據預設，新的私人雲端會停用網際網路存取。 如需詳細資訊，請參閱[如何使用 Azure VMware 解決方案中的公用 IP 功能](../public-ip-usage.md)。