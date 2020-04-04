---
title: 在 StorSimple 8000 系列設備上安裝更新 5.1 |微軟文件
description: 說明如何在 StorSimple 8000 系列設備上安裝 StorSimple 8000 系列更新 5.1。
services: storsimple
documentationcenter: NA
author: priestlg
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: v-grpr
ms.openlocfilehash: b90f0f35f908d16b0746075e638fe66769939a64
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658036"
---
# <a name="install-update-51-on-your-storsimple-device"></a>在 StorSimple 裝置上安裝更新 5.1

## <a name="overview"></a>概觀

本教程介紹如何在通過 Azure 門戶運行早期軟體版本的 StorSimple 設備上安裝 Update 5.1。 <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

更新 5.1 包括無中斷的安全更新。 可透過 Azure 門戶應用無中斷更新或定期更新 <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * 更新 5.1 是必填更新,應立即安裝。 有關詳細資訊,請參閱更新[5.1 發行說明](storsimple-update51-release-notes.md)。
> * 安裝前會執行一組手動和自動預先檢查，以根據硬體狀態和網路連線來判斷裝置健全狀況。 僅當從 Azure 門戶應用更新時,才會執行這些預檢查。
> * 如果要使用修補程式方法安裝,請與[Microsoft 支援](mailto:support@microsoft.com)。

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>透過 Azure 門戶安裝更新 5.1

執行以下步驟將裝置更新為[更新 5.1](storsimple-update51-release-notes.md)。

> [!NOTE]
> Microsoft 會從裝置提取其他診斷資訊。 因此，當我們的作業小組識別有問題的裝置時，我們更有能力從裝置收集資訊並診斷問題。

#### <a name="to-install-an-update-from-the-azure-portal"></a>從 Azure 入口網站安裝更新

1. 在 [StorSimple 服務] 頁面上，選取您的裝置。

    ![選取裝置](./media/storsimple-8000-install-update-51/update1.png)

2. 瀏覽到**裝置設定** > **裝置更新**。

    ![按一下 [裝置更新]](./media/storsimple-8000-install-update-51/update2.png)

3. 有新的更新可用時，會顯示通知。 或者，在 [裝置更新]**** 刀鋒視窗中，按一下 [掃描更新]****。 系統會建立用來掃描可用更新的作業。 當作業成功完成時，系統會通知您。

    ![按一下 [裝置更新]](./media/storsimple-8000-install-update-51/update3.png)

4. 我們建議您先檢閱版本資訊，然後再於裝置上套用更新。 若要套用更新，請按一下 [安裝更新]****。 在 [確認定期更新]**** 刀鋒視窗中，檢閱要完成的必要條件後，才能套用更新。 選取核取方塊，指出您已準備好更新裝置，然後按一下 [安裝]****。

    ![按一下 [裝置更新]](./media/storsimple-8000-install-update-51/update4.png)

5. 一組必要條件檢查會隨即開始。 這些檢查包括︰
   
   * **控制器健康狀況檢查** ：確認這兩個裝置控制器的狀況良好且在線上。
   * **硬體元件健康狀況檢查** ：確認您的 StorSimple 裝置上的所有硬體元件的狀況良好。
   * **DATA 0 檢查** ：確認您的裝置已啟用 DATA 0。 如果未啟用此介面，您必須啟用它，然後重試。

     只在所有檢查都成功都完成時，才會下載並安裝更新。 當檢查進行時，您會收到通知。 如果前置檢查失敗，系統將會提供您失敗的原因。 處理這些問題，然後重試作業。 如果您不能自行解決這些問題，您可能需要連絡 Microsoft 支援服務。

7. 前置檢查成功完成後，將會建立更新作業。 成功建立更新作業時，系統將會通知您。
   
    ![建立更新工作](./media/storsimple-8000-install-update-51/update6.png)
   
    接著，更新會套用到您的裝置上。

9. 更新需要幾個小時才能完成。 隨時可選取更新工作並按一下 [詳細資料] **** 來檢視工作的詳細資料。

    ![建立更新工作](./media/storsimple-8000-install-update-51/update8.png)

     您也可以從 [裝置設定] > [作業]**** 來監視更新作業的進度。 在 [作業]**** 刀鋒視窗中，您可以看到更新進度。

     ![建立更新工作](./media/storsimple-8000-install-update-51/update7.png)

10. 作業完成後，瀏覽至 [裝置設定] > [裝置更新]****。 現在應該已更新軟體版本。


驗證您的設備是否正在運行**StorSimple 8000 系列更新 5.1 (6.3.9600.17885)。** [上次更新日期]**** 應該已修改。
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>後續步驟

瞭解有關[更新 5.1 版本](storsimple-update51-release-notes.md)的詳細資訊。
