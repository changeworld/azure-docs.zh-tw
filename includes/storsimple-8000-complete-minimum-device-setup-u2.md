---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 05719d9633429c4b8ee6fee79833f594c94ab23d
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376128"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>完成最小的 StorSimple 裝置安裝

   > [!NOTE]
   > 一旦完成最基本的裝置設定，您就無法變更裝置名稱。
   
1. 從 [裝置] 刀鋒視窗的表格式裝置清單中，選取並按一下您的裝置。 裝置處於 [就緒可進行設定] 狀態。 [設定裝置] 刀鋒視窗隨即開啟。

     ![StorSimple 最小裝置設定網路介面](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. 在 [設定裝置] 刀鋒視窗中︰
   
   1. 為裝置提供 [易記名稱]  。 預設的裝置名稱會反映裝置型號及序號等資訊。 您最多可為易記名稱指定 64 個字元來管理您的裝置。
   2. 根據部署裝置的地理位置來設定 [時區]  。 裝置將針對所有排程作業使用這個時區。
   3. 在 **DATA 0 設定** 下：

       1. 您的 DATA 0 網路介面顯示為已啟用，且透過安裝精靈設定網路設定 (IP、子網路、閘道)。 也會針對雲端以及 iSCSI 將 DATA 0 自動啟用。

       2. 提供控制器 0 和控制器 1 的固定 IP 位址。 **控制站的固定 IP 位址必須是裝置 IP 位址可存取的子網路內的可用 IP。** 如果 DATA 0 介面是針對 IPv4 所設定，就必須以 IPv4 格式提供固定的 IP 位址。 如果您提供了適用於 IPv6 設定的首碼，固定的 IP 位址就會自動填入這些欄位中。

            ![StorSimple 最小裝置設定網路介面2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            控制器的固定 IP 位址用來為裝置更新提供服務和記憶體回收。 因此，固定 IP 必須可路由傳送且能夠連線到網際網路。 您可以檢查固定的控制器 IP 是否可路由傳送，方法是使用 [Test-HcsmConnection][Test] Cmdlet。 下列範例會顯示固定控制器 IP 路由傳送至網際網路，並且可以存取 Microsoft Update 伺服器。

            ![Test-HcsmConnection 顯示可路由傳送 IP](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. 按一下 [確定]  。 裝置設定隨即啟動。 完成裝置設定時，您會收到通知。 [裝置] 刀鋒視窗中的裝置狀態會變更為 [上線]。

    ![StorSimple 最小裝置設定網路介面3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx
