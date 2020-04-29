---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73412949"
---
**輸出資料傳輸**： [輸出資料傳輸](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

**交易**：您需支付在標準受控磁片上執行的交易數目。 針對標準 Ssd，小於或等於 256 KiB 輸送量的每個 i/o 作業都會被視為單一 i/o 作業。 大於 256 KiB 輸送量的 i/o 作業會視為大小為 256 KiB 的多個 i/o。 針對標準 Hdd，不論 i/o 大小為何，每個 IO 作業都會被視為單一交易。

如需受控磁碟定價的詳細資訊，包括交易成本，請參閱[受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra 磁片 VM 保留費用

Azure Vm 具有指示是否與 ultra 磁片相容的功能。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當 Ultra 磁碟連結至與 Ultra 磁碟相容的 VM 時，就不會收取此費用。 此費用會根據在 VM 上佈建的 vCPU 計費。 

> [!Note]
> 對於[受條件約束的核心 VM 大小](../articles/virtual-machines/linux/constrained-vcpu.md)，保留費用是根據實際的個 vcpu 數目，而不是受限制的核心數。 針對 Standard_E32 8s_v3，保留費用將以32核心為基礎。 

如需 ultra 磁片定價詳細資料，請參閱[Azure 磁片定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="azure-disk-reservation"></a>Azure 磁片保留

磁片保留可讓您以折扣預先購買一年的磁片儲存體，以降低您的總成本。 購買磁片保留時，您會在目的地區域中選取特定的磁片 SKU，例如，在美國東部2區域中的10個 P30 （1TiB） premium Ssd 為一年的詞彙。 保留體驗類似于保留的虛擬機器（VM）實例。 您可以組合 VM 和磁片保留區，以最大化您的節約。 目前，Azure 磁片保留會針對所有生產區域中的 premium SSD Sku，從 P30 （1TiB）到 P80 （32 TiB）提供一年承諾用量方案。 如需保留磁片價格的詳細資訊，請參閱[Azure 磁片定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)。