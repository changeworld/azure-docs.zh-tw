---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d2df7388018c463ba58b57be46945915210b84b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174177"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>從 Azure 入口網站安裝更新

1. 在 [StorSimple 服務] 頁面上，選取您的裝置。

    ![選取裝置](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. 流覽至 [**裝置設定**] [  >  **裝置更新**]。

    ![按一下 [裝置更新]](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. 有新的更新可用時，會顯示通知。 或者，在 [裝置更新]**** 刀鋒視窗中，按一下 [掃描更新]****。 系統會建立用來掃描可用更新的作業。 當作業成功完成時，系統會通知您。

    ![按一下 [裝置更新]](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. 我們建議您先檢閱版本資訊，然後再於裝置上套用更新。 若要套用更新，請按一下 [安裝更新]****。 在 [確認定期更新]**** 刀鋒視窗中，檢閱要完成的必要條件後，才能套用更新。 選取核取方塊，指出您已準備好更新裝置，然後按一下 [安裝]****。

    ![按一下 [裝置更新]](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. 一組必要條件檢查會隨即開始。 這些檢查包括︰
   
   * **控制器健康狀況檢查** ：確認這兩個裝置控制器的狀況良好且在線上。
   * **硬體元件健康狀況檢查** ：確認您的 StorSimple 裝置上的所有硬體元件的狀況良好。
   * **DATA 0 檢查** ：確認您的裝置已啟用 DATA 0。 如果未啟用此介面，您必須啟用它，然後重試。

     只在所有檢查都成功都完成時，才會下載並安裝更新。 當檢查進行時，您會收到通知。 如果前置檢查失敗，系統將會提供您失敗的原因。 處理這些問題，然後重試作業。 如果您不能自行解決這些問題，您可能需要連絡 Microsoft 支援服務。

7. 前置檢查成功完成後，將會建立更新作業。 成功建立更新作業時，系統將會通知您。
   
    ![建立更新工作](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    接著，更新會套用到您的裝置上。

9. 更新需要幾個小時才能完成。 隨時可選取更新工作並按一下 [詳細資料] **** 來檢視工作的詳細資料。

    ![建立更新工作](./media/storsimple-8000-install-update5-via-portal/update8.png)

     您也可以從 [裝置設定] > [作業]**** 來監視更新作業的進度。 在 [作業]**** 刀鋒視窗中，您可以看到更新進度。

     ![建立更新工作](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. 作業完成後，瀏覽至 [裝置設定] > [裝置更新]****。 現在應該已更新軟體版本。

