---
title: 評估大量 VMware VM，以便通過 Azure 遷移遷移到 Azure
description: 介紹如何使用 Azure 遷移服務評估大量 VMware VM 以遷移到 Azure。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336862"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>評估大量 VMware VM 以遷移到 Azure


本文介紹如何使用 Azure 遷移伺服器評估工具評估大量本地 VMware VM 以遷移到 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 

在本文中，您將學會如何：
> [!div class="checklist"]
> * 計畫規模評估。
> * 配置 Azure 許可權，並為評估準備 VMware。
> * 創建 Azure 遷移專案，並創建評估。
> * 在規劃遷移時查看評估。


> [!NOTE]
> 如果您想嘗試一個概念驗證，以評估幾個 VM 之前進行大規模評估，請按照我們的[教程系列](tutorial-prepare-vmware.md)操作

## <a name="plan-for-assessment"></a>評估計畫

在規劃評估大量 VMware VM 時，需要考慮幾點：

- **規劃 Azure 遷移專案**：瞭解如何部署 Azure 遷移專案。 例如，如果資料中心位於不同的地理位置，或者您需要將發現、評估或遷移相關的中繼資料存儲在不同的地理位置，則可能需要多個專案。 
- **計畫設備**：Azure 遷移使用本地 Azure 遷移設備（部署為 VMware VM）來持續發現 VM。 設備監視環境更改，如添加 VM、磁片或網路介面卡。 它還會向 Azure 發送有關中繼資料和效能資料的中繼資料和效能資料。 您需要確定需要部署多少台設備。
- **計畫帳戶進行發現**：Azure 遷移設備使用有權訪問 vCenter Server 的帳戶來發現 VM 以進行評估和遷移。 如果您發現超過 10，000 個 VM，則設置多個帳戶。


## <a name="planning-limits"></a>規劃限制
 
使用此表中總結的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure 遷移專案** | 評估專案中多達 35，000 個 VM。
**Azure Migrate 設備** | 設備可以在 vCenter 伺服器上發現多達 10，000 台 VM。<br/> 設備只能連接到單個 vCenter 伺服器。<br/> 設備只能與單個 Azure 遷移專案關聯。<br/>  任意數量的設備都可以與單個 Azure 遷移專案相關聯。 <br/><br/> 
**組** | 單個組中最多可以添加 35，000 個 VM。
**Azure 遷移評估** | 您可以在單個評估中評估多達 35，000 個 VM。

考慮到這些限制，下面是一些示例部署：


**vCenter 伺服器** | **伺服器上的 VM** | **建議** | **動作**
---|---|---
一個 | < 10，000 | 一個 Azure 遷移專案。<br/> 一個設備。<br/> 一個 vCenter 帳戶用於發現。 | 設置設備，使用帳戶連接到 vCenter 伺服器。
一個 | > 10，000 | 一個 Azure 遷移專案。<br/> 多個設備。<br/> 多個 vCenter 帳戶。 | 為每 10，000 個 VM 設置設備。<br/><br/> 設置 vCenter 帳戶，並將庫存劃分以將帳戶的訪問限制為少於 10，000 個 VM。<br/> 使用帳戶將每個設備連接到 vCenter 伺服器。<br/> 您可以分析使用不同設備發現的電腦之間的依賴項。
多個 | < 10，000 |  一個 Azure 遷移專案。<br/> 多個設備。<br/> 一個 vCenter 帳戶用於發現。 | 設置設備，使用帳戶連接到 vCenter 伺服器。<br/> 您可以分析使用不同設備發現的電腦之間的依賴項。
多個 | > 10，000 | 一個 Azure 遷移專案。<br/> 多個設備。<br/> 多個 vCenter 帳戶。 | 如果 vCenter 伺服器發現< 10，000 個 VM，請為每個 vCenter 伺服器設置一個設備。<br/><br/> 如果 vCenter Server 發現> 10，000 個 VM，則為每個 10，000 個 VM 設置一個設備。<br/> 設置 vCenter 帳戶，並將庫存劃分以將帳戶的訪問限制為少於 10，000 個 VM。<br/> 使用帳戶將每個設備連接到 vCenter 伺服器。<br/> 您可以分析使用不同設備發現的電腦之間的依賴項。


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>在多租戶環境中規劃發現

如果您計畫使用多租戶環境，則可以在 vCenter 伺服器上對發現進行限定。

- 您可以將設備發現範圍設置為 vCenter Server 資料中心、群集群集或資料夾、主機主機或資料夾或單個 VM。
- 如果環境在租戶之間共用，並且希望單獨發現每個租戶，則可以對設備用於發現的 vCenter 帳戶的訪問範圍。 
    - 如果租戶共用主機，則可能需要按 VM 資料夾進行範圍。 如果 vCenter 帳戶在 vCenter VM 資料夾級別授予存取權限，則 Azure 遷移無法發現 VM。 如果您想要依據 VM 資料夾來界定探索範圍，只要確保 vCenter 帳戶在 VM 層級上已獲派唯讀存取權即可。 [深入了解](set-discovery-scope.md)。

## <a name="prepare-for-assessment"></a>準備評估

準備 Azure 和 VMware 進行伺服器評估。 

1. 驗證[VMware 支援要求和限制](migrate-support-matrix-vmware.md)。
2. 設置 Azure 帳戶與 Azure 遷移交互的許可權。
3. 準備 VMware 進行評估。

按照[本教程](tutorial-prepare-vmware.md)中的說明配置這些設置。


## <a name="create-a-project"></a>建立專案

根據您的規劃要求，請執行以下操作：

1. 創建 Azure 遷移專案。
2. 將 Azure 遷移伺服器評估工具添加到專案中。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>創建和審核評估

1. 為 VMware VM 創建評估。
1. 審查評估，為遷移規劃做準備。


按照[本教程](tutorial-assess-vmware.md)中的說明配置這些設置。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 計畫為 VMware VM 擴展 Azure 遷移評估
> * 準備 Azure 和 VMware 進行評估
> * 創建 Azure 遷移專案並運行評估
> * 審查了為遷移做準備的評估。

現在，[瞭解如何](concepts-assessment-calculation.md)計算評估，以及如何[修改評估](how-to-modify-assessment.md)。
