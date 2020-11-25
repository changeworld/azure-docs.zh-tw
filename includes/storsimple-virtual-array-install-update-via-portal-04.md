---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026793"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>透過 Azure 入口網站安裝更新

1. 移至您的 StorSimple 裝置管理員，然後選取 [裝置]。 從連接到您服務的裝置清單中，選取並按一下您想要更新的裝置。 

    ![在 [最近] 中，裝置管理員 MySSDevManager 已反白顯示並選取，裝置會反白顯示並選取，並且會反白顯示並選取 [裝置 MYSSIS1103]。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. 在 [設定] 刀鋒視窗中，按一下 [裝置更新]。 

    ![會顯示 MYSSIS1103 的資訊。 在 [設定] 中，會反白顯示裝置更新。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. 如有軟體更新可用，您會看到一則訊息。 若要檢查更新，您也可以按一下 [掃描]。

    ![[裝置更新] 窗格會顯示「有新的更新可用」和「上次掃描/18/01/2017 2:42 PM/軟體版本/>10.0.10288.0」。 版本已反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    當掃瞄啟動且成功完成時，系統將會通知您。

    ![通知會顯示「正在掃描裝置 MySSIS1103 的更新。 2:12 PM/成功完成作業。」](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. 在掃描更新之後，按一下 [下載更新]。 

    ![在 [裝置更新] 窗格中會出現「有新的更新可用」的訊息。 [下載更新] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. 在 [新的更新] 刀鋒視窗中，於下載完成之後檢閱資訊，您必須確認安裝。 按一下 [確定]。

    ![新的 [更新] 窗格會顯示「下載更新後，您必須確認安裝」。 [確定] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. 當上傳啟動且成功完成時，系統會通知您。

     ![通知會顯示「正在下載裝置 MySSIS1 的更新 .。。 2:13 PM」。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. 在 [裝置更新] 刀鋒視窗中，按一下 [安裝]。

     ![裝置更新顯示「確認正在安裝更新」。 有一個 [安裝] 按鈕。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. 在 [新的更新] 刀鋒視窗中，系統會提醒您更新將造成運作中斷。 由於虛擬陣列是單一節點裝置，因此裝置會在更新之後重新啟動。 這會中斷任何進行中的 IO。 按一下 [確定] 以安裝更新。 

    ![新 [更新] 窗格中的訊息是「您的裝置會在安裝這些更新時停止運作」。 [確定] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. 安裝工作開始時，系統會通知您。 

    ![通知會顯示「正在安裝裝置 MySSIS1103 的更新。 2:15 PM」。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  在安裝工作成功完成之後，按一下 [裝置更新] 刀鋒視窗中的 [檢視工作] 連結來監視安裝。 

    ![在 [裝置更新] 窗格中，有一個標示為「正在安裝更新」的連結。 查看作業」。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    這會將您帶往 [安裝更新] 刀鋒視窗。 您可以在此處檢視有關工作的詳細資訊。

    ![[安裝更新] 窗格具有安裝資訊，包括裝置、狀態、持續時間、開始時間和停止時間。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. 成功安裝更新之後，您會在 [ **裝置更新** ] 分頁中看到此效果的訊息。 
