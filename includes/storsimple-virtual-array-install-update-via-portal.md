---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95562696"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>透過 Azure 入口網站安裝更新

1. 移至您的 StorSimple 裝置管理員，然後選取 [裝置]。 從連接到您服務的裝置清單中，選取並按一下您想要更新的裝置。 

    ![在 [最近] 中，裝置管理員 MySSDevManager 已反白顯示並選取，裝置會反白顯示並選取，並且會反白顯示並選取 [裝置 MYSSIS1103]。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. 在 [設定] 刀鋒視窗中，按一下 [裝置更新]。 

    ![MYSSIS1103 的資訊隨即出現。 在 [設定] 中，會反白顯示裝置更新。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. 如有軟體更新可用，您會看到一則訊息。 若要檢查更新，您也可以按一下 [掃描]。

    ![在 [裝置更新] 窗格中，[掃描] 按鈕會反白顯示。 有四行訊息：上次掃描/未掃描/軟體版本/10.0.10280.0。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    當掃瞄啟動且成功完成時，系統將會通知您。

    ![通知會顯示「正在掃描裝置 MySSIS 的更新1103.2： 12 PM/已成功完成作業」。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. 在掃描更新之後，按一下 [下載更新]。 

    ![[裝置更新] 窗格會顯示「有新的更新可用」和「上次掃描/11/3/2016 2:12 PM/軟體版本/10.0.10280.0」。 版本已反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. 在 [新的更新] 刀鋒視窗中，於下載完成之後檢閱資訊，您必須確認安裝。 按一下 [確定]。

    ![新的 [更新] 窗格會顯示「下載更新後，您必須確認安裝」。 [確定] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. 當上傳啟動且成功完成時，系統會通知您。

     ![通知會顯示「正在下載裝置 MySSIS 的更新 .。。 2:13 PM」。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. 在 [裝置更新] 刀鋒視窗中，按一下 [安裝]。

     ![[裝置更新] 窗格會顯示「確認安裝更新」訊息。 [安裝] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. 在 [新的更新] 刀鋒視窗中，系統會提醒您更新將造成運作中斷。 由於虛擬陣列是單一節點裝置，因此裝置會在更新之後重新啟動。 這會中斷任何進行中的 IO。 按一下 [確定] 以安裝更新。 

    ![新 [更新] 窗格中的訊息是「您的裝置會在安裝這些更新時停止運作」。 [確定] 按鈕會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. 安裝工作開始時，系統會通知您。 

    ![通知會顯示「正在安裝裝置 MYSSIS1103 的更新」。 2:15 PM。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  在安裝工作成功完成之後，按一下 [裝置更新] 刀鋒視窗中的 [檢視工作] 連結來監視安裝。 

    ![在 [裝置更新] 窗格中，標示為「正在安裝更新」的連結。 已反白顯示 [View Job]。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    這會將您帶往 [安裝更新] 刀鋒視窗。 您可以在此處檢視有關工作的詳細資訊。

    ![[安裝更新] 窗格具有安裝資訊，包括裝置、狀態、持續時間、開始時間和停止時間。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. 成功安裝更新之後，您會在 [裝置更新] 刀鋒視窗中看見關於此效果的訊息。 軟體版本也會變更為 **10.0.10288.0**。 

    ![[裝置更新] 窗格會顯示「您的裝置處於最新狀態」，並提供 (>10.0.10288.0) 的軟體版本。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
