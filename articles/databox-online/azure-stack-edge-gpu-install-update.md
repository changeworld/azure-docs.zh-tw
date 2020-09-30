---
title: 在 Azure Stack Edge Pro GPU 裝置上安裝更新 |Microsoft Docs
description: 說明如何使用適用于 Azure Stack Edge Pro GPU 裝置的 Azure 入口網站和本機 web UI，以及裝置上的 Kubernetes 叢集來套用更新
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: 7a534f794f7ab5323ad46ebc555e42b2514e94e2
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542337"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>更新您的 Azure Stack Edge Pro GPU 

本文說明透過本機 web UI 和透過 Azure 入口網站在 Azure Stack Edge Pro 上安裝更新時所需的步驟。 您可以套用軟體更新或修補程式，以將裝置上的 Azure Stack Edge Pro 裝置和相關聯的 Kubernetes 叢集保持在最新狀態。 

本文中所述的程式是使用不同版本的軟體來執行，但程式對於目前的軟體版本會維持不變。

> [!IMPORTANT]
> - 更新 **2009** 對應于裝置上的 **2.1.1364.2110** 軟體版本。 如需此更新的詳細資訊，請參閱 [版本](azure-stack-edge-gpu-2009-release-notes.md)資訊。
>
> - 請記住，安裝更新或 Hotfix 會重新啟動您的裝置。 此更新需要您依序套用兩個更新。 首先，套用裝置軟體更新，然後 Kubernetes 更新。 假設 Azure Stack Edge Pro 是單一節點裝置，任何進行中的 i/o 都會中斷，而您的裝置會在裝置軟體更新時遇到最多30分鐘的停機時間。

若要在裝置上安裝更新，您必須先設定補救伺服器的位置。 設定補救伺服器之後，您可以透過 Azure 入口網站 UI 或本機 web UI 來套用更新。

下列各節將說明這其中每個步驟。

## <a name="configure-update-server"></a>設定補救伺服器

1. 在本機 web UI 中，**移至設定**  >  **補救伺服器**。 
   
    ![設定更新](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. 在 [ **選取補救伺服器類型**] 中，從下拉式清單中選擇 [Microsoft Update server (預設) 或 Windows Server Update Services。  
   
    如果是從 Windows Server Update Services 更新，請指定伺服器 URI。 該 URI 的伺服器將會在所有連線到此伺服器的裝置上部署更新。

    ![設定更新](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS 伺服器是用來透過管理主控台來管理和發佈更新。 WSUS 伺服器也可以是組織內其他 WSUS 伺服器的更新來源。 做為更新來源的 WSUS 伺服器稱為「上游伺服器」。 在 WSUS 實作中，網路中至少要有一部 WSUS 伺服器必須能夠連線到 Microsoft Update，以取得可用的更新資訊。 身為系統管理員，您可以根據網路安全性和設定，決定有多少其他 WSUS 伺服器可以與 Microsoft Update 直接連線。
    
    如需詳細資訊，請移至 [Windows Server Update Services (WSUS) ](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

建議您透過 Azure 入口網站安裝更新。 裝置會自動掃描一天一次的更新。 一旦有可用的更新，您就會在入口網站中看到通知。 然後您就可以下載並安裝更新。 

> [!NOTE]
> 在您繼續安裝更新之前，請確定裝置狀況良好且狀態顯示為 [ **線上** ]。

1. 當您的裝置有可用的更新時，您會看到通知。 選取通知，或從頂端命令列 **更新裝置**。 這可讓您套用裝置軟體更新。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. 在 [ **裝置更新** ] 分頁中，確認您已在版本資訊中檢查與新功能相關聯的授權條款。

    您可以選擇 **下載並安裝更新，** 或只 **下載** 更新。 然後可以選擇稍後再安裝這些更新。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-2a.png)    

    如果您想要下載並安裝更新，請在下載完成之後，選取 [自動安裝更新] 選項。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-2b.png)

3. 更新的下載隨即開始。 您會看到下載正在進行中的通知。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Azure 入口網站中也會顯示通知橫幅。 這表示下載進度。 

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    您可以選取此通知或選取 [ **更新裝置** ]，以查看更新的詳細狀態。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)   


4. 下載完成後，通知橫幅會更新以指出完成。 如果您選擇下載並安裝更新，則會自動開始安裝。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    如果您選擇只下載更新，請選取通知以開啟 [ **裝置更新** ] 分頁。 選取 [安裝]。
  
    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. 您會看到安裝正在進行中的通知。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)

    入口網站也會顯示資訊警示，指出正在進行安裝。 裝置已離線且處於維護模式。
    
    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. 由於這是1個節點的裝置，因此裝置會在安裝更新之後重新開機。 重新開機期間的重大警示會指出裝置的信號遺失。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    選取警示以查看對應的裝置事件。
    
    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. 重新開機之後，裝置會再次進入維護模式，並顯示資訊警示以指出。

    如果您從頂端命令列選取 **更新裝置** ，您可以看到更新的進度。   

8. 安裝更新之後，裝置狀態會更新為 [ **線上** ]。 

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    從頂端命令列選取 [ **裝置更新**]。 確認更新已成功安裝，且裝置軟體版本反映該更新。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

9. 您會再次看到有可用更新的通知。 這些是 Kubernetes 更新。 選取通知，或從頂端命令列中選取 [ **更新裝置** ]。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. 下載 Kubernetes 更新。 相較于先前的更新套件，您可以看到套件大小不同。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    安裝程式與裝置更新的程式相同。 首先會下載更新。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. 下載更新之後，您就可以安裝更新。 

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    安裝更新時，裝置會進入維護模式。 裝置不會重新開機以進行 Kubernetes 更新。 

    成功安裝 Kubernetes 更新之後，橫幅通知就會消失，因為不需要進一步的更新。 您的裝置現在已有最新版本的裝置軟體和 Kubernetes。

    ![更新之後的軟體版本](./media/azure-stack-edge-gpu-install-update/portal-update-20.png)


## <a name="use-the-local-web-ui"></a>使用本機 Web UI

使用本機 Web UI 時有兩個步驟︰

* 下載更新或 Hotfix
* 安裝更新或 Hotfix

下列各節將詳細討論這其中每個步驟。


### <a name="download-the-update-or-the-hotfix"></a>下載更新或 Hotfix

請執行下列步驟來下載更新。 您可以從 Microsoft 提供的位置或 Microsoft Update 目錄下載更新。


請執行下列步驟，從 Microsoft Update 目錄下載更新。

1. 啟動瀏覽器並流覽至 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) 。

    ![搜尋目錄](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. 在 [Microsoft Update 目錄] 的 [搜尋] 方塊中，輸入知識庫 (KB) 您要下載之更新的修正程式號碼或條款。 例如，輸入 **Azure Stack Edge Pro**]，然後按一下 [ **搜尋**]。
   
    更新清單會顯示為 **Azure Stack Edge Pro 2006**。
   
    ![搜尋目錄](./media/azure-stack-edge-gpu-install-update/download-update-2b.png)

4. 選取 [下載]。 有兩個檔案可供下載 *SoftwareUpdatePackage.exe* 和分別對應到裝置軟體更新和 Kubernetes 更新 *Kubernetes_Package.exe* 尾碼。 將檔案下載到本機系統上的資料夾。 您也可以將資料夾複製到裝置可連線的網路共用位置。

### <a name="install-the-update-or-the-hotfix"></a>安裝更新或 Hotfix

安裝更新或 Hotfix 之前，請確定：

 - 已有更新檔，或 Hotfix 已下載至您主機的本機上，或是可透過網路共用存取。
 - 您的裝置狀態狀況良好，如本機 web UI 的 [ **總覽** ] 頁面所示。

   ![更新裝置](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png) 

此程式大約需要20分鐘才能完成。 請執行下列步驟以安裝更新或 Hotfix。

1. 在本機 web UI 中，移至 [**維護**  >  **軟體更新**]。 記下您在執行的軟體版本。 
   
   ![更新裝置](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. 提供更新檔案的路徑。 如果放置在網路共用上，您也可以流覽至更新安裝檔案。 選取具有 *SoftwareUpdatePackage.exe* 尾碼的軟體更新檔案。

   ![更新裝置](./media/azure-stack-edge-gpu-install-update/local-ui-update-3a.png)

3. 選取 [套用]。 

   ![更新裝置](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. 當系統提示您確認時，請選取 **[是]** 以繼續。 如果裝置是單一節點裝置，在套用更新後，裝置就會重新開機，而且會有停機時間。 
   
   ![更新裝置](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. 更新會開始進行。 成功更新裝置之後，裝置就會重新啟動。 在這段持續時間會無法存取本機 UI。
   
6. 重新啟動完成後，您就會進入 [登入]**** 頁面。 若要確認裝置軟體是否已更新，請在本機 web UI 中，移至 [**維護**  >  **軟體更新**]。 此範例中顯示的軟體版本是 **2.0.1257.1591**。

   ![更新裝置](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png) 

7. 您現在將更新 Kubernetes 軟體版本。 重複上述步驟。 使用 *Kubernetes_Package.exe* 尾碼提供 Kubernetes 更新檔案的路徑。  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)--> 

8. 選取 [套用]。 

   ![更新裝置](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. 當系統提示您確認時，請選取 **[是]** 以繼續。 

10. 成功安裝 Kubernetes 更新之後，在 [**維護**  >  **軟體更新**] 中不會變更顯示的軟體。 


## <a name="next-steps"></a>後續步驟

深入瞭解如何 [管理您的 Azure Stack Edge 專業人員](azure-stack-edge-manage-access-power-connectivity-mode.md)。
