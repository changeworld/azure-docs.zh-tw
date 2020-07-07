---
title: 使用 Azure Migrate 評定大量的 VMware Vm 以遷移至 Azure
description: 說明如何使用 Azure Migrate 服務，評估大量的 VMware Vm 以遷移至 Azure。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80336862"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>評估大量的 VMware Vm 以遷移至 Azure


本文說明如何使用 Azure Migrate Server 評估工具，評估內部部署 VMware Vm 的大量（1000-35000）以遷移至 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 

在本文中，您將學會如何：
> [!div class="checklist"]
> * 規劃大規模的評估。
> * 設定 Azure 許可權，並準備 VMware 進行評估。
> * 建立 Azure Migrate 專案，並建立評量。
> * 當您規劃遷移時，請檢查評量。


> [!NOTE]
> 如果您想要在大規模評估之前嘗試評估一些 Vm 的概念證明，請遵循我們的[教學課程系列](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>規劃評估

規劃大量 VMware Vm 的評估時，有幾件事需要考慮：

- **規劃 Azure Migrate 專案**：瞭解如何部署 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或是您需要將探索、評定或遷移相關的中繼資料儲存在不同的地理位置，您可能需要多個專案。 
- **規劃設備**： Azure Migrate 使用部署為 VMware VM 的內部部署 Azure Migrate 設備，持續探索 vm。 設備會監視環境變更，例如新增 Vm、磁片或網路介面卡。 它也會將其相關的中繼資料和效能資料傳送至 Azure。 您必須找出需要部署的應用裝置數量。
- **規劃探索的帳戶**： Azure Migrate 設備使用可存取 vCenter Server 的帳戶，以探索用於評估和遷移的 vm。 如果您要探索的 Vm 超過10000個，請設定多個帳戶。


## <a name="planning-limits"></a>規劃限制
 
使用此表中摘要說明的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure Migrate 專案** | 在專案中評估最多35000個 Vm。
**Azure Migrate 設備** | 設備可以在 vCenter Server 上探索最多10000部 Vm。<br/> 設備只能連接到單一 vCenter Server。<br/> 設備只能與單一 Azure Migrate 專案相關聯。<br/>  任何數目的設備都可以與單一 Azure Migrate 專案相關聯。 <br/><br/> 
**群組** | 您最多可以在單一群組中新增35000個 Vm。
**Azure Migrate 評量** | 您最多可以在單一評估中評估 35,000 個 VM。

考慮這些限制之後，以下是一些部署範例：


**vCenter server** | **伺服器上的 Vm** | **建議** | **動作**
---|---|---
一個 | < 10000 | 一個 Azure Migrate 專案。<br/> 一個設備。<br/> 一個用於探索的 vCenter 帳戶。 | 設定設備，使用帳戶連接到 vCenter Server。
一個 | > 10000 | 一個 Azure Migrate 專案。<br/> 多個設備。<br/> 多個 vCenter 帳戶。 | 為每個 10000 Vm 設定設備。<br/><br/> 設定 vCenter 帳戶，並劃分清查以將帳戶的存取許可權制為少於10000個 Vm。<br/> 使用帳戶將每個設備連接到 vCenter server。<br/> 您可以分析使用不同設備探索到的機器之間的相依性。
多個 | < 10000 |  一個 Azure Migrate 專案。<br/> 多個設備。<br/> 一個用於探索的 vCenter 帳戶。 | 設定設備，使用帳戶連接到 vCenter Server。<br/> 您可以分析使用不同設備探索到的機器之間的相依性。
多個 | > 10000 | 一個 Azure Migrate 專案。<br/> 多個設備。<br/> 多個 vCenter 帳戶。 | 如果 vCenter Server 探索 < 10000 Vm，請為每個 vCenter Server 設定設備。<br/><br/> 如果 vCenter Server 探索 > 10000 Vm，請為每個 10000 Vm 設定設備。<br/> 設定 vCenter 帳戶，並劃分清查以將帳戶的存取許可權制為少於10000個 Vm。<br/> 使用帳戶將每個設備連接到 vCenter server。<br/> 您可以分析使用不同設備探索到的機器之間的相依性。


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>在多租使用者環境中規劃探索

如果您打算使用多租使用者環境，可以將探索範圍限定在 vCenter Server。

- 您可以將設備探索範圍設定為 vCenter Server 的資料中心、叢集或叢集的叢集、主機或主機或個別 Vm 的資料夾。
- 如果您的環境在租使用者之間共用，而您想要個別探索每個租使用者，您可以將存取許可權定為設備用於探索的 vCenter 帳戶。 
    - 如果租使用者共用主機，您可能會想要依 VM 資料夾進行範圍。 如果 vCenter 帳戶具有在 vCenter VM 資料夾層級上授與的存取權，Azure Migrate 就無法探索 Vm。 如果您想要依據 VM 資料夾來界定探索範圍，只要確保 vCenter 帳戶在 VM 層級上已獲派唯讀存取權即可。 [深入了解](set-discovery-scope.md)。

## <a name="prepare-for-assessment"></a>準備進行評估

準備 Azure 和 VMware 以進行伺服器評估。 

1. 確認[VMware 支援需求和限制](migrate-support-matrix-vmware.md)。
2. 設定 Azure 帳戶的許可權，以與 Azure Migrate 進行互動。
3. 準備 VMware 進行評估。

請遵循[本教學](tutorial-prepare-vmware.md)課程中的指示來進行這些設定。


## <a name="create-a-project"></a>建立專案

根據您的規劃需求，執行下列動作：

1. 建立 Azure Migrate 專案。
2. 將 [Azure Migrate Server 評估工具] 新增至專案。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>建立和審查評量

1. 建立 VMware Vm 的評量。
1. 請參閱評估以準備進行遷移計畫。


請遵循[本教學](tutorial-assess-vmware.md)課程中的指示來進行這些設定。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 規劃調整 VMware Vm 的 Azure Migrate 評量
> * 準備好 Azure 和 VMware 進行評估
> * 建立 Azure Migrate 專案並執行評量
> * 已審查評估以準備進行遷移。

現在，[瞭解如何](concepts-assessment-calculation.md)計算評定，以及如何[修改評](how-to-modify-assessment.md)量。
