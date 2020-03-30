---
title: 評估大量 Hyper-V VM，以便通過 Azure 遷移遷移到 Azure |微軟文檔
description: 介紹如何使用 Azure 遷移服務評估大量 Hyper-VM 以遷移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279445"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>評估大量 Hyper-V VM 以遷移到 Azure

本文介紹如何使用 Azure 遷移伺服器評估工具評估大量本地 Hyper-V VM 以遷移到 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 


在本文中，您將學會如何：
> [!div class="checklist"]
> * 計畫規模評估。
> * 配置 Azure 許可權，並準備 Hyper-V 進行評估。
> * 創建 Azure 遷移專案，並創建評估。
> * 在規劃遷移時查看評估。


> [!NOTE]
> 如果您想嘗試一個概念驗證，以評估幾個 VM 之前進行大規模評估，請按照我們的[教程系列](tutorial-prepare-hyper-v.md)操作

## <a name="plan-for-assessment"></a>評估計畫

在規劃評估大量超 VM 時，需要考慮幾件事：

- **規劃 Azure 遷移專案**：瞭解如何部署 Azure 遷移專案。 例如，如果資料中心位於不同的地理位置，或者您需要將發現、評估或遷移相關的中繼資料存儲在不同的地理位置，則可能需要多個專案。
- **計畫設備**：Azure 遷移使用本地 Azure 遷移設備（部署為 Hyper-V VM）來持續發現 VM 以進行評估和遷移。 設備監視環境更改，如添加 VM、磁片或網路介面卡。 它還會向 Azure 發送有關中繼資料和效能資料的中繼資料和效能資料。 您需要確定要部署的設備數。


## <a name="planning-limits"></a>規劃限制
 
使用此表中總結的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure 遷移專案** | 評估專案中多達 35，000 個 VM。
**Azure Migrate 設備** | 設備可以發現多達 5000 個 VM。<br/> 設備可連接到多達 300 台 Hyper-V 主機。<br/> 設備只能與單個 Azure 遷移專案關聯。<br/> 任意數量的設備都可以與單個 Azure 遷移專案相關聯。 <br/><br/> 
**組** | 單個組中最多可以添加 35，000 個 VM。
**Azure 遷移評估** | 您可以在單個評估中評估多達 35，000 個 VM。



## <a name="other-planning-considerations"></a>其他規劃注意事項

- 要從設備開始發現，您必須選擇每個 Hyper-V 主機。 
- 如果您正在運行多租戶環境，則當前無法發現僅屬於特定租戶的 VM。 

## <a name="prepare-for-assessment"></a>準備評估

準備 Azure 和 Hyper-V 進行伺服器評估。 

1. 驗證[Hyper-V 支援要求和限制](migrate-support-matrix-hyper-v.md)。
2. 設置 Azure 帳戶與 Azure 遷移交互的許可權
3. 準備超 V 主機和 VM

按照[本教程](tutorial-prepare-hyper-v.md)中的說明配置這些設置。

## <a name="create-a-project"></a>建立專案

根據您的規劃要求，請執行以下操作：

1. 創建 Azure 遷移專案。
2. 將 Azure 遷移伺服器評估工具添加到專案中。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>創建和審核評估

1. 為超 VM 創建評估。
1. 審查評估，為遷移規劃做準備。

[詳細瞭解](tutorial-assess-hyper-v.md)創建和審核評估。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 計畫為超 VM 縮放 Azure 遷移評估
> * 準備 Azure 和超 V 進行評估
> * 創建 Azure 遷移專案並運行評估
> * 審查了為遷移做準備的評估。

現在，[瞭解如何](concepts-assessment-calculation.md)計算評估以及如何[修改評估](how-to-modify-assessment.md)
