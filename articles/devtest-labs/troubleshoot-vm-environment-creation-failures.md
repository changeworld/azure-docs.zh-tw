---
title: 疑難排解 VM 和環境失敗 Azure DevTest Labs
description: 瞭解如何在 Azure DevTest Labs 中針對虛擬機器 (VM) 和環境建立失敗進行疑難排解。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476473"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>針對 Azure DevTest Labs 中的虛擬機器 (VM) 和環境建立失敗進行疑難排解
DevTest Labs 會在電腦名稱稱無效或您即將違反實驗室原則時提供警告。 有時，您會在實驗室 VM 旁邊看到紅色，或在發生問題 `X` 時通知您的環境狀態。  本文提供一些訣竅，讓您可以用來找出基礎問題，並希望避免未來的問題。

## <a name="portal-notifications"></a>入口網站通知
如果您是使用 Azure 入口網站，則第一個要查看的地方是 [ **通知] 面板**。  按一下 **鐘圖示**，即可在主要命令列上使用 [通知] 面板，告訴您實驗室 VM 或環境建立是否成功。  如果發生失敗，您會看到與建立失敗相關聯的錯誤訊息。 詳細資料通常會提供進一步的資訊來協助您解決問題。 在下列範例中，虛擬機器建立失敗，因為核心不足。 詳細訊息會告訴您如何修正問題，並要求增加核心配額。

![Azure 入口網站通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM 處於損毀狀態
如果您在實驗室中看到 VM 的狀態**已損毀**，則基礎 VM 可能已從 [**虛擬**機] 頁面中的 [虛擬**機**] 頁面中刪除， (不是從 [DevTest Labs] 頁面) 。 從實驗室中刪除 VM，以清除 DevTest Labs 中的實驗室。 然後，在實驗室中重新建立您的 VM。 

![VM 處於損毀狀態](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>活動記錄
如果您在嘗試建立 VM 或環境之後調查失敗，請查看活動記錄。 本節說明如何尋找 Vm 和環境的記錄。

## <a name="activity-logs-for-virtual-machines"></a>虛擬機器的活動記錄

1. 在實驗室的首頁上，選取要啟動 **虛擬機器** 頁面的 VM。
2. 在 [ **虛擬機器** ] 頁面的左側功能表的 [ **監視** ] 區段中，選取 [ **活動記錄** ] 以查看與 VM 相關聯的所有記錄。
3. 在活動記錄專案中，選取失敗的作業。 通常會呼叫失敗的作業 `Write Virtualmachines` 。
4. 在右窗格中，切換至 [JSON] 索引標籤。您會在記錄的 JSON 視圖中看到詳細資料。

    ![VM 的活動記錄](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 查看 JSON 記錄檔，直到您找到該 `statusMessage` 屬性為止。 它會提供您主要的錯誤訊息和詳細的詳細資訊（如果適用）。 下列 JSON 是本文稍早所述的「核心引號已超過」錯誤範例。

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>環境的活動記錄

若要查看建立環境的活動記錄，請遵循下列步驟：

1. 在實驗室的首頁上，選取左側功能表上的 [設定 **與原則** ]。
2. 在 [設定 **與原則** ] 頁面上，選取功能表上的 [ **活動記錄** ]。
3. 在記錄檔的活動清單中尋找失敗並加以選取。
4. 在右窗格中，切換至 [JSON] 索引標籤，然後尋找 **>statusmessage**。

    ![環境活動記錄](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager 範本部署記錄檔
如果您的環境或虛擬機器是透過自動化所建立，則會有一個最後的位置來查看錯誤資訊。 這就是 Azure Resource Manager 範本部署記錄檔。 透過自動化建立實驗室資源時，通常會透過 Azure Resource Manager 範本部署完成。 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)如需建立 DevTest Labs 資源的範例 Azure Resource Manager 範本，請參閱。

若要查看實驗室範本部署記錄檔，請遵循下列步驟：

1. 啟動實驗室所在資源群組的頁面。
2. 在左側功能表的 [**設定**] 底下，選取 [**部署**]。
3. 尋找失敗狀態的部署，然後選取它。
4. 在 [ **部署** ] 頁面上，針對失敗的作業選取 [作業 **詳細資料** ] 連結。
5. 您會在 [作業 **詳細資料** ] 視窗中看到失敗作業的詳細資料。

## <a name="next-steps"></a>接下來的步驟
請參閱針對構件 [失敗進行疑難排解](devtest-lab-troubleshoot-artifact-failure.md)
