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
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "60561450"
---
# <a name="about-azure-devtest-labs"></a>關於 Azure DevTest Labs
Azure DevTest Labs 可讓小組開發人員有效率地自我管理虛擬機器（Vm）和 PaaS 資源，而不需要等待核准。

DevTest Labs 會建立實驗室，其中包含預先設定的基底或 Azure Resource Manager 範本。 這些都具有所有必要的工具和軟體，可讓您用來建立環境。 您可以在幾分鐘內建立環境，而不是小時或數天。

藉由使用 DevTest Labs，您可以執行下列工作來測試應用程式的最新版本：

- 使用可重複使用的範本和構件，快速布建 Windows 和 Linux 環境。
- 將您的部署管線與研發/測試實驗室輕鬆整合，來佈建隨選環境。
- 布建多個測試代理程式，並建立預先布建的環境以進行訓練和示範，以相應增加您的負載測試。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
DevTest Labs 為使用 Vm 的開發人員提供下列功能：

- 遵循少於五個簡單的步驟，快速建立 Vm。
- 從已設定、核准及由小組負責人或中央 IT 授權的 VM 基礎策劃清單中選擇。
- 從預先建立的自訂映射建立已安裝所有軟體和工具的 Vm。 
- 從基本上是自訂映射的公式建立 Vm，並結合建立 Vm 時所安裝的最新軟體組建。 
- 安裝布建在 Vm 上的延伸模組所部署的構件。
- 在 Vm 上設定自動關機和自動啟動排程。
- 宣告預先建立的 VM，而不需執行建立程式。

DevTest Labs 為使用 PaaS 環境的開發人員提供下列功能：

- 透過少於三個簡單的步驟，使用 Resource Manager 來快速建立 PaaS 環境。
- 從策劃的 Resource Manager 範本清單中進行選擇，這些是由小組負責人或中央 IT 所設定和授權。
- 使用 Resource Manager 範本來啟動空的資源群組（沙箱），以在實驗室的內容中探索 Azure。

DevTest Labs 也可讓中央 IT 人員控制浪費、將資源的成本優化，並藉由執行下列工作，在預算內保持不變：  

- 在 Vm 上設定自動關閉和自動啟動排程。
- 設定使用者可以建立的 Vm 數目的原則。
- 在使用者選擇的 Vm 大小和圖庫映射上設定原則。
- 追蹤成本並設定實驗室的目標。
- 取得實驗室的高預估成本通知，讓您可以採取必要的動作。

DevTest Labs 提供在雲端中建立、設定和管理環境的下列優點。

## <a name="cost-control-and-governance"></a>成本控制與治理
DevTest Labs 可讓您執行下列工作，讓您更輕鬆地控制成本：

- [在您的實驗室上設定原則](devtest-lab-get-started-with-lab-policies.md)，例如每位使用者或每個實驗室的 vm 數目。 
- 建立[原則以自動關閉](devtest-lab-set-lab-policy.md)和啟動 vm。
- 追蹤 Vm 和 PaaS 資源在實驗室中的成本，以維持在[預算](devtest-lab-configure-cost-management.md)內。
- 保持在實驗室的內容中，讓您不會在其外部啟動資源。

## <a name="quickly-get-to-ready-to-test"></a>快速做好測試的準備
DevTest Labs 可讓您建立預先布建的環境，並配備小組開發和測試應用程式所需的所有專案。 只需要宣告應用程式的最後一個良好組建已安裝並開始運作[的環境](devtest-lab-add-claimable-vm.md)。 或使用容器更快速地建立更精簡環境。

## <a name="create-once-use-everywhere"></a>一次建立，隨處可用
在您的小組或組織內捕捉並共用 PaaS[環境範本](devtest-lab-create-environment-from-arm.md)和成品（全都在原始檔控制中[），輕鬆](add-artifact-repository.md)建立開發人員和測試環境。

## <a name="worry-free-self-service"></a>零煩惱的自助方式
DevTest Labs 可讓您的開發人員和測試人員使用一組預先設定的資源，快速且輕鬆地[建立 IaaS vm](devtest-lab-add-vm.md)和[PaaS 資源](devtest-lab-create-environment-from-arm.md)。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 資源 
開發人員也可以使用 Resource Manager 範本來啟動 PaaS 資源，例如 Azure Service Fabric 叢集、Azure App Service 的 Web Apps 功能，以及 SharePoint 伺服器陣列。 若要開始使用 PaaS 的實驗室，請使用[公用環境存放庫](devtest-lab-configure-use-public-environments.md)中的範本，或將[實驗室連線至您自己的 Git 存放庫](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 您也可以追蹤這些資源的成本，以維持在預算內。

## <a name="integrate-with-your-existing-toolchain"></a>與您現有的工具鏈整合
使用預先製作的外掛程式或 API，直接從您偏好的[持續整合（CI）工具](devtest-lab-integrate-ci-cd-vsts.md)、整合式開發環境（IDE）或自動化的發行管線來布建開發/測試環境。 您也可以使用完整的命令列工具。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- 若要深入瞭解 DevTest Labs，請參閱[DevTest labs 概念](devtest-lab-concepts.md)。
- 如需逐步指示的逐步解說，請參閱教學課程[：使用 Azure DevTest Labs 設定實驗室](tutorial-create-custom-lab.md)。