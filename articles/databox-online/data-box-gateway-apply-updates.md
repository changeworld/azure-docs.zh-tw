---
title: 在 Azure 資料箱閘道系列裝置上安裝更新 |Microsoft Docs
description: 說明如何使用適用于 Azure 資料箱閘道系列裝置的 Azure 入口網站和本機 web UI 來套用更新
services: databox
author: priestlg
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: v-grpr
ms.openlocfilehash: 4c17488a875484b2d3dc0e7e8e1045ce8ea75cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85802025"
---
# <a name="update-your-azure-data-box-gateway"></a>更新您的 Azure 資料箱閘道

本文說明透過本機 web UI 和 Azure 入口網站在 Azure 資料箱閘道上安裝更新所需的步驟。 您可以套用軟體更新或修補程式，讓您的資料箱閘道裝置保持在最新狀態。

> [!IMPORTANT]
>
> - 更新**1911**對應至您裝置上的**1.6.1049.786**軟體版本。 如需此更新的詳細資訊，請移至[版本](data-box-gateway-1911-release-notes.md)資訊。
>
> - 請記住，安裝更新或 Hotfix 會重新啟動您的裝置。 假設資料箱閘道是單一節點裝置，任何進行中的 i/o 都會中斷，而您的裝置會遇到最多30分鐘的裝置軟體更新停機。

下列各節將說明這其中每個步驟。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

我們建議您透過 Azure 入口網站安裝更新。 裝置會每天自動掃描更新一次。 一旦有可用的更新，您就會在入口網站中看到通知。 接著，您可以下載並安裝更新。

> [!NOTE]
> 請確定裝置狀況良好且狀態顯示為 [**線上**]，然後再繼續安裝更新。

1. 當您的裝置有可用的更新時，您會看到通知。 選取通知，或從頂端命令列 [**更新裝置**]。 這可讓您套用裝置軟體更新。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. 在 [**裝置更新**] 分頁中，確認您已檢查與版本資訊中的新功能相關聯的授權條款。

    您可以選擇**下載並安裝更新，** 或直接**下載**更新。 然後您可以選擇稍後再安裝這些更新。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    如果您想要下載並安裝更新，請核取 [下載完成後自動安裝] 的選項。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. 更新會開始下載。 您會看到下載正在進行中的通知。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    通知橫幅也會顯示在 Azure 入口網站中。 這表示下載進度。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    您可以選取此通知或選取 [**更新裝置**]，以查看更新的詳細狀態。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. 下載完成後，通知橫幅會更新以指出完成。 如果您選擇下載並安裝更新，安裝將會自動開始。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    如果您選擇只下載更新，則請選取通知以開啟 [**裝置更新**] 分頁。 選取 [安裝]。
  
    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. 您會看到安裝正在進行中的通知。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    入口網站也會顯示資訊警示，指出安裝正在進行中。 <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. 由於這是1個節點的裝置，因此裝置會在安裝更新之後重新開機。 重新開機期間的重大警示會指出裝置的心跳已遺失。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    選取警示以查看對應的裝置事件。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. 安裝更新之後，裝置狀態會更新為 [**線上**]。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    從頂端的命令列中，選取 [**裝置更新**]。 確認更新已安裝成功，且裝置軟體版本反映了。

    ![更新之後的軟體版本](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>使用本機 Web UI

使用本機 Web UI 時有兩個步驟︰

- 下載更新或 Hotfix
- 安裝更新或 Hotfix

下列各節將詳細討論這其中每個步驟。

### <a name="download-the-update-or-the-hotfix"></a>下載更新或 Hotfix

請執行下列步驟來下載更新。 您可以從 Microsoft 提供的位置或 Microsoft Update 目錄下載更新。

請執行下列步驟，從 Microsoft Update 目錄下載更新。

1. 啟動瀏覽器，並流覽至 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) 。

   ![搜尋目錄](./media/data-box-gateway-apply-updates/download-update-1.png)

2. 在 [Microsoft Update 目錄] 的 [搜尋] 方塊中，輸入您要下載之更新的 [知識庫（KB）] 或 [詞彙] 數目。 例如，輸入**Azure 資料箱閘道**]，然後按一下 [**搜尋**]。

   更新清單會顯示為**Azure 資料箱閘道 1911**。

   ![搜尋目錄](./media/data-box-gateway-apply-updates/download-update-2.png)

3. 選取 [下載]。 有一個可供下載的單一檔案，稱為*SoftwareUpdatePackage.exe* ，其對應至裝置軟體更新。 將檔下載到本機系統上的資料夾。 您也可以將資料夾複製到裝置可連線的網路共用位置。

   ![搜尋目錄](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>安裝更新或 Hotfix

安裝更新或 Hotfix 之前，請確定：

- 已有更新檔，或 Hotfix 已下載至您主機的本機上，或是可透過網路共用存取。
- 如本機 web UI 的 [**總覽**] 頁面所示，您的裝置狀態會是 [狀況良好]。

   ![更新裝置](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

此程式大約需要20分鐘的時間才能完成。 請執行下列步驟以安裝更新或 Hotfix。

1. 在本機 web UI 中，移至 [**維護**] [  >  **軟體更新**]。 記下您在執行的軟體版本。

   ![更新裝置](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. 提供更新檔案的路徑。 如果放在網路共用上，您也可以流覽至更新安裝檔案。 選取具有*SoftwareUpdatePackage.exe*尾碼的軟體更新檔案。

   ![更新裝置](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. 選取 [套用]。

   ![更新裝置](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. 當系統提示您確認時，請選取 **[是]** 繼續進行。 假設裝置是單一節點裝置，在套用更新後，裝置就會重新開機，而且會有停機時間。
   ![更新裝置](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. 更新會開始進行。 成功更新裝置之後，裝置就會重新啟動。 在這段持續時間會無法存取本機 UI。

6. 重新啟動完成後，您就會進入 [登入]**** 頁面。 若要確認裝置軟體是否已更新，請在本機 web UI 中，移至 [**維護**] [  >  **軟體更新**]。 本範例中顯示的軟體版本為**1.6.1049.786**。

   ![更新裝置](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>後續步驟

深入瞭解如何[管理您的 Azure 資料箱閘道](data-box-gateway-manage-users.md)。
