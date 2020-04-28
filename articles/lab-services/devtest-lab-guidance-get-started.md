---
title: 使用 Azure DevTest Labs 的熱門案例
description: 本文提供使用 Azure DevTest Labs 和兩個一般路徑來開始在組織中使用服務的主要案例。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "60773803"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>使用 Azure DevTest Labs 的熱門案例
視企業的需求而定，您可以設定 DevTest Labs 以符合不同的需求。  本文討論熱門案例。 每個案例都涵蓋使用 DevTest Labs 和用來執行這些案例的資源所帶來的好處。  

- 開發人員桌面
- 測試環境
- 訓練課程、實際操作實驗室和駭客松
- 沙箱化調查
- 教室實驗室

## <a name="developer-desktops"></a>開發人員桌面
開發人員針對不同專案的開發電腦通常有不同的需求。 透過 DevTest Labs，開發人員可以存取已設定為符合其最常見案例的隨選虛擬機器。 DevTest Labs 提供下列權益：

- 組織可以提供常見的開發電腦，確保各小組之間的一致性。
- 開發人員可以視需要快速布建其開發電腦，或宣告[現有預先設定的電腦](devtest-lab-add-claimable-vm.md)。
- 開發人員可以透過自助方式布建資源，而不需要訂用帳戶層級許可權。
- IT 或系統管理員可以[預先定義網路拓撲](devtest-lab-configure-vnet.md)，而開發人員可以用簡單且直覺的方式直接使用它，而不需要任何特殊的存取權。
- 開發人員可以視需要輕鬆地[自訂](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)他們的開發電腦。
- 系統管理員可以確定下列事項以控制成本：
    - 開發人員[無法取得](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)超過開發所需的 vm
    - [Vm](devtest-lab-set-lab-policy.md#set-auto-shutdown)在未使用時關閉
    - 僅允許特定實驗室的[VM 實例大小子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每個實驗室的[成本目標和通知](devtest-lab-configure-cost-management.md)。

如需進一步閱讀，請參閱[使用適用于開發人員的 Azure DevTest Labs](devtest-lab-developer-lab.md)。 

## <a name="test-environments"></a>測試環境
在整個企業內建立和管理測試環境，可能需要投入更多努力。 透過 DevTest Labs，可以輕鬆地建立、更新或複製測試環境。 它可讓小組在需要時存取已完全設定的環境。 在此案例中，DevTest Labs 提供以下權益：

- 組織可以提供常見的測試環境，確保小組之間的一致性。
- 測試人員可以藉由使用可重複使用的範本，快速布建 Windows 和 Linux 環境，來測試其應用程式的最新版本。
- 系統管理員可以將實驗室連線至 Azure DevOps，以啟用 DevOps 案例
- 實驗室擁有者可以藉由確保下列事項來控制成本：
    - [環境中的 vm 會](devtest-lab-set-lab-policy.md#set-auto-shutdown)在不使用時關閉
    - 僅允許特定實驗室的[VM 實例大小子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每個實驗室的[成本目標和通知](devtest-lab-configure-cost-management.md)。

如需詳細資訊，請參閱[使用 VM 和 PaaS 測試環境的 Azure DevTest Labs](devtest-lab-test-env.md)。

## <a name="sandboxed-investigations"></a>沙箱化調查
開發人員通常會調查不同的技術或基礎結構設計。 根據預設，使用 DevTest Labs 建立的所有環境都會建立在自己的資源群組中。 DevTest Labs 使用者只會取得這些資源的讀取權限。 不過，針對需要更多控制的開發人員，可以更新實驗室範圍的設定，以針對他們所建立的每個環境，將[參與者許可權](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/)授與原始 DevTest 實驗室使用者。  有了 DevTest Labs，開發人員就可以自動獲得參與者許可權給他們在實驗室中建立的環境。  此案例可讓開發人員新增和/或變更其開發或測試環境所需的 Azure 資源。 [[依資源的成本](devtest-lab-configure-cost-management.md#view-cost-by-resource)] 頁面可讓實驗室擁有者追蹤用於調查之每個環境的成本。

如需詳細資訊，請參閱[設定環境資源群組的存取權限](https://aka.ms/dtl-sandbox)。

## <a name="trainings-hands-on-labs-and-hackathons"></a>訓練、實際操作實驗室和駭客松 
Azure DevTest Labs 中的實驗室可做為暫時性活動的絕佳容器，例如研討會、實習實驗室、訓練或駭客松。  此服務可讓您建立實驗室，在其中提供自訂範本供每位受訓者建立相同且隔離的訓練環境。 在此案例中，DevTest Labs 提供以下權益：

- [原則](devtest-lab-set-lab-policy.md)可確保受訓者只會取得所需的資源（例如虛擬機器）數目。
- 預先設定和建立的機器會透過受訓者的單一動作來[宣告](devtest-lab-add-claimable-vm.md)。
- 實驗室會透過存取[實驗室的 URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)來與受訓者共用。
- 虛擬機器上的[到期](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)日會確保電腦在不再需要之後才會刪除。
- 當定型結束時，很容易就能[刪除實驗室](devtest-lab-delete-lab-vm.md#delete-a-lab)和所有[相關資源](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)。

如需詳細資訊，請參閱[使用 Azure DevTest Labs 進行定型](devtest-lab-training-lab.md)。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>概念證明與調整的部署
在您決定要探索 DevTest Labs 之後，有兩個一般路徑向前：概念證明與已調整的部署。  

**經過調整的部署**包含檢閱與規劃的週/月，其目的是將 DevTest Labs 部署至擁有數百或數千名開發人員的整個企業。

**概念證明**部署著重于單一小組的集中工作，以建立組織的價值。 雖然很容易讓人聯想到經過調整的部署，但此方法往往比證明概念選項更容易失敗。 因此，我們建議從小規模開始、從第一個小組進行了解、透過二至三個其他小組重複使用相同方法，然後根據所獲得的知識來規劃經過調整的部署。 對於成功的概念證明，我們建議您挑選一或兩個小組，並識別他們的案例 (開發環境與測試環境)、記錄其目前的使用案例，以及部署 DevTest Labs。

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [研發/測試實驗室概念](devtest-lab-concepts.md)
- [DevTest Labs 常見問題集](devtest-lab-faq.md)

