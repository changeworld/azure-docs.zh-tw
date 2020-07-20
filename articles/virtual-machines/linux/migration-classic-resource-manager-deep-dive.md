---
title: 傳統到 Azure Resource Manager 遷移技術深入探討
description: 平臺支援的資源從傳統部署模型遷移至 Azure Resource Manager 的技術深入探討
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: a5277e23d92dd026aa19e278532869747709e646
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78944738"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討

> [!IMPORTANT]
> 目前，大約有90% 的 IaaS Vm 正在使用[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)。 從2020年2月28日起，傳統 Vm 已淘汰，並將于2023年3月1日完全淘汰。 [深入瞭解]( https://aka.ms/classicvmretirement)此淘汰及其對[您的影響](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)。

讓我們深入探討如何從 Azure 傳統部署模型移轉至 Azure Resource Manager 部署模型。 我們會就資源和功能層級來探討資源，以協助您了解 Azure 平台如何在這兩種部署模型之間移轉資源。 如需詳細資訊，請閱讀服務公告文章： [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager)](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>後續步驟

* [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager) 的概觀](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [將 IaaS 資源從傳統移轉至 Azure Resource Manager 的規劃](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [用於協助將 IaaS 資源從傳統移轉至 Azure Resource Manager 的社群工具](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [檢閱最常見的移轉錯誤](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [檢閱有關將 IaaS 資源從傳統移轉至 Azure Resource Manager 的常見問題集](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
