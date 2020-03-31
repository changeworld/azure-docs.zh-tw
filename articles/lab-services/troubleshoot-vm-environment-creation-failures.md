---
title: 解決 VM 和環境故障 Azure 開發人員測試實驗室
description: 瞭解如何在 Azure 開發人員測試實驗室中排除虛擬機器 （VM） 和環境創建故障。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166335"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>在 Azure 開發人員測試實驗室中排除虛擬機器 （VM） 和環境創建失敗
如果電腦名稱稱無效或即將違反實驗室策略，DevTest Labs 會為您提供警告。 有時，在實驗室`X`VM 或環境狀態旁邊看到紅色，這些紅色會通知您出現問題。  本文提供了一些技巧，您可以使用這些技巧來查找根本問題，並希望將來避免此問題。

## <a name="portal-notifications"></a>門戶通知
如果使用 Azure 門戶，則首先查看的是**通知面板**。  通過按一下**鈴形圖示**，主命令列上可用的通知面板將告訴您實驗室 VM 或環境創建是否成功。  如果失敗，您將看到與創建失敗關聯的錯誤訊息。 詳細資訊通常會提供進一步的資訊，以説明您解決問題。 在下面的示例中，由於內核不足，虛擬機器創建失敗。 詳細消息告訴您如何解決此問題並請求增加核心配額。

![Azure 門戶通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM 處於損壞狀態
如果您在實驗室中看到 VM 的狀態為 **"已損壞**"，則基礎 VM 可能已從使用者可以從**虛擬機器**頁面導航到的**虛擬機器**頁面中刪除（而不是從 DevTest Labs 頁面）。 通過從實驗室中刪除 VM，清理開發人員實驗室中的實驗室。 然後，在實驗室中重新創建 VM。 

![處於損壞狀態的 VM](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>活動記錄
如果在嘗試創建 VM 或環境後某個時候調查故障，請查看活動日誌。 本節介紹如何查找 VM 和環境的日誌。

## <a name="activity-logs-for-virtual-machines"></a>虛擬機器的活動日誌

1. 在實驗室的主頁上，選擇 VM 以啟動**虛擬機器**頁面。
2. 在 **"虛擬機器**"頁上，在左側功能表的 **"監視"** 部分中，選擇 **"活動日誌"** 以查看與 VM 關聯的所有日誌。
3. 在活動日誌項中，選擇失敗的操作。 通常，失敗操作稱為`Write Virtualmachines`。
4. 在右側窗格中，切換到 JSON 選項卡。您可以在日誌的 JSON 視圖中看到詳細資訊。

    ![VM 的活動日誌](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 查看 JSON 日誌，直到找到該`statusMessage`屬性。 它為您提供主錯誤訊息和進一步的詳細資訊（如果適用）。 以下 JSON 是本文前面看到的核心引用超過錯誤的示例。

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>環境的活動日誌

要查看創建環境的活動日誌，請按照以下步驟操作：

1. 在實驗室的主頁上，選擇左側功能表上的 **"配置"和"策略**"。
2. 在 **"配置和策略**"頁上，選擇功能表上**的活動日誌**。
3. 在日誌中的活動清單中查找失敗並選擇它。
4. 在右側窗格中，切換到 JSON 選項卡，然後查找**狀態Message**。

    ![環境活動日誌](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>資源管理器範本部署日誌
如果環境或虛擬機器是通過自動化創建的，則有最後一個位置可以查看錯誤資訊。 這是 Azure 資源管理器範本部署日誌。 通過自動化創建實驗室資源時，通常通過 Azure 資源管理器範本部署完成。 有關[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)創建 DevTest Labs 資源的示例 Azure 資源管理器範本，請參閱。

要查看實驗室範本部署日誌，請按照以下步驟操作：

1. 啟動存在實驗室的資源組的頁面。
2. 在 **"設置"** 下選擇左側功能表上**的部署**。
3. 查找狀態失敗的部署並選擇它。
4. 在 **"部署"** 頁上，為失敗的操作選擇 **"操作詳細資訊**"連結。
5. 在 **"操作詳細資訊"** 視窗中可以看到有關失敗操作的詳細資訊。

## <a name="next-steps"></a>後續步驟
請參閱[排除工件故障](devtest-lab-troubleshoot-artifact-failure.md)
