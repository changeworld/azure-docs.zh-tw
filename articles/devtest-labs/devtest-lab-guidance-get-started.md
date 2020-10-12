---
title: 使用 Azure DevTest Labs 的熱門案例
description: 本文提供使用 Azure DevTest Labs 的主要案例，以及在組織中開始使用服務的兩個一般途徑。
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481590"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>使用 Azure DevTest Labs 的熱門案例
根據企業的需求，DevTest Labs 可以設定為符合不同的需求。  本文討論熱門案例。 每個案例都涵蓋使用 DevTest Labs 和資源來實行這些案例所帶來的好處。  

- 開發人員桌面
- 測試環境
- 訓練課程、實際操作實驗室和駭客松
- 沙箱調查
- 教室實驗室

## <a name="developer-desktops"></a>開發人員桌面
開發人員針對不同專案的開發電腦通常有不同的需求。 使用 DevTest Labs，開發人員可以存取已設定為符合其最常見案例的隨選虛擬機器。 DevTest Labs 提供下列權益：

- 組織可以提供一般開發電腦，確保小組之間的一致性。
- 開發人員可以視需要快速布建開發電腦，或 [索取現有的預先設定電腦](devtest-lab-add-claimable-vm.md)。
- 開發人員可以在不需要訂用帳戶層級許可權的情況下，以自助方式布建資源。
- IT 或系統管理員可以 [預先定義網路拓撲](devtest-lab-configure-vnet.md) ，而開發人員可以直接以簡單且直覺的方式使用它，而不需要任何特殊存取。
- 開發人員可以視需要輕鬆地 [自訂](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) 他們的開發電腦。
- 系統管理員可以確定下列事項以控制成本：
    - 開發人員 [無法取得](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) 超過開發所需的 vm
    - [Vm](devtest-lab-set-lab-policy.md#set-auto-shutdown) 在未使用時關閉
    - 僅允許特定實驗室的[VM 實例大小子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每個實驗室的[成本目標和通知](devtest-lab-configure-cost-management.md)。

如需進一步閱讀，請參閱 [使用適用于開發人員的 Azure DevTest Labs](devtest-lab-developer-lab.md)。 

## <a name="test-environments"></a>測試環境
在整個企業內建立和管理測試環境，可能需要投入更多努力。 使用 DevTest Labs，可以輕鬆地建立、更新或複製測試環境。 它可讓小組在需要時，存取完整設定的環境。 在此案例中，DevTest Labs 提供以下權益：

- 組織可以提供常見的測試環境，以確保小組之間的一致性。
- 測試人員可以使用可重複使用的範本，快速布建 Windows 和 Linux 環境，來測試其應用程式的最新版本。
- 系統管理員可以將實驗室連線到 Azure DevOps 以啟用 DevOps 案例
- 實驗室擁有者可以藉由確保下列各項來控制成本：
    - [環境中的 vm 未使用時，會關閉](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - 僅允許特定實驗室的[VM 實例大小子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每個實驗室的[成本目標和通知](devtest-lab-configure-cost-management.md)。

如需詳細資訊，請參閱 [使用 VM 和 PaaS 測試環境 Azure DevTest Labs](devtest-lab-test-env.md)。

## <a name="sandboxed-investigations"></a>沙箱調查
開發人員通常會調查不同的技術或基礎結構設計。 依預設，使用 DevTest Labs 建立的所有環境都會建立在自己的資源群組中。 DevTest Labs 使用者只會取得這些資源的讀取權限。 不過，對於需要更多控制的開發人員，您可以更新整個實驗室的設定，以將 [參與者許可權](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) 授與所建立之每個環境的原始 DevTest Labs 使用者。  有了 DevTest Labs，開發人員就可以將參與者許可權自動授與給他們在實驗室中建立的環境。  此案例可讓開發人員在其開發或測試環境中，依需要新增和/或變更 Azure 資源。 [ [依資源的成本](devtest-lab-configure-cost-management.md#view-cost-by-resource) ] 頁面可讓實驗室擁有者追蹤每個用於調查的環境成本。

如需詳細資訊，請參閱 [設定環境資源群組的存取權限](https://aka.ms/dtl-sandbox)。

## <a name="trainings-hands-on-labs-and-hackathons"></a>訓練、實際操作實驗室和駭客松 
Azure DevTest Labs 中的實驗室可作為暫時性活動的絕佳容器，例如研討會、實際操作實驗室、訓練或駭客松。  此服務可讓您建立實驗室，在其中提供自訂範本供每位受訓者建立相同且隔離的訓練環境。 在此案例中，DevTest Labs 提供以下權益：

- [原則](devtest-lab-set-lab-policy.md) 可確保受訓者只會取得其所需的資源數目，例如虛擬機器。
- 預先設定和建立的機器會透過受訓者的單一動作來 [宣告](devtest-lab-add-claimable-vm.md) 。
- 實驗室會透過存取 [實驗室的 URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)，與受訓者共用。
- 虛擬機器上的[到期](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)日，可確保在不再需要機器之後將其刪除。
- 定型結束時，很容易就能 [刪除實驗室](devtest-lab-delete-lab-vm.md#delete-a-lab) 和所有 [相關資源](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) 。

如需詳細資訊，請參閱 [使用 Azure DevTest Labs 進行定型](devtest-lab-training-lab.md)。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>概念證明與調整的部署
當您決定探索 DevTest Labs 之後，有兩個一般途徑：概念證明與調整的部署。  

**經過調整的部署**包含檢閱與規劃的週/月，其目的是將 DevTest Labs 部署至擁有數百或數千名開發人員的整個企業。

**概念證明**部署著重于單一小組的集中工作，以建立組織的價值。 雖然很容易讓人聯想到經過調整的部署，但此方法往往比證明概念選項更容易失敗。 因此，我們建議從小規模開始、從第一個小組進行了解、透過二至三個其他小組重複使用相同方法，然後根據所獲得的知識來規劃經過調整的部署。 對於成功的概念證明，我們建議您挑選一或兩個小組，並識別他們的案例 (開發環境與測試環境)、記錄其目前的使用案例，以及部署 DevTest Labs。

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [研發/測試實驗室概念](devtest-lab-concepts.md)
- [DevTest Labs 常見問題集](devtest-lab-faq.md)

