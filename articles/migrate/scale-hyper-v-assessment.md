---
title: 使用 Azure Migrate 評定大量的 Hyper-v Vm 以遷移至 Azure |Microsoft Docs
description: 說明如何使用 Azure Migrate 服務，評估大量的 Hyper-v Vm 以遷移至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70279445"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>評估大量的 Hyper-v Vm 以遷移至 Azure

本文說明如何使用 Azure Migrate Server 評估工具，來評估大量內部部署 Hyper-v Vm 以遷移至 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 


在本文中，您將學會如何：
> [!div class="checklist"]
> * 規劃大規模的評估。
> * 設定 Azure 許可權，並準備 Hyper-v 進行評估。
> * 建立 Azure Migrate 專案，並建立評量。
> * 當您規劃遷移時，請檢查評量。


> [!NOTE]
> 如果您想要在大規模評估之前嘗試評估一些 Vm 的概念證明，請遵循我們的[教學課程系列](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>規劃評估

在規劃大量 Hyper-v Vm 的評估時，有幾件事需要考慮：

- **規劃 Azure Migrate 專案**：瞭解如何部署 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或是您需要將探索、評定或遷移相關的中繼資料儲存在不同的地理位置，您可能需要多個專案。
- **規劃設備**： Azure Migrate 使用部署為 hyper-v VM 的內部部署 Azure Migrate 設備，持續探索 vm 以進行評估和遷移。 設備會監視環境變更，例如新增 Vm、磁片或網路介面卡。 它也會將其相關的中繼資料和效能資料傳送至 Azure。 您必須找出要部署的應用裝置數量。


## <a name="planning-limits"></a>規劃限制
 
使用此表中摘要說明的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure Migrate 專案** | 在專案中評估最多35000個 Vm。
**Azure Migrate 設備** | 設備可以探索最多5000部 Vm。<br/> 設備最多可以連線到300的 Hyper-v 主機。<br/> 設備只能與單一 Azure Migrate 專案相關聯。<br/> 任何數目的設備都可以與單一 Azure Migrate 專案相關聯。 <br/><br/> 
**群組** | 您最多可以在單一群組中新增35000個 Vm。
**Azure Migrate 評量** | 您可以在單一評估中評估多達35000個 Vm。



## <a name="other-planning-considerations"></a>其他規劃考慮

- 若要從設備啟動探索，您必須選取每個 Hyper-v 主機。 
- 如果您執行的是多租使用者環境，目前無法只探索屬於特定租使用者的 Vm。 

## <a name="prepare-for-assessment"></a>準備進行評估

準備 Azure 和 Hyper-v 進行伺服器評估。 

1. 確認[hyper-v 支援需求和限制](migrate-support-matrix-hyper-v.md)。
2. 設定 Azure 帳戶的許可權以與 Azure Migrate 互動
3. 準備 Hyper-v 主機和 Vm

請遵循[本教學](tutorial-prepare-hyper-v.md)課程中的指示來進行這些設定。

## <a name="create-a-project"></a>建立專案

根據您的規劃需求，執行下列動作：

1. 建立 Azure Migrate 專案。
2. 將 [Azure Migrate Server 評估工具] 新增至專案。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>建立和審查評量

1. 建立 Hyper-v Vm 的評量。
1. 請參閱評估以準備進行遷移計畫。

[深入瞭解](tutorial-assess-hyper-v.md)如何建立和查看評量。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 已規劃調整 Hyper-v Vm 的 Azure Migrate 評量
> * 已準備好 Azure 和 Hyper-v 進行評估
> * 建立 Azure Migrate 專案並執行評量
> * 已審查評估以準備進行遷移。

現在，[瞭解如何](concepts-assessment-calculation.md)計算評定，以及如何[修改評](how-to-modify-assessment.md)量
