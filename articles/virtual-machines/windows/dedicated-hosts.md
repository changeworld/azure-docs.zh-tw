---
title: 虛擬機器的 Azure 專用主機總覽
description: 深入瞭解如何使用 Azure 專用主機來部署虛擬機器。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/28/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 08d5f88ab018f9852da5bba5fe2230ef8148e914
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386517"
---
# <a name="azure-dedicated-hosts"></a>Azure 專用主機

Azure 專用主機是一個服務，可讓實體伺服器裝載一或多個虛擬機器 - 專用於一個 Azure 訂用帳戶。 專用主機是在資料中心內使用的相同實體伺服器，以資源的形式提供。 您可以在區域、可用性區域與容錯網域中佈建專用主機。 然後，您可以依照最能滿足您需要的設定，將 VM 直接放入已佈建的主機中。


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>後續步驟

- 您可以使用[Azure PowerShell](dedicated-hosts-powershell.md)、[入口網站](dedicated-hosts-portal.md)和[Azure CLI](../linux/dedicated-hosts-cli.md)來部署專用主機。

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，範例中使用區域和容錯網域來獲得區域中的最大復原。

- 您也可以使用[Azure 專用主機的保留實例](../prepay-dedicated-hosts-reserved-instances.md)來節省成本。
