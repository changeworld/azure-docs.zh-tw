---
title: Azure VMWare 解決方案網路和連線能力
description: Azure VMWare 解決方案網路和連線能力描述。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574446"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware 解決方案提供私人雲端環境，可從內部部署和 Azure 型環境或資源存取。 Azure ExpressRoute 和 VPN 連線這類服務會提供連線能力。 這些服務需要特定的網路位址範圍和防火牆連接埠來啟用服務。

部署私人雲端時，會建立用於管理、佈建和 vMotion 的私人網路。 使用這些私人網路來存取 vCenter 和 NSX-T 管理員，以及虛擬機器 vMotion 或部署。  ExpressRoute Global Reach 可用來將私人雲端連線至內部部署環境。 連線需要在您的訂用帳戶中具有 ExpressRoute 線路的虛擬網路。



>[!NOTE]
>在部署私人雲端時，會佈建並提供對網際網路和 Azure 服務的存取，以取用生產網路上的 VM。  預設會停用新私人雲端的網際網路存取，且可以隨時啟用或停用。