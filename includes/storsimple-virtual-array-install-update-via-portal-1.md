---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b4c3fcb86fb098263840accc561785a40b767952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174167"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>透過 Azure 入口網站安裝更新

1. 移至您的 StorSimple 裝置管理員，然後選取 [裝置]****。 從連接到您服務的裝置清單中，選取並按一下您想要更新的裝置。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. 在 [設定]**** 刀鋒視窗中，按一下 [裝置更新]****。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. 如有軟體更新可用，您會看到一則訊息。 若要檢查更新，您也可以按一下 [掃描]****。 記下您在執行的軟體版本。 

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    當掃瞄啟動且成功完成時，系統會通知您。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. 在掃描更新之後，按一下 [下載更新]****。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. 在 [新的更新]**** 刀鋒視窗中，檢閱版本資訊。 另請注意，下載更新後，您必須確認安裝。 按一下 [確定]****。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. 當上傳啟動且成功完成時，系統會通知您。

     ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. 在 [裝置更新]**** 刀鋒視窗中，按一下 [安裝]****。

     ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. 在 [新的更新]**** 刀鋒視窗中，系統會提醒您更新將造成運作中斷。 由於虛擬陣列是單一節點裝置，因此裝置會在更新之後重新啟動。 這會中斷任何進行中的 IO。 按一下 [確定]**** 以安裝更新。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. 安裝工作開始時，系統會通知您。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  在安裝工作成功完成之後，按一下 [裝置更新]**** 刀鋒視窗中的 [檢視工作]**** 連結來監視安裝。 

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    此動作會將您帶往 [安裝更新]**** 刀鋒視窗。 您可以在此處檢視有關工作的詳細資訊。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. 如果您開始使用執行軟體版本 Update 0.6 (10.0.10293.0) 的虛擬陣列，您現在正在執行 Update 1，並已完成。 您可以略過剩餘步驟。 如果您開始使用執行軟體版本 Update 0.6 (10.0.10293.0) 的虛擬陣列，您現在會更新為 Update 0.6。 您會看到另一則訊息，指出有可用的更新。 重複步驟 4-8 以安裝 Update 1。

    ![更新裝置](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

