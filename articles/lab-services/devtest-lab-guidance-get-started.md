---
title: 使用 Azure 開發人員測試實驗室的常用方案
description: 本文提供了使用 Azure DevTest Labs 的主要方案以及兩條在組織中開始使用服務的一般路徑。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773803"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>使用 Azure 開發人員測試實驗室的常用方案
根據企業的需求，可以配置 DevTest Labs 以滿足不同的要求。  本文討論了流行的方案。 每個方案都涵蓋通過使用 DevTest Labs 帶來的優勢以及用於實施這些方案的資源。  

- 開發人員桌面
- 測試環境
- 培訓課程、動手實驗和駭客馬拉松
- 沙箱調查
- 教室實驗室

## <a name="developer-desktops"></a>開發人員桌面
開發人員針對不同專案的開發電腦通常有不同的需求。 借助 DevTest Labs，開發人員可以訪問配置為適合其最常見方案的按需虛擬機器。 DevTest Labs 提供下列權益：

- 組織可以提供通用開發機器，確保團隊之間的一致性。
- 開發人員可以按需快速調配其開發電腦，或[要求現有預配置的電腦](devtest-lab-add-claimable-vm.md)。
- 開發人員可以自助方式預配資源，而無需訂閱級許可權。
- IT 或管理員可以[預先定義網路拓撲](devtest-lab-configure-vnet.md)，開發人員可以直接以簡單直觀的方式使用它，而無需任何特殊訪問。
- 開發人員可以輕鬆地根據需要[自訂](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)其開發電腦。
- 系統管理員可以確定下列事項以控制成本：
    - 開發人員[無法獲得](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)比開發所需的更多 VM
    - 不使用時[，VM 將關閉](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - [僅允許特定實驗室的 VM 實例大小的子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - [管理每個實驗室的成本目標和通知](devtest-lab-configure-cost-management.md)。

有關進一步閱讀，請參閱[為開發人員使用 Azure 開發人員測試實驗室](devtest-lab-developer-lab.md)。 

## <a name="test-environments"></a>測試環境
在整個企業內建立和管理測試環境，可能需要投入更多努力。 借助 DevTest 實驗室，可以輕鬆創建、更新或複製測試環境。 它允許團隊在需要時訪問完全配置的環境。 在此案例中，DevTest Labs 提供以下權益：

- 組織可以提供通用的測試環境，確保團隊之間的一致性。
- 測試人員可以通過使用可重用的範本快速預配 Windows 和 Linux 環境來測試其應用程式的最新版本。
- 管理員可以將實驗室連接到 Azure DevOps，以啟用 DevOps 方案
- 實驗室擁有者可以通過確保：
    - 不使用時，[環境中的 VM 將關閉](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - [僅允許特定實驗室的 VM 實例大小的子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - [管理每個實驗室的成本目標和通知](devtest-lab-configure-cost-management.md)。

有關詳細資訊，請參閱將[Azure 開發人員測試實驗室用於 VM 和 PaaS 測試環境](devtest-lab-test-env.md)。

## <a name="sandboxed-investigations"></a>沙箱調查
開發人員經常調查不同的技術或基礎結構設計。 預設情況下，使用 DevTest Labs 創建的所有環境都在其自己的資源組中創建。 開發人員測試實驗室使用者只能讀取對這些資源的存取權限。 但是，對於需要更多控制的開發人員，可以更新實驗室範圍的設置，以便為其創建的每個環境向源的 DevTest Labs 使用者授予[參與者許可權](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/)。  借助 DevTest Labs，開發人員可以自動獲得他們在實驗室中創建的環境的參與者許可權。  此方案允許開發人員根據需要添加和/或更改 Azure 資源，以進行開發或測試環境。 [按資源計的成本](devtest-lab-configure-cost-management.md#view-cost-by-resource)頁允許實驗室擁有者跟蹤用於調查的每個環境的成本。

有關詳細資訊，請參閱[設置對環境資源組的存取權限](https://aka.ms/dtl-sandbox)。

## <a name="trainings-hands-on-labs-and-hackathons"></a>培訓、動手實驗和駭客馬拉松 
Azure DevTest 實驗室中的實驗室充當臨時活動（如研討會、動手實驗、培訓或駭客馬拉松）的絕佳容器。  此服務可讓您建立實驗室，在其中提供自訂範本供每位受訓者建立相同且隔離的訓練環境。 在此案例中，DevTest Labs 提供以下權益：

- [策略](devtest-lab-set-lab-policy.md)確保學員只能獲得所需的資源（如虛擬機器）的數量。
- 預配置和創建的機器由受訓者單一操作[進行聲明](devtest-lab-add-claimable-vm.md)。
- 通過訪問[實驗室的 URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)與學員共用實驗室。
- 虛擬機器上的[到期日期](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)可確保在不再需要電腦後將其刪除。
- 培訓結束後，很容易[刪除實驗室](devtest-lab-delete-lab-vm.md#delete-a-lab)和[所有相關資源](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)。

有關詳細資訊，請參閱使用[Azure 開發人員測試實驗室進行培訓](devtest-lab-training-lab.md)。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>概念證明與擴展部署
一旦您決定探索 DevTest Labs，有兩條常規路徑：概念驗證與縮放部署。  

**經過調整的部署**包含檢閱與規劃的週/月，其目的是將 DevTest Labs 部署至擁有數百或數千名開發人員的整個企業。

**概念證明**部署側重于單個團隊為建立組織價值而集中進行的工作。 雖然很容易讓人聯想到經過調整的部署，但此方法往往比證明概念選項更容易失敗。 因此，我們建議從小規模開始、從第一個小組進行了解、透過二至三個其他小組重複使用相同方法，然後根據所獲得的知識來規劃經過調整的部署。 對於成功的概念證明，我們建議您挑選一或兩個小組，並識別他們的案例 (開發環境與測試環境)、記錄其目前的使用案例，以及部署 DevTest Labs。

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [研發/測試實驗室概念](devtest-lab-concepts.md)
- [DevTest Labs 常見問題集](devtest-lab-faq.md)

