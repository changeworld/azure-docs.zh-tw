---
title: 使用 Azure Migrate 評估大量的實體伺服器以遷移至 Azure |Microsoft Docs
description: 說明如何使用 Azure Migrate 服務，評估大量的實體伺服器以遷移至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76294366"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>評估大量的實體伺服器以遷移至 Azure

本文說明如何使用 Azure Migrate Server 評估工具來評估大量內部部署實體伺服器，以遷移至 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 


在本文中，您將學會如何：
> [!div class="checklist"]
> * 規劃大規模的評估。
> * 設定 Azure 許可權，並準備要評估的實體伺服器。
> * 建立 Azure Migrate 專案，並建立評量。
> * 當您規劃遷移時，請檢查評量。


> [!NOTE]
> 如果您想要在大規模評估之前嘗試評估幾部伺服器的概念證明，請遵循我們的[教學課程系列](tutorial-prepare-physical.md)。

## <a name="plan-for-assessment"></a>規劃評估

在規劃大量實體伺服器的評估時，有幾件事需要考慮：

- **規劃 Azure Migrate 專案**：瞭解如何部署 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或是您需要將探索、評定或遷移相關的中繼資料儲存在不同的地理位置，您可能需要多個專案。
- **規劃設備**： Azure Migrate 使用部署在 Windows 電腦上的內部部署 Azure Migrate 設備，持續探索伺服器以進行評估和遷移。 設備會監視環境變更，例如新增 Vm、磁片或網路介面卡。 它也會將其相關的中繼資料和效能資料傳送至 Azure。 您必須找出要部署的應用裝置數量。


## <a name="planning-limits"></a>規劃限制
 
使用此表中摘要說明的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure Migrate 專案** | 評估多達35000伺服器的專案。
**Azure Migrate 設備** | 設備可以探索最多250部伺服器。<br/> 設備只能與單一 Azure Migrate 專案相關聯。<br/> 任何數目的設備都可以與單一 Azure Migrate 專案相關聯。 <br/><br/> 
**群組** | 您最多可以在單一群組中新增35000部伺服器。
**Azure Migrate 評量** | 您最多可以在單一評估中評估35000部伺服器。


## <a name="other-planning-considerations"></a>其他規劃考慮

- 若要從設備開始探索，您必須選取每個實體伺服器。 

## <a name="prepare-for-assessment"></a>準備進行評估

準備 Azure 和實體伺服器以進行伺服器評估。 

1. 確認[實體伺服器支援需求和限制](migrate-support-matrix-physical.md)。
2. 設定 Azure 帳戶的許可權，以與 Azure Migrate 進行互動。
3. 準備實體伺服器。

請遵循[本教學](tutorial-prepare-physical.md)課程中的指示來進行這些設定。

## <a name="create-a-project"></a>建立專案

根據您的規劃需求，執行下列動作：

1. 建立 Azure Migrate 專案。
2. 將 [Azure Migrate Server 評估工具] 新增至專案。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>建立和審查評量

1. 建立實體伺服器的評量。
1. 請參閱評估以準備進行遷移計畫。

[深入瞭解](tutorial-assess-physical.md)如何建立和查看評量。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 規劃調整實體伺服器的 Azure Migrate 評量。
> * 準備好 Azure 和實體伺服器以進行評量。
> * 建立 Azure Migrate 專案並執行評量。
> * 已審查評估以準備進行遷移。

現在，[瞭解如何](concepts-assessment-calculation.md)計算評定，以及如何[修改評](how-to-modify-assessment.md)量。
