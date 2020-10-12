---
title: 使用 Azure Migrate 評定大量的 VMware Vm 以遷移至 Azure
description: 說明如何使用 Azure Migrate 服務，評定大量的 VMware Vm 以遷移至 Azure。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 6490a5448bb68dcccd61784d149e9765107400c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171912"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>評定大量的 VMware Vm 以遷移至 Azure


本文說明如何使用 Azure Migrate 伺服器評量工具，來評估內部部署 VMware Vm (35000) 的大型數位，以遷移至 Azure。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 

在本文中，您將學會如何：
> [!div class="checklist"]
> * 大規模規劃評量。
> * 設定 Azure 許可權，並準備 VMware 進行評量。
> * 建立 Azure Migrate 專案，並建立評量。
> * 當您計畫進行遷移時，請參閱評量。


> [!NOTE]
> 如果您想要在大規模評估之前，嘗試評估幾個 Vm，請遵循我們的 [教學課程系列](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>規劃評量

規劃大量 VMware Vm 的評量時，需要考慮幾個事項：

- **規劃 Azure Migrate 專案**：找出如何部署 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或您需要將探索、評量或遷移相關的中繼資料儲存在不同的地理位置，您可能需要多個專案。 
- **規劃設備**： Azure Migrate 使用內部部署 Azure Migrate 設備部署為 VMware VM，以持續探索 vm。 設備會監視環境變更，例如新增 Vm、磁片或網路介面卡。 它也會將相關的中繼資料和效能資料傳送至 Azure。 您必須找出需要部署的應用裝置數量。
- **規劃帳戶以進行探索**： Azure Migrate 設備會使用可存取 vCenter Server 的帳戶，以探索 vm 以進行評量和遷移。 如果您要探索10000以上的 Vm，請設定多個帳戶，因為在需要從專案中的任何兩個設備探索到的 Vm 之間不會重迭。 

> [!NOTE]
> 如果您要設定多個應用裝置，請確定提供的 vCenter 帳戶上的 Vm 之間沒有重迭。 若有這類重疊，則不支援探索。 如果有多個設備探索到 VM，這會導致探索和發生問題，並在伺服器遷移中使用 Azure 入口網站為 VM 啟用複寫。

## <a name="planning-limits"></a>規劃限制
 
使用下表中摘要說明的限制進行規劃。

**規劃** | **限制**
--- | --- 
**Azure Migrate 專案** | 在專案中評定最多35000個 Vm。
**Azure Migrate 設備** | 設備最多可探索 vCenter Server 上的 10000 Vm。<br/> 設備只能連線到單一 vCenter Server。<br/> 設備只能與單一 Azure Migrate 專案相關聯。<br/>  任何數量的設備都可以與單一 Azure Migrate 專案相關聯。 <br/><br/> 
**群組** | 您最多可以在單一群組中新增35000個 Vm。
**Azure Migrate 評量** | 您最多可以在單一評估中評估 35,000 個 VM。

考慮這些限制之後，以下是一些部署範例：


**vCenter server** | **伺服器上的 Vm** | **建議** | **動作**
---|---|---|---
一個 | < 10000 | 一個 Azure Migrate 專案。<br/> 一個設備。<br/> 一個用於探索的 vCenter 帳戶。 | 設定設備，以帳戶連接到 vCenter Server。
一個 | > 10000 | 一個 Azure Migrate 專案。<br/> 多個設備。<br/> 多個 vCenter 帳戶。 | 為每個 10000 Vm 設定設備。<br/><br/> 設定 vCenter 帳戶和劃分清查，以將帳戶的存取限制為低於10000的 Vm。<br/> 使用帳戶將每個設備連線到 vCenter server。<br/> 您可以分析使用不同設備探索到的機器之間的相依性。 <br/> <br/> 請確定提供的 vCenter 帳戶上的 Vm 之間沒有重迭。 若有這類重疊，則不支援探索。 如果有一個以上的設備探索到 VM，這會導致探索和發生問題，並在伺服器遷移中使用 Azure 入口網站為 VM 啟用複寫。
多個 | < 10000 |  一個 Azure Migrate 專案。<br/> 多個設備。<br/> 一個用於探索的 vCenter 帳戶。 | 設定設備，以帳戶連接到 vCenter Server。<br/> 您可以分析使用不同設備探索到的機器之間的相依性。
多個 | > 10000 | 一個 Azure Migrate 專案。<br/> 多個設備。<br/> 多個 vCenter 帳戶。 | 如果 vCenter Server 探索 < 10000 Vm，請為每個 vCenter Server 設定設備。<br/><br/> 如果 vCenter Server 探索 > 10000 Vm，請為每個 10000 Vm 設定設備。<br/> 設定 vCenter 帳戶和劃分清查，以將帳戶的存取限制為低於10000的 Vm。<br/> 使用帳戶將每個設備連線到 vCenter server。<br/> 您可以分析使用不同設備探索到的機器之間的相依性。 <br/><br/> 請確定提供的 vCenter 帳戶上的 Vm 之間沒有重迭。 若有這類重疊，則不支援探索。 如果有一個以上的設備探索到 VM，這會導致探索和發生問題，並在伺服器遷移中使用 Azure 入口網站為 VM 啟用複寫。



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>在多租使用者環境中規劃探索

如果您正在規劃多租使用者環境，則可以將 vCenter Server 的探索範圍。

- 您可以將設備探索範圍設定為 vCenter Server 的資料中心、叢集或叢集的資料夾、主機的主機或主機，或個別 Vm 的群組。
- 如果您的環境是跨租使用者共用，而且您想要個別探索每個租使用者，您可以將存取權的範圍設為設備用來進行探索的 vCenter 帳戶。 
    - 如果租使用者共用主機，您可能會想要依 VM 資料夾進行範圍。 如果 vCenter 帳戶具有在 vCenter VM 資料夾層級授與的存取權，Azure Migrate 無法探索 Vm。 如果您想要依據 VM 資料夾來界定探索範圍，只要確保 vCenter 帳戶在 VM 層級上已獲派唯讀存取權即可。 [深入了解](set-discovery-scope.md)。

## <a name="prepare-for-assessment"></a>準備進行評估

準備 Azure 和 VMware 進行伺服器評量。 

1. 確認 [VMware 支援需求和限制](migrate-support-matrix-vmware.md)。
2. 為您的 Azure 帳戶設定許可權，以與 Azure Migrate 互動。
3. 準備 VMware 進行評量。

遵循 [本教學](tutorial-prepare-vmware.md) 課程中的指示來進行這些設定。


## <a name="create-a-project"></a>建立專案

根據您的規劃需求，請執行下列動作：

1. 建立 Azure Migrate 專案。
2. 將 Azure Migrate Server 評定工具新增至專案。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>建立和審核評量

1. 建立 VMware Vm 的評量。
1. 請參閱評定以準備進行遷移規劃。


遵循 [本教學](tutorial-assess-vmware.md) 課程中的指示來進行這些設定。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 規劃為 VMware Vm 調整 Azure Migrate 評量
> * 準備好 Azure 和 VMware 進行評量
> * 建立 Azure Migrate 專案並執行評量
> * 已檢查要準備進行遷移的評量。

現在， [瞭解如何](concepts-assessment-calculation.md) 計算評定，以及如何 [修改評](how-to-modify-assessment.md)量。
