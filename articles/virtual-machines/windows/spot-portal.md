---
title: 使用入口網站來部署 Azure 位置 Vm
description: 瞭解如何使用 Azure PowerShell 部署點 Vm 以節省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 6e7723a437e90807063e3c3b7af2bf068dca5b9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82100646"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>使用 Azure 入口網站部署點 Vm

使用「[點 vm](spot-vms.md) 」可讓您以可觀的成本節約，利用我們未使用的容量。 Azure 基礎結構會在任何時間點回復，以找出虛擬機器的功能。 因此，針對可處理中斷的工作負載（例如批次處理作業、開發/測試環境、大型計算工作負載等），找出 Vm 很棒。

點 Vm 的定價是以區域和 SKU 為依據的變數。 如需詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 如需設定最大價格的詳細資訊，請參閱[找出 vm-定價](spot-vms.md#pricing)。

您可以選擇為 VM 設定您願意支付的最大價格（每小時）。 您可以使用最多5個小數位數，以美元（USD）來設定點 VM 的最大價格。 例如，此值 `0.05701` 是每小時 $0.05701 美元的最大價格。 如果您將最大價格設為 `-1` ，則不會根據價格來收回 VM。 VM 的價格將會是標準 VM 的目前價格或價格（這是較少的），只要有可用的容量和配額。


## <a name="create-the-vm"></a>建立 VM

建立使用點 Vm 之 VM 的程式，與[快速入門](quick-create-portal.md)中所述的步驟相同。 當您部署 VM 時，您可以選擇使用 Azure 點實例。


在 [**基本**] 索引標籤的 [**實例詳細資料**] 區段中，[**否**] 是使用 Azure 點實例的預設值。

![螢幕擷取畫面：選擇 [否]，不要使用 Azure 點實例](media/spot-portal/no.png)

您選取 **[是]**，區段會展開，而您可以選擇收回[類型和收回原則](spot-vms.md#eviction-policy)。 

![選擇 [是] 的螢幕擷取畫面，使用 Azure 點實例](media/spot-portal/yes.png)


## <a name="next-steps"></a>後續步驟

您也可以使用[PowerShell](spot-powershell.md)建立點 vm。