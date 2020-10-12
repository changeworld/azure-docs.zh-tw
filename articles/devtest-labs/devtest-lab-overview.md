---
title: 關於 Azure DevTest Labs | Microsoft Docs
description: 了解 DevTest Labs 如何讓您輕鬆地建立、管理和監視 Azure 虛擬機器
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480196"
---
# <a name="about-azure-devtest-labs"></a>關於 Azure DevTest Labs
Azure DevTest Labs 可讓小組的開發人員有效率地自行管理虛擬機器 (Vm) 和 PaaS 資源，而不需要等待核准。

DevTest Labs 會建立實驗室，其中包含預先設定的基底或 Azure Resource Manager 範本。 這些都有您可以用來建立環境的所有必要工具和軟體。 您可以在幾分鐘內建立環境，而不是數小時或數天。

藉由使用 DevTest Labs，您可以執行下列工作來測試應用程式的最新版本：

- 使用可重複使用的範本和構件，快速布建 Windows 和 Linux 環境。
- 將您的部署管線與研發/測試實驗室輕鬆整合，來佈建隨選環境。
- 布建多個測試代理程式，並建立預先布建的環境進行訓練和示範，以擴大負載測試。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
DevTest Labs 為使用 Vm 的開發人員提供下列功能：

- 遵循五個簡單的步驟，快速建立 Vm。
- 從已設定、核准及授權的 VM 基底策劃清單中選擇，以供小組負責人或中央 IT 人員使用。
- 從預先建立的自訂映射建立 Vm，這些映射已安裝所有的軟體和工具。 
- 從作為自訂映射的公式建立 Vm，這些公式與建立 Vm 時所安裝之軟體的最新組建合併。 
- 在布建 Vm 之後，安裝部署于 Vm 上的構件。
- 在 Vm 上設定自動關機和自動啟動排程。
- 宣告預先建立的 VM，而不需要完成建立程式。

DevTest Labs 為使用 PaaS 環境的開發人員提供下列功能：

- 使用 Resource Manager，只要遵循三個簡單的步驟，就能快速建立 PaaS 環境。
- 從策劃 Resource Manager 的範本清單中選擇，這些範本已設定，並由小組負責人或中央 IT 授權。
- 使用 Resource Manager 範本在實驗室的環境內探索 Azure，以 (沙箱) 來啟動空的資源群組。

DevTest Labs 也可讓 IT 中心 IT 用來控制浪費、將資源的成本優化，並藉由執行下列工作來維持在預算內：  

- 在 Vm 上設定自動關機和自動啟動排程。
- 設定使用者可建立之 Vm 數目的原則。
- 在 Vm 的大小和資源庫映射上設定使用者選擇的原則。
- 追蹤成本及設定實驗室的目標。
- 取得實驗室的高預測成本通知，讓您可以採取必要的動作。

DevTest Labs 可讓您在雲端中建立、設定和管理環境方面提供下列優點。

## <a name="cost-control-and-governance"></a>成本控制和治理
DevTest Labs 可讓您執行下列工作，讓您更輕鬆地控制成本：

- [在您的實驗室上設定原則](devtest-lab-set-lab-policy.md)，例如每位使用者或每個實驗室的 vm 數目。 
- 建立 [原則以自動關閉](devtest-lab-set-lab-policy.md) 和啟動 vm。
- 追蹤 Vm 的成本，並在實驗室內啟動 PaaS 資源，以保持在 [您的預算](devtest-lab-configure-cost-management.md)內。
- 在您的實驗室內容中保持不變，因此您不會啟動它們以外的資源。

## <a name="quickly-get-to-ready-to-test"></a>快速做好測試的準備
DevTest Labs 可讓您建立預先布建的環境，並具備您的小組開發和測試應用程式所需的一切。 只要宣告已安裝應用程式最後一個良好組建的 [環境](devtest-lab-add-claimable-vm.md) ，就可以開始運作。 或者，您也可使用容器來更快速地建立更精簡環境。

## <a name="create-once-use-everywhere"></a>一次建立，隨處可用
在您的小組或[組織內，](add-artifact-repository.md)抓住並共用 PaaS[環境範本](devtest-lab-create-environment-from-arm.md)和成品（全都在原始檔控制中），輕鬆地建立開發人員和測試環境。

## <a name="worry-free-self-service"></a>零煩惱的自助方式
DevTest Labs 可讓您的開發人員和測試人員使用一組預先設定的資源，快速且輕鬆地 [建立 IaaS vm](devtest-lab-add-vm.md) 和 [PaaS 資源](devtest-lab-create-environment-from-arm.md) 。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 資源 
開發人員也可以使用 Resource Manager 範本來啟動 PaaS 資源，例如 Azure Service Fabric 叢集、Azure App Service 的 Web Apps 功能，以及 SharePoint 伺服器陣列。 若要在實驗室中開始使用 PaaS，請使用 [公用環境存放庫](devtest-lab-configure-use-public-environments.md) 中的範本，或 [將實驗室連接到您自己的 Git 存放庫](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 您也可以追蹤這些資源的成本，以維持在預算內。

## <a name="integrate-with-your-existing-toolchain"></a>與您現有的工具鏈整合
使用預先製作的外掛程式或 API，直接從您偏好的 [持續整合 (CI) 工具](devtest-lab-integrate-ci-cd.md)、整合式開發環境 (IDE) 或自動發行管線，來布建開發/測試環境。 您也可以使用完整的命令列工具。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- 若要深入瞭解 DevTest Labs，請參閱 [DevTest labs 概念](devtest-lab-concepts.md)。
- 如需逐步指示的逐步解說，請參閱教學課程 [：使用 Azure DevTest Labs 設定實驗室](tutorial-create-custom-lab.md)。