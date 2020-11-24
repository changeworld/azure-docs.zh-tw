---
title: 包含檔案
description: 包含檔案
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95559206"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>透過 Azure 入口網站安裝更新

1. 移至您的 StorSimple 裝置管理員，然後選取 [裝置]。 從連接到您服務的裝置清單中，選取並按一下您想要更新的裝置。

2. 在 [設定] 下，按一下 [裝置更新]。  

3. 如有軟體更新可用，您會看到一則訊息。 若要檢查更新，您也可以按一下 [掃描]。 記下您在執行的軟體版本。 

    ![[裝置更新] 窗格會顯示 [有新的更新可供使用] (醒目提示) 和 [上次掃描/6/22/2018 2:46 PM/軟體版本/10.0.10296.0]。 版本已反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    當掃瞄啟動且成功完成時，系統會通知您。
 
4. 在掃描更新之後，按一下 [下載更新]。 在 [新的更新] 下，檢閱版本資訊。 另請注意，下載更新後，您必須確認安裝。 按一下 [確定]。

    ![在 [裝置更新] 中，會反白顯示 [下載更新] 按鈕。 在新的更新中，有一個版本資訊的連結，以及下載更新後確認安裝的訊息。 有一個 [確定] 按鈕。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    當上傳啟動且成功完成時，系統會通知您。

5. 在 [裝置更新] 下，按一下 [安裝]。

     ![裝置更新顯示「確認正在安裝更新」。 [安裝] 按鈕和軟體版本會反白顯示。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. 在 [新的更新] 下，系統會提醒您更新將造成運作中斷。 由於虛擬陣列是單一節點裝置，因此裝置會在更新之後重新啟動。 這會中斷任何進行中的 IO。 按一下 [確定] 以安裝更新。

    ![新的更新會顯示「您的裝置會在安裝這些更新時停止運作」。 其中有一個版本資訊的連結，還有一個 [確定] 按鈕。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    安裝工作開始時，系統會通知您。

7.  在安裝工作成功完成之後，按一下 [檢視工作] 連結。 此動作會將您帶往 [安裝更新] 刀鋒視窗。 您可以在此處檢視有關工作的詳細資訊。 

    ![裝置更新有一個標示為「正在安裝更新」的連結。 查看作業」。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. 如果您從執行軟體版本 Update 1 (10.0.10296.0) 的虛擬陣列開始，您現在正在執行 Update 1.1，並已完成。 您可以略過剩餘步驟。 

    如果您從執行 Update 0.6 (10.0.10293.0) 軟體版本的虛擬陣列開始，您現在已更新為 Update 1.0。 您會看到另一則訊息，指出有可用的更新。 重複步驟 4-7 以安裝 Update 1.1。

    

