---
title: 關於 Azure DevTest Labs | Microsoft Docs
description: 了解 DevTest Labs 如何讓您輕鬆地建立、管理和監視 Azure 虛擬機器
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561450"
---
# <a name="about-azure-devtest-labs"></a>關於 Azure DevTest Labs
Azure 開發人員測試實驗室使團隊中的開發人員能夠高效自管理虛擬機器 （VM） 和 PaaS 資源，而無需等待批准。

DevTest 實驗室創建由預配置的基礎或 Azure 資源管理器範本組成的實驗室。 這些工具和軟體可用於創建環境。 您可以在幾分鐘內創建環境，而不是數小時或數天。

通過使用 DevTest Labs，您可以通過執行以下任務來測試應用程式的最新版本：

- 使用可重用的範本和工件快速預配 Windows 和 Linux 環境。
- 將您的部署管線與研發/測試實驗室輕鬆整合，來佈建隨選環境。
- 通過預配多個測試代理並創建用於培訓和演示的預配置環境，從而擴展負載測試。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
DevTest Labs 為使用 VM 的開發人員提供以下功能：

- 通過執行少於五個簡單步驟，快速建立 VM。
- 從由團隊領導或中央 IT 配置、批准和授權的 VM 基礎的精選清單中選擇。
- 從已安裝所有軟體和工具的預創建自訂映射創建 VM。 
- 從本質上是自訂映射的公式創建 VM，這些公式與創建 VM 時安裝的軟體的最新版本相結合。 
- 在 VM 預配後安裝部署在 VM 上的擴展專案。
- 在 VM 上設置自動關閉和自動啟動計畫。
- 聲明預創建的 VM 而不完成創建過程。

DevTest Labs 為使用 PaaS 環境的開發人員提供以下功能：

- 使用資源管理器通過執行少於三個簡單步驟來快速建立 PaaS 環境。
- 從精心策劃的資源管理器範本清單中進行選擇，這些範本由團隊領導或中央 IT 人員配置並授權。
- 通過使用資源管理器範本在實驗室的上下文中流覽 Azure，將空資源組（沙箱）旋轉。

DevTest Labs 還使中央 IT 部門能夠控制浪費、優化資源成本，並通過執行以下任務保持預算範圍：  

- 在 VM 上設置自動關閉和自動啟動計畫。
- 設置使用者可以創建的 VM 數量的策略。
- 設置使用者選擇的 VM 大小和庫圖像的策略。
- 跟蹤成本並設定實驗室目標。
- 獲得實驗室高預計成本的通知，以便您可以採取必要的行動。

DevTest Labs 在創建、配置和管理雲中的環境時提供了以下優勢。

## <a name="cost-control-and-governance"></a>成本控制和治理
DevTest Labs 允許您執行以下任務，從而更輕鬆地控制成本：

- [在實驗室上設置策略](devtest-lab-get-started-with-lab-policies.md)，例如每個使用者或每個實驗室的 VM 數。 
- 創建[策略以自動關閉](devtest-lab-set-lab-policy.md)和啟動 VM。
- 跟蹤 VM 和 PaaS 資源的成本，在實驗室內啟動，以保持在[您的預算](devtest-lab-configure-cost-management.md)之內。
- 保持在實驗室的上下文中，這樣您就不會在實驗室之外啟動資源。

## <a name="quickly-get-to-ready-to-test"></a>快速做好測試的準備
DevTest Labs 允許您創建預配置的環境，並配備您的團隊開發和測試應用程式所需的一切。 只需[聲明](devtest-lab-add-claimable-vm.md)安裝應用程式最後一個良好版本的環境並開始工作。 或使用容器創建更快、更精簡的環境。

## <a name="create-once-use-everywhere"></a>一次建立，隨處可用
在團隊或組織中捕獲和共用 PaaS[環境範本](devtest-lab-create-environment-from-arm.md)和[專案](add-artifact-repository.md)（全部在原始程式碼管理中），輕鬆創建開發人員和測試環境。

## <a name="worry-free-self-service"></a>零煩惱的自助方式
DevTest Labs 使開發人員和測試人員能夠使用一組預配置的資源快速輕鬆地[創建 IaaS VM](devtest-lab-add-vm.md)和[PaaS 資源](devtest-lab-create-environment-from-arm.md)。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 資源 
開發人員還可以通過使用資源管理器範本來旋轉 PaaS 資源，例如 Azure 服務結構群集、Azure 應用服務的 Web 應用功能和 SharePoint 伺服器場。 要在實驗室中開始使用 PaaS，請使用[公共環境存儲庫](devtest-lab-configure-use-public-environments.md)中的範本或[將實驗室連接到您自己的 Git 存儲庫](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 您還可以跟蹤這些資源的成本，以保持在預算範圍內。

## <a name="integrate-with-your-existing-toolchain"></a>與現有工具鏈集成
使用預先製作的外掛程式或 API 直接從您首選[的連續集成 （CI） 工具](devtest-lab-integrate-ci-cd-vsts.md)、整合式開發環境 （IDE） 或自動發佈管道中預配開發/測試環境。 您還可以使用全面的命令列工具。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- 要瞭解有關 DevTest 實驗室的更多內容，請參閱[開發人員測試實驗室概念](devtest-lab-concepts.md)。
- 有關分步說明的演練，請參閱[教程：使用 Azure 開發人員測試實驗室設置實驗室](tutorial-create-custom-lab.md)。