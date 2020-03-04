---
title: 虛擬機器的 Azure 專用主機總覽
description: 深入瞭解如何使用 Azure 專用主機來部署虛擬機器。
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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251560"
---
# <a name="azure-dedicated-hosts"></a>Azure 專用主機

「Azure 專用主機」是一種服務，可提供實體伺服器來裝載一或多個 Azure 訂用帳戶專用的虛擬機器。 專用主機是在資料中心內使用的相同實體伺服器，以資源的形式提供。 您可以在區域、可用性區域和容錯網域中布建專用主機。 然後，您可以將 Vm 直接放入已布建的主機中，不論何種設定最符合您的需求。


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>後續步驟

- 您可以使用[Azure PowerShell](dedicated-hosts-powershell.md)、[入口網站](dedicated-hosts-portal.md)和[Azure CLI](../linux/dedicated-hosts-cli.md)來部署專用主機。

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，它會使用區域和容錯網域來取得區域中的最大復原。

- 您也可以使用[Azure 專用主機的保留實例](../prepay-dedicated-hosts-reserved-instances.md)來節省成本。
