---
title: 使用 Azure 遷移評估大量物理伺服器以遷移到 Azure |微軟文檔
description: 介紹如何使用 Azure 遷移服務評估大量物理伺服器以遷移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294366"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>評估大量物理伺服器以遷移到 Azure

本文介紹如何使用 Azure 遷移伺服器評估工具評估大量本地物理伺服器以遷移到 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 


在本文中，您將學會如何：
> [!div class="checklist"]
> * 計畫規模評估。
> * 配置 Azure 許可權，並準備物理伺服器進行評估。
> * 創建 Azure 遷移專案，並創建評估。
> * 在規劃遷移時查看評估。


> [!NOTE]
> 如果你想嘗試一個概念驗證，以評估幾個伺服器之前評估規模，請按照我們的[教程系列](tutorial-prepare-physical.md)。

## <a name="plan-for-assessment"></a>評估計畫

在規劃評估大量物理伺服器時，需要考慮幾點：

- **規劃 Azure 遷移專案**：瞭解如何部署 Azure 遷移專案。 例如，如果資料中心位於不同的地理位置，或者您需要將發現、評估或遷移相關的中繼資料存儲在不同的地理位置，則可能需要多個專案。
- **計畫設備**：Azure 遷移使用部署在 Windows 電腦上的本地 Azure 遷移設備來不斷發現用於評估和遷移的伺服器。 設備監視環境更改，如添加 VM、磁片或網路介面卡。 它還會向 Azure 發送有關中繼資料和效能資料的中繼資料和效能資料。 您需要確定要部署的設備數。


## <a name="planning-limits"></a>規劃限制
 
使用此表中總結的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure 遷移專案** | 評估專案中多達 35，000 台伺服器。
**Azure Migrate 設備** | 設備最多可以發現 250 台伺服器。<br/> 設備只能與單個 Azure 遷移專案關聯。<br/> 任意數量的設備都可以與單個 Azure 遷移專案相關聯。 <br/><br/> 
**組** | 單個組中最多可以添加 35，000 台伺服器。
**Azure 遷移評估** | 您可以在單個評估中評估多達 35，000 台伺服器。


## <a name="other-planning-considerations"></a>其他規劃注意事項

- 要從設備開始發現，您必須選擇每個物理伺服器。 

## <a name="prepare-for-assessment"></a>準備評估

準備 Azure 和物理伺服器以進行伺服器評估。 

1. 驗證[物理伺服器支援要求和限制](migrate-support-matrix-physical.md)。
2. 設置 Azure 帳戶與 Azure 遷移交互的許可權。
3. 準備物理伺服器。

按照[本教程](tutorial-prepare-physical.md)中的說明配置這些設置。

## <a name="create-a-project"></a>建立專案

根據您的規劃要求，請執行以下操作：

1. 建立 Azure Migrate 專案。
2. 將 Azure 遷移伺服器評估工具添加到專案中。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>創建和審核評估

1. 為物理伺服器創建評估。
1. 審查評估，為遷移規劃做準備。

[詳細瞭解](tutorial-assess-physical.md)創建和審核評估。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 計畫為物理伺服器縮放 Azure 遷移評估。
> * 已準備好的 Azure 和物理伺服器進行評估。
> * 創建了 Azure 遷移專案並運行了評估。
> * 審查了為遷移做準備的評估。

現在，[瞭解如何](concepts-assessment-calculation.md)計算評估，以及如何[修改評估](how-to-modify-assessment.md)。
