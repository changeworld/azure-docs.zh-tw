---
title: 針對 VM 和環境失敗 Azure DevTest Labs 進行疑難排解
description: 瞭解如何在 Azure DevTest Labs 中針對虛擬機器（VM）和環境建立失敗進行疑難排解。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76166335"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>針對 Azure DevTest Labs 中的虛擬機器（VM）和環境建立失敗進行疑難排解
如果電腦名稱稱無效或您即將違反實驗室原則，DevTest Labs 就會提供警告。 有時候，您會在`X`實驗室 VM 或環境狀態旁看到紅色，通知您發生錯誤。  這篇文章提供幾個訣竅，讓您可以用來找出基礎問題，並希望在未來避免這個問題。

## <a name="portal-notifications"></a>入口網站通知
如果您使用 Azure 入口網站，第一個要查看的地方就是 [**通知] 面板**。  [通知] 面板（可在主要命令列上按一下**鐘圖示**取得）會告訴您實驗室 VM 或環境建立是否成功。  如果發生失敗，您會看到與建立失敗相關聯的錯誤訊息。 詳細資料通常會提供進一步的資訊，協助您解決問題。 在下列範例中，因為核心不足，導致虛擬機器建立失敗。 詳細訊息會告訴您如何修正問題，並要求增加核心配額。

![Azure 入口網站通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM 處於損毀狀態
如果您在實驗室中看到 VM 的狀態**損毀**，則基礎 VM 可能已從 [**虛擬機器**] 頁面（而不是從 [DevTest Labs] 頁面）的 [**虛擬機器**] 頁面中刪除。 從實驗室中刪除 VM，以清除 DevTest Labs 中的實驗室。 然後，在實驗室中重新建立您的 VM。 

![VM 處於損毀狀態](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>活動記錄
如果您在嘗試建立 VM 或環境之後調查失敗，請查看活動記錄。 本節說明如何尋找 Vm 和環境的記錄。

## <a name="activity-logs-for-virtual-machines"></a>虛擬機器的活動記錄

1. 在實驗室的首頁上，選取要啟動**虛擬機器**的 VM 頁面。
2. 在 [**虛擬機器**] 頁面上，選取左側功能表的 [**監視**] 區段中的 [**活動記錄**]，以查看與 VM 相關聯的所有記錄。
3. 在 [活動記錄專案] 中，選取失敗的作業。 通常會呼叫`Write Virtualmachines`失敗的作業。
4. 在右窗格中，切換至 [JSON] 索引標籤。您會在記錄檔的 JSON 視圖中看到詳細資料。

    ![VM 的活動記錄](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 查看 JSON 記錄檔，直到您找到`statusMessage`屬性。 它會提供您主要的錯誤訊息，以及進一步的詳細資訊（如果適用的話）。 下列 JSON 是本文稍早所述的核心引號已超過錯誤的範例。

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>環境的活動記錄

若要查看建立環境的活動記錄，請遵循下列步驟：

1. 在您實驗室的首頁上，選取左側功能表上的 [設定**和原則**]。
2. 在 [設定**和原則**] 頁面上，選取功能表上的 [**活動記錄**]。
3. 在記錄檔的 [活動] 清單中尋找失敗，並加以選取。
4. 在右窗格中，切換至 [JSON] 索引標籤，然後尋找**statusMessage**。

    ![環境活動記錄](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager 範本部署記錄檔
如果您的環境或虛擬機器是透過「自動化」所建立，則最後有一個地方可以查看錯誤資訊。 這就是 Azure Resource Manager 範本部署記錄檔。 透過自動化建立實驗室資源時，通常會透過 Azure Resource Manager 範本部署來完成。 如[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)需建立 DevTest Labs 資源的範例 Azure Resource Manager 範本，請參閱。

若要查看實驗室範本部署記錄，請遵循下列步驟：

1. 針對實驗室所在的資源群組啟動頁面。
2. 選取左側功能表中 [**設定**] 底下的 [**部署**]。
3. 尋找狀態為 [失敗] 的部署，並加以選取。
4. 在 [**部署**] 頁面上，針對失敗的操作選取 [作業**詳細資料**] 連結。
5. 您會在 [作業**詳細資料**] 視窗中看到失敗作業的詳細資料。

## <a name="next-steps"></a>後續步驟
請參閱針對成品[失敗進行疑難排解](devtest-lab-troubleshoot-artifact-failure.md)
