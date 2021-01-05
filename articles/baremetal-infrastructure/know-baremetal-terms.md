---
title: 瞭解 Azure BareMetal 基礎結構的條款
description: 瞭解 Azure BareMetal 基礎結構的條款。
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829120"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>瞭解 BareMetal 基礎結構的條款

在本文中，我們將討論一些重要的 BareMetal 條款。

- **修訂**： BareMetal 實例戳記有兩種不同的戳記修訂。 Azure 虛擬機器主機的架構和鄰近性各有不同的版本：
    - **修訂 3** (Rev 3) ：是原始的設計。
    - **修訂 4** (Rev 4) ：這是新的設計，可讓您更接近 azure 虛擬機器 (VM) 主機，並降低 azure Vm 與 BareMetal 實例單位之間的延遲。 
    - **修訂 4.2** (Rev 4.2) ：是最新的更名 BareMetal 基礎結構，其使用現有的 Rev 4 架構。 您可以透過 Azure 入口網站存取和管理您的 BareMetal 實例。  

- **戳記**：定義 BareMetal 實例的 Microsoft 內部部署大小。 在部署實例單位之前，您必須在資料中心位置部署由計算、網路和存放機架組成的 BareMetal 實例戳記。 這類部署稱為 BareMetal 實例戳記或修訂4.2。

- **租** 使用者：部署在 BareMetal 實例戳記中的客戶會隔離到租使用者中 *。* 每個租用戶的網路、儲存體和計算層會彼此區隔開來。 指派給不同租使用者的儲存體和計算單位無法看到彼此，也無法在 BareMetal 實例戳記層級上彼此通訊。 客戶可以選擇部署到不同的租用戶。 就算如此，BareMetal 實例戳記層級上的租使用者之間也不會進行通訊。

## <a name="next-steps"></a>後續步驟
瞭解如何透過 [Azure 入口網站](workloads/sap/baremetal-infrastructure-portal.md)識別 BareMetal 實例單位並與其互動。


 