---
title: 在 Azure DevTest Labs 中建立 Service Fabric 叢集環境
description: 瞭解如何使用獨立的 Service Fabric 叢集建立環境，以及如何使用排程來啟動和停止叢集。
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 530cf3b20820e34913612419d0ffa731a70f6a58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85484004"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立具有獨立 Service Fabric 叢集的環境
本文提供的資訊說明如何在 Azure DevTest Labs 中建立具有獨立 Service Fabric 叢集的環境。 

## <a name="overview"></a>概觀
DevTest Labs 可以依照 Azure 資源管理範本的定義，建立獨立的測試環境。 這些環境包含 IaaS 資源，例如虛擬機器和 PaaS 資源（例如 Service Fabric）。 DevTest Labs 可讓您藉由提供命令來控制虛擬機器，以管理環境中的虛擬機器。 這些命令可讓您依排程啟動或停止虛擬機器。 同樣地，DevTest Labs 也可以協助您管理環境中的 Service Fabric 叢集。 您可以手動或透過排程來啟動或停止環境中的 Service Fabric 叢集。

## <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集
Service Fabric 叢集是使用 DevTest Labs 中的環境所建立。 每個環境都是由 Git 存放庫中的 Azure Resource Manager 範本所定義。 適用于 DevTest Labs 的 [公用 Git 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) 包含 Resource Manager 範本，可在 [ServiceFabric](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 叢集資料夾中建立 Service Fabric 叢集。 

1. 首先，使用下列文章中的指示，在 Azure DevTest Labs 中建立實驗室： [建立實驗室](devtest-lab-create-lab.md)。 請注意，[ **公用環境** ] 選項預設為 [ **開啟** ]。 
2. 依照下列步驟，確認已為您的訂用帳戶註冊 Service Fabric 提供者：
    1. 選取左側導覽功能表上的 [**訂閱**]，然後選取您的**訂**用帳戶
    2. 在 [**訂**用帳戶] 頁面上，選取左側功能表的 [**設定**] 區段中的 [**資源提供者**]。 
    3. 如果未註冊 **Microsoft ServiecFabric** ，請選取 [ **註冊**]。 
3. 在實驗室的 [DevTest Lab]**** 頁面上，選取工具列上的 [+ 新增]****。 
    
    ![工具列上的 [新增] 按鈕](./media/create-environment-service-fabric-cluster/add-button.png)
3. 在 [ **選擇基底** ] 頁面上，選取清單中 **Service Fabric Lab** 叢集]。 

    ![選取清單中的 Service Fabric Lab Cluster](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 在 [設定 **設定** ] 頁面上，執行下列步驟： 
    1. 指定叢集**環境**的**名稱**。 這是 Azure 中即將建立 Service Fabric 叢集的資源組名。 
    2. 選取叢集虛擬機器的 **作業系統 (作業系統) ** 。 預設值為： **Windows**。
    3. 指定叢集的 **系統管理員** 名稱。 
    4. 指定系統管理員的 **密碼** 。 
    5. 針對 **憑證**，以 Base64 編碼的字串形式輸入您的憑證資訊。 若要建立憑證，請執行下列步驟：
        1. 從[Git 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)下載**Create-ClusterCertificate.ps1**檔案。 或者，在您的電腦上複製存放庫。 
        2. 啟動 **PowerShell**。 
        3. 使用命令執行 **ps1** 檔案 `.\Create-ClusterCertificate.ps1` 。 您會看到在 [記事本] 中開啟的文字檔，以及您需要填入此頁面上憑證相關欄位的資訊。 . 
    6. 輸入 **憑證的密碼**。
    7. 指定憑證的 **指紋** 。
    8. 在 [設定**設定**] 頁面上選取 [**新增**]。 

        ![設定叢集設定](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 建立叢集之後，您會看到一個資源群組，其中包含您在上一個步驟中提供的環境名稱。 展開時，您會看到其中的 Service Fabric 叢集。 如果資源群組的狀態在 **建立**時停滯 **，請在工具列上選取 [** 重新整理]。 **Service Fabric**叢集環境會在 Linux 或 Windows 上建立5節點的1個 nodetype 叢集。

    在下列範例中， **mysfabricclusterrg** 是專門為 Service Fabric 叢集建立的資源組名。 請務必注意，實驗室環境會獨立于其建立所在的資源群組內。 這表示定義環境的範本，只能存取新建立的資源群組內的資源，或 [設定為實驗室使用的虛擬網路](devtest-lab-configure-vnet.md)中的資源。 上述範例會在相同的資源群組中建立所有必要的資源。

    ![叢集已建立](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>啟動或停止叢集
您可以從 DevTest Lab 頁面本身或 DevTest Labs 提供的 Service Fabric 叢集頁面啟動或停止叢集。 

### <a name="from-the-devtest-lab-page"></a>從 DevTest Lab 頁面
您可以在實驗室的 [DevTest Lab] 頁面上啟動或停止叢集。 

1. 針對 Service Fabric 叢集選取 **三個點 ( ... ) ** ，如下圖所示： 

    ![啟動和停止叢集的命令](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 您會在內容功能表中看到兩個命令來 **啟動** 和 **停止** 叢集。 Start 命令會啟動叢集中的所有節點。 Stop 命令會停止叢集中的所有節點。 停止叢集之後，Service Fabric 叢集本身會維持在就緒狀態，但在實驗室中的叢集上重新發出啟動命令之前，不會有任何可用的節點。

    在測試環境中使用 Service Fabric 叢集時，有幾個要注意的考慮。 在節點重新開機之後，Service Fabric 叢集可能需要一些時間才能完全解除凍結。 若要將資料從關機保留到啟動，必須將資料儲存在連接至虛擬機器的受控磁片上。 使用連結的受控磁片時，會對效能造成影響，因此建議僅用於測試環境。 如果用於資料存放區的磁片未受支援，則在叢集上發出 stop 命令時，資料會遺失。

### <a name="from-the-service-fabric-cluster-page"></a>從 Service Fabric 叢集] 頁面 
有另一種方式可以啟動或停止叢集。 

1. 在 DevTest Lab 頁面的樹狀檢視中，選取您的 Service Fabric 叢集。 

    ![選取您的叢集](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 在叢集的 [ **Service Fabric** 叢集] 頁面上，您會在工具列上看到命令以啟動或停止叢集。 

    ![啟動或停止 Service Fabric 叢集頁面中的命令](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>設定自動啟動和自動關機排程
Service Fabric 叢集也可以依排程啟動或停止。 這項體驗類似于實驗室中的虛擬機器體驗。 為了節省成本，根據預設，在實驗室中建立的每個叢集都會在實驗室 [關機原則](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)所定義的時間自動關閉。 您可以藉由指定叢集是否應關機或指定關閉叢集的時間來覆寫。 

![自動啟動和自動關機的現有排程](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>加入宣告自動啟動排程
若要加入宣告啟動排程，請執行下列步驟：

1. 在左側功能表上選取 [ **自動啟動** ]
2. 選取 [ **開啟** ] 可 **讓此 service fabric 叢集排程為自動啟動**。 只有實驗室擁有者允許使用者自動啟動其虛擬機器或 Service Fabric 叢集時，才會啟用此頁面。
3. 在工具列上選取 [儲存]  。 

    ![自動星星頁面](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>設定自動關機設定 
若要變更關機的設定，請執行下列步驟：

1. 在左側功能表上選取 [ **自動關機** ]。 
2. 在此頁面上，您可以選取 [**關閉**] 來退出自動關機 **。** 
3. 如果您 **已選取 [** **啟用**]，請遵循下列步驟：
    1. 指定關機的 **時間** 。
    2. 指定時間的 **時區** 。 
    3. 指定您是否要讓 DevTest Labs 在自動關機前傳送 **通知** 。 
    4. 如果您針對通知選項選取 **[是]** ，請指定要傳送通知的 **Webhook URL** 和/或 **電子郵件地址** 。 
    5. 在工具列上選取 [儲存]  。

        ![自動關機頁面](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>若要查看 Service Fabric 叢集中的節點
您在先前步驟中看到的 [Service Fabric 叢集] 頁面是 [DevTest Labs] 頁面專用的。 它不會顯示叢集中的節點。 若要查看叢集的詳細資訊，請遵循下列步驟：

1. 在實驗室的 [ **DevTest lab** ] 頁面上，選取 [**我的虛擬機器**] 區段的樹狀檢視中的**資源群組**。

    ![選取資源群組](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 在 [ **資源群組** ] 頁面上，您會在清單中看到資源群組中的所有資源。 從清單中選取您的 **Service Fabric** 叢集。 

    ![在清單中選取您的叢集](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 您會看到叢集的 [ **Service Fabric** 叢集] 頁面。 這是 Service Fabric 提供的頁面。 您會看到有關叢集的所有資訊，例如節點、節點類型等等。

    ![Service Fabric 叢集首頁](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>接下來的步驟
如需環境的詳細資訊，請參閱下列文章： 

- [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
