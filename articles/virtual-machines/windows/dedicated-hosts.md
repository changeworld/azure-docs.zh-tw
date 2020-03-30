---
title: 虛擬機器 Azure 專用主機概述
description: 詳細瞭解如何使用 Azure 專用主機部署虛擬機器。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251560"
---
# <a name="azure-dedicated-hosts"></a>Azure 專用主機

Azure 專用主機是一種服務，它提供物理伺服器（能夠承載一個或多個虛擬機器）專用於一個 Azure 訂閱。 專用主機是作為資源提供的資料中心中使用的物理伺服器。 您可以在區域、可用性區域和容錯域中預配專用主機。 然後，您可以將 VM 直接放入預配主機中，無論配置最適合您的需要。


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>後續步驟

- 可以使用[Azure PowerShell](dedicated-hosts-powershell.md)、[門戶](dedicated-hosts-portal.md)和[Azure CLI](../linux/dedicated-hosts-cli.md)部署專用主機。

- [此處](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例範本，它同時使用區域和容錯域，以實現區域中的最大恢復能力。

- 您還可以使用[Azure 專用主機的預留實例](../prepay-dedicated-hosts-reserved-instances.md)來節省成本。
