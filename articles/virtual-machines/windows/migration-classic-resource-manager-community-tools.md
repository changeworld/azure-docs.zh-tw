---
title: 社區工具 - 將經典資源移動到 Azure 資源管理器
description: 本文收錄由社群所提供、可協助將 IaaS 資源從傳統模型移轉至 Azure Resource Manager 部署模型的工具。
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f4298af05f021f0a9579a9837308be5d15a3c14f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915396"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>可將 IaaS 資源從傳統模型移轉至 Azure Resource Manager 的社群工具

> [!IMPORTANT]
> 今天，大約 90% 的 IaaS VM 正在使用[Azure 資源管理器](https://azure.microsoft.com/features/resource-manager/)。 截至 2020 年 2 月 28 日，經典 VM 已被棄用，將于 2023 年 3 月 1 日完全停用。 [詳細瞭解]( https://aka.ms/classicvmretirement)此棄用[及其如何影響您](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)。

本文收錄由社群所提供、可協助將 IaaS 資源從傳統模型移轉至 Azure Resource Manager 部署模型的工具。

> [!NOTE]
> 這些工具尚未獲得 Microsoft 支援服務的官方支援。 因此可在 GitHub 上取得其開放原始碼，我們很樂意收到修正或其他案例的 PR。 若要回報問題，請使用 GitHub 問題功能。
> 
> 使用這些工具移轉可能會造成傳統虛擬機器停機。 如果您想要尋求平台支援的移轉，請造訪 
> 
>   * [支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 堆疊的平台](migration-classic-resource-manager-overview.md)
>   * [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](migration-classic-resource-manager-deep-dive.md)
>   * [使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
建立這些協助工具集合旨在協助企業從 Azure 服務管理移轉到 Azure Resource Manager。 此工具可讓您將您的基礎結構複寫到另一個訂用帳戶中，以用於在於生產訂用帳戶執行移轉之前先測試移轉並解決所有問題。

[連結至工具文件](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz 是可將一整組傳統 IaaS 資源移轉至 Azure Resource Manager IaaS 資源的額外選項。 移轉可能會發生在相同的訂用帳戶內或不同的訂用帳戶與訂用帳戶類型之間 (例如 CSP 訂用帳戶)。

[連結至工具文件](https://github.com/Azure/migAz)

## <a name="next-steps"></a>後續步驟

* [平台支援的 IaaS 資源移轉 (從傳統移轉至 Azure Resource Manager) 的概觀](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [平台支援的從傳統移轉至 Azure Resource Manager 的技術深入探討](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [將 IaaS 資源從傳統移轉至 Azure Resource Manager 的規劃](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [檢閱最常見的移轉錯誤](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [檢閱有關將 IaaS 資源從傳統移轉至 Azure Resource Manager 的常見問題集](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

