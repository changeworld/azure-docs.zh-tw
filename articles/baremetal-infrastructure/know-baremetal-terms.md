---
title: 瞭解 Azure BareMetal 基礎結構的條款
description: 瞭解 Azure BareMetal 基礎結構的條款。
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861914"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>瞭解 BareMetal 基礎結構的條款

在本文中，我們將討論一些重要的 BareMetal 條款。

- **修訂**：有一份原始的戳記修訂版稱為修訂 3 (Rev 3) ，以及 BareMetal 實例戳記的兩個不同戳記修訂。 Azure 虛擬機器主機的架構和鄰近性各有不同的戳記：
    - **修訂 4** (Rev 4) ：較新的設計，可更接近 azure 虛擬機器 (VM) 主機，並降低 azure Vm 與 BareMetal 實例單位之間的延遲。 
    - **修訂 4.2** (Rev 4.2) ：使用現有的 Rev 4 架構的最新更名 BareMetal 基礎結構。 Rev 4 可 (VM) 主機，更接近 Azure 虛擬機器。 它大幅改善了 Azure Vm 和 BareMetal 實例單位（部署于 Rev 4 戳記或資料列）之間的網路延遲。 您可以透過 Azure 入口網站存取和管理您的 BareMetal 實例。    

- **戳記**：定義 BareMetal 實例的 Microsoft 內部部署大小。 在部署實例單位之前，您必須在資料中心位置部署包含計算、網路和存放機架的 BareMetal 實例戳記。 這類部署稱為 BareMetal 實例戳記或修訂4.2。

- **租** 使用者：部署在 BareMetal 實例戳記中的客戶會隔離到租使用者中 *。* 每個租用戶的網路、儲存體和計算層會彼此區隔開來。 指派給不同租使用者的儲存體和計算單位無法看到彼此，也無法在 BareMetal 實例戳記層級上彼此通訊。 客戶可以選擇部署到不同的租用戶。 就算如此，BareMetal 實例戳記層級上的租使用者之間也不會進行通訊。

## <a name="next-steps"></a>後續步驟
深入瞭解 [BareMetal 基礎結構](workloads/sap/baremetal-overview-architecture.md) ，或如何 [識別和與 BareMetal 實例單位互動](workloads/sap/baremetal-infrastructure-portal.md)。 