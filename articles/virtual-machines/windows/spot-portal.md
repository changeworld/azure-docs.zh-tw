---
title: 使用門戶部署 Azure Spot VM
description: 瞭解如何使用 Azure PowerShell 部署 Spot VM 以節省成本。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158973"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>預覽：使用 Azure 門戶部署 Spot VM

使用[Spot VM](spot-vms.md)使您能夠利用我們未使用的容量，從而顯著節省成本。 在 Azure 需要返回容量的任何時間點，Azure 基礎結構將驅逐 Spot VM。 因此，Spot VM 非常適合處理批次處理作業、開發/測試環境、大型計算工作負載等中斷的工作負載。

現貨 VM 的定價基於區域和 SKU 是可變的。 有關詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 有關設置最高價格的詳細資訊，請參閱[現貨 VM - 定價](spot-vms.md#pricing)。

您可以選擇為 VM 設置您願意每小時支付的最高價格。 Spot VM 的最高價格可以用美元 （USD） 設置，最多使用 5 個小數位。 例如，該值`0.05701`將是每小時 0.05701 美元的最高價格。 如果將最高價格設置為`-1`，則 VM 不會根據價格被逐出。 VM 的價格將是現貨的當前價格或標準 VM 的價格，只要有容量和配額可用，標準 VM 的價格就更少了。

> [!IMPORTANT]
> 競價實例當前處於公共預覽版中。
> 不建議生產工作負載使用此預覽版本。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>


## <a name="create-the-vm"></a>建立 VM

創建使用 Spot VM 的 VM 的過程與[快速入門](quick-create-portal.md)中詳細說明的過程相同。 部署 VM 時，可以選擇使用 Azure 點實例。


在 **"基本"** 選項卡上，在 **"實例詳細資訊**"部分中，"**否**"是使用 Azure 點實例的預設值。

![選擇"否"的螢幕截圖，不要使用 Azure 點實例](media/spot-portal/no.png)

它你選擇**是**，部分展開，你可以選擇你的[驅逐類型和驅逐策略](spot-vms.md#eviction-policy)。 

![選擇"是"的螢幕截圖，請使用 Azure 點實例](media/spot-portal/yes.png)


## <a name="next-steps"></a>後續步驟

您還可以使用[PowerShell](spot-powershell.md)創建 Spot VM。