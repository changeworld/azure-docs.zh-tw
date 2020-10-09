---
title: 使用 Azure Migrate 評定大量的實體伺服器以遷移至 Azure |Microsoft Docs
description: 說明如何使用 Azure Migrate 服務來評估大量的實體伺服器，以遷移至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: b84a7b88f700e2ba8e9fbe29099dfcbce8dbf3b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85081081"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>評定大量的實體伺服器以遷移至 Azure

本文說明如何使用 Azure Migrate Server 評定工具，來評定大量內部部署實體伺服器以遷移至 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 


在本文中，您將學會如何：
> [!div class="checklist"]
> * 大規模規劃評量。
> * 設定 Azure 許可權，並準備實體伺服器進行評量。
> * 建立 Azure Migrate 專案，並建立評量。
> * 當您計畫進行遷移時，請參閱評量。


> [!NOTE]
> 如果您想要在大規模評估之前，嘗試評估幾部伺服器的概念證明，請遵循我們的 [教學課程系列](tutorial-prepare-physical.md)。

## <a name="plan-for-assessment"></a>規劃評量

規劃大量實體伺服器的評量時，需要考慮幾個事項：

- **規劃 Azure Migrate 專案**：找出如何部署 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或您需要將探索、評量或遷移相關的中繼資料儲存在不同的地理位置，您可能需要多個專案。
- **規劃設備**： Azure Migrate 使用在 Windows 電腦上部署的內部部署 Azure Migrate 設備，持續探索伺服器以進行評量和遷移。 設備會監視環境變更，例如新增 Vm、磁片或網路介面卡。 它也會將相關的中繼資料和效能資料傳送至 Azure。 您必須找出要部署多少個設備。


## <a name="planning-limits"></a>規劃限制
 
使用下表中摘要說明的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure Migrate 專案** | 在專案中評估多達35000伺服器。
**Azure Migrate 設備** | 設備最多可探索1000部伺服器。<br/> 設備只能與單一 Azure Migrate 專案相關聯。<br/> 任何數量的設備都可以與單一 Azure Migrate 專案相關聯。 <br/><br/> 
**群組** | 您最多可以在單一群組中新增35000部伺服器。
**Azure Migrate 評量** | 您最多可以在單一評估中評估35000部伺服器。


## <a name="other-planning-considerations"></a>其他規劃考慮

- 若要從設備啟動探索，您必須選取每一部實體伺服器。 

## <a name="prepare-for-assessment"></a>準備進行評估

準備 Azure 和實體伺服器以進行伺服器評量。 

1. 確認 [實體伺服器支援需求和限制](migrate-support-matrix-physical.md)。
2. 為您的 Azure 帳戶設定許可權，以與 Azure Migrate 互動。
3. 準備實體伺服器。

遵循 [本教學](tutorial-prepare-physical.md) 課程中的指示來進行這些設定。

## <a name="create-a-project"></a>建立專案

根據您的規劃需求，請執行下列動作：

1. 建立 Azure Migrate 專案。
2. 將 Azure Migrate Server 評定工具新增至專案。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>建立和審核評量

1. 建立實體伺服器的評量。
1. 請參閱評定以準備進行遷移規劃。

[深入瞭解](tutorial-assess-physical.md) 如何建立和檢查評量。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 規劃調整實體伺服器 Azure Migrate 評量。
> * 準備好要進行評量的 Azure 和實體伺服器。
> * 建立 Azure Migrate 專案並執行評量。
> * 已檢查要準備進行遷移的評量。

現在， [瞭解如何](concepts-assessment-calculation.md) 計算評定，以及如何 [修改評](how-to-modify-assessment.md)量。
