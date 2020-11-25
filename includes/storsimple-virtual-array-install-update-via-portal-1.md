---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005806"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>透過 Azure 入口網站安裝更新

1. 移至您的 StorSimple 裝置管理員，然後選取 [裝置]。 從連接到您服務的裝置清單中，選取並按一下您想要更新的裝置。

    ![在 [最近] 中，裝置管理員 MySSDevManager 已反白顯示並選取，裝置會反白顯示並選取，並且會反白顯示並選取 [裝置 MYSSIS1103]。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. 在 [設定] 刀鋒視窗中，按一下 [裝置更新]。

    ![會顯示 MYSSIS1103 的資訊。 在 [設定] 中，會反白顯示裝置更新。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. 如有軟體更新可用，您會看到一則訊息。 若要檢查更新，您也可以按一下 [掃描]。 記下您在執行的軟體版本。 

    ![[裝置更新] 窗格會顯示「有新的更新可用」和「上次掃描/11/01/2017 10:56 AM/Software version/>10.0.10289.0」。 版本已反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    當掃瞄啟動且成功完成時，系統會通知您。

    ![通知會顯示「正在掃描裝置 MySVAFS110 的更新 ...」。 10:57 AM/作業正在進行中。」](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. 在掃描更新之後，按一下 [下載更新]。

    ![在 [裝置更新] 窗格中會出現「有新的更新可用」的訊息。 [下載更新] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. 在 [新的更新] 刀鋒視窗中，檢閱版本資訊。 另請注意，下載更新後，您必須確認安裝。 按一下 [確定]。

    ![新的 [更新] 窗格會顯示「下載更新後，您必須確認安裝」。 [確定] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. 當上傳啟動且成功完成時，系統會通知您。

     ![通知會顯示「正在下載裝置 MySVAFS 的更新 .。。 11:07 AM」。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. 在 [裝置更新] 刀鋒視窗中，按一下 [安裝]。

     ![裝置更新顯示「確認正在安裝更新」。 [安裝] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. 在 [新的更新] 刀鋒視窗中，系統會提醒您更新將造成運作中斷。 由於虛擬陣列是單一節點裝置，因此裝置會在更新之後重新啟動。 這會中斷任何進行中的 IO。 按一下 [確定] 以安裝更新。

    ![新 [更新] 窗格中的訊息是「您的裝置會在安裝這些更新時停止運作」。 [確定] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. 安裝工作開始時，系統會通知您。

    ![通知會顯示「正在安裝裝置 MySVAFS110 的更新 ...」。 11:09 AM」。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  在安裝工作成功完成之後，按一下 [裝置更新] 刀鋒視窗中的 [檢視工作] 連結來監視安裝。 

    ![在 [裝置更新] 窗格中，有一個標示為「正在安裝更新」的連結。 查看作業」。 [安裝] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    此動作會將您帶往 [安裝更新] 刀鋒視窗。 您可以在此處檢視有關工作的詳細資訊。

    ![[安裝更新] 窗格具有安裝資訊，包括裝置、狀態、持續時間、開始時間和停止時間。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. 如果您開始使用執行軟體版本 Update 0.6 (10.0.10293.0) 的虛擬陣列，您現在正在執行 Update 1，並已完成。 您可以略過剩餘步驟。 如果您開始使用執行軟體版本 Update 0.6 (10.0.10293.0) 的虛擬陣列，您現在會更新為 Update 0.6。 您會看到另一則訊息，指出有可用的更新。 重複步驟 4-8 以安裝 Update 1。

    ![[裝置更新] 窗格會顯示「有新的更新可用」和「上次掃描/11/1/2017 10:56 AM/Software version/10.0.10293.0」。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

