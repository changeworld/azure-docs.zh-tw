---
title: 在 Azure 開發人員測試實驗室中創建服務結構群集環境
description: 瞭解如何使用自包含的服務結構群集創建環境，並使用計畫啟動和停止群集。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170346"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>在 Azure 開發人員測試實驗室中使用自包含的服務結構群集創建環境
本文提供有關如何在 Azure DevTest 實驗室中使用自包含的服務結構群集創建環境的資訊。 

## <a name="overview"></a>總覽
DevTest 實驗室可以創建由 Azure 資源管理範本定義的自包含測試環境。 這些環境包含 IaaS 資源（如虛擬機器）和 PaaS 資源（如 Service Fabric）。 DevTest Labs 允許您通過提供控制虛擬機器的命令來管理環境中的虛擬機器。 這些命令使您能夠按計劃啟動或停止虛擬機器。 同樣，DevTest Labs 還可以説明您在環境中管理服務結構群集。 您可以手動或通過計畫在環境中啟動或停止 Service Fabric 群集。

## <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集
使用 DevTest 實驗室中的環境創建服務交換矩陣群集。 每個環境都由 Git 存儲庫中的 Azure 資源管理器範本定義。 開發人員測試實驗室[的公共 Git 存儲庫](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)包含資源管理器範本，用於在[ServiceFabric 群集](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)資料夾中創建服務結構群集。 

1. 首先，使用以下文章中的說明在 Azure 開發人員測試實驗室中[創建實驗室：創建實驗室](devtest-lab-create-lab.md)。 請注意，預設情況下 **，"公共環境**"選項處於**打開狀態**。 
2. 按照以下步驟確認服務交換矩陣提供程式已註冊您的訂閱：
    1. 選擇左側導航功能表上的**訂閱**，然後選擇 **"訂閱"**
    2. 在 **"訂閱"** 頁上，在左側功能表的 **"設置"** 部分中選擇 **"資來源提供者**"。 
    3. 如果**Microsoft.ServiecFabric**未註冊，請選擇"**註冊**"。 
3. 在實驗室的 [DevTest Lab]**** 頁面上，選取工具列上的 [+ 新增]****。 
    
    ![在工具列上添加按鈕](./media/create-environment-service-fabric-cluster/add-button.png)
3. 在 **"選擇基本**"頁上，在清單中選擇 **"服務結構實驗室群集**"。 

    ![在清單中選擇服務結構實驗室群集](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 在 **"配置設置"** 頁上，執行以下步驟： 
    1. 為群集**環境**指定**名稱**。 這是要在 Azure 中創建服務結構群集的資源組的名稱。 
    2. 選擇群集虛擬機器的**作業系統 （OS）。** 預設值為： **Windows**。
    3. 為群集的**管理員**指定名稱。 
    4. 為管理員指定**密碼**。 
    5. 對於**證書**，將證書資訊輸入為 Base64 編碼字串。 要創建證書，請執行以下步驟：
        1. 從[Git 存儲庫](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)下載**創建群集證書.ps1**檔。 或者，在電腦上克隆存儲庫。 
        2. 啟動 **PowerShell**。 
        3. 使用 命令`.\Create-ClusterCertificate.ps1`運行**ps1**檔。 您會看到一個在記事本中打開的文字檔，其中包含填寫此頁面上與證書相關的欄位所需的資訊。 . 
    6. 輸入**證書的密碼**。
    7. 指定證書的**指紋**。
    8. 在 **"配置設置"** 頁上選擇 **"添加**"。 

        ![設定叢集設定](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 創建群集後，您將看到一個資源組，其名稱為上一步驟中提供的環境。 展開時，可以看到其中的服務結構群集。 如果資源組的狀態停留在 **"創建"** 中，則選擇工具列上的 **"刷新**"。 **Service Fabric 群集**環境在 Linux 或 Windows 上創建 5 節點 1 節點類型群集。

    在下面的示例中 **，mysfabricClusterrg**是專門為服務結構群集創建的資源組的名稱。 請務必注意，實驗室環境在創建它們的資源組中是獨立的。 這意味著定義環境的範本，該範本只能訪問新創建的資源組或[配置為由實驗室使用的虛擬網路](devtest-lab-configure-vnet.md)中的資源。 上面的此示例將在同一資源組中創建所有必需的資源。

    ![群集已創建](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>啟動或停止群集
可以從 DevTest 實驗室頁面本身或 DevTest Labs 提供的服務交換矩陣群集頁面啟動或停止群集。 

### <a name="from-the-devtest-lab-page"></a>從開發人員測試實驗室頁面
您可以在實驗室的 DevTest 實驗室頁面上啟動或停止群集。 

1. 為服務結構群集選擇**三個點 （...），** 如下圖所示： 

    ![啟動和停止群集的命令](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 在內容功能表中可以看到兩個命令來**啟動**和**停止**群集。 Start 命令啟動群集中的所有節點。 停止命令停止群集中的所有節點。 群集停止後，Service Fabric 群集本身將保持就緒狀態，但在實驗室中的群集上重新發出 Start 命令之前，沒有節點可用。

    在測試環境中使用 Service Fabric 群集時，需要注意一些注意事項。 重新開機節點後，服務結構群集可能需要一些時間才能完全重新補充。 要將資料從關機保留到啟動，資料必須保存在連接到虛擬機器的託管磁片上。 使用附加的託管磁片時會產生性能影響，因此建議僅針對測試環境。 如果未備份用於資料存儲的磁片，則在群集上發出停止命令時，資料將丟失。

### <a name="from-the-service-fabric-cluster-page"></a>從"服務結構群集"頁 
還有另一種方法來啟動或停止群集。 

1. 在 DevTest 實驗室頁面上的樹狀檢視中選擇您的服務結構群集。 

    ![選擇群集](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 在群集的服務**結構群集**頁上，可以看到工具列上用於啟動或停止群集的命令。 

    ![在"服務結構群集"頁中啟動或停止命令](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>配置自動啟動和自動關閉計畫
服務結構群集也可以按計劃啟動或停止。 此體驗類似于實驗室中虛擬機器的體驗。 為了節省資金，預設情況下，在實驗室中創建的每個群集都會在實驗室[關閉策略](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)定義的時間自動關閉。 可以通過指定是否應關閉群集或指定群集關閉的時間來覆蓋。 

![自動啟動和自動關機的現有計劃](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>加入宣告自動啟動計畫
要加入宣告啟動計畫，執行以下步驟：

1. 選擇左側功能表上**的自動啟動**
2. 選擇 **"打開**"**以允許此服務結構群集計畫自動啟動**。 僅當實驗室擁有者允許使用者自動啟動其虛擬機器或 Service Fabric 群集時，才會啟用此頁面。
3. 在工具列上選取 [儲存]****。 

    ![自動星形頁面](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>配置自動關機設置 
要更改關機設置，請執行以下步驟：

1. 選擇左側功能表上**的自動關閉**。 
2. 在此頁上，您可以通過選擇 **"已啟用的關閉"** 來退出宣告自動關閉**Enabled**。 
3. 如果選擇了 **"啟用****"，** 請按照以下步驟操作：
    1. 指定關機**時間**。
    2. 指定時間的**時區**。 
    3. 指定是否希望 DevTest Labs 在自動關閉之前發送**通知**。 
    4. 如果為通知選項選擇了 **"是**"，請指定**Webhook URL**和/或**電子郵件地址**以發送通知。 
    5. 在工具列上選取 [儲存]****。

        ![自動關閉頁面](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>查看服務結構群集中的節點
在前面的步驟中看到的服務結構群集頁面特定于 DevTest Labs 頁面。 它不顯示群集中的節點。 要查看有關群集的詳細資訊，請按照以下步驟操作：

1. 在實驗室的**DevTest 實驗室**頁面上，在"**我的虛擬機器"** 部分的樹狀檢視中選擇**資源組**。

    ![選取資源群組](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 在"**資源組"** 頁上，您可以看到清單中資源組中的所有資源。 從清單中選擇**您的服務結構群集**。 

    ![在清單中選擇群集](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 您將看到群集的服務**結構群集**頁面。 這是服務結構提供的頁面。 您將看到有關群集的所有資訊，如節點、節點類型等。

    ![服務結構群集主頁](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>後續步驟
有關環境的詳細資訊，請參閱以下文章： 

- [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
