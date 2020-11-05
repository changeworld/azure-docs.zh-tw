---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 1522bb133c7d8f877a334e47bf7929c533ea3465
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376265"
---
#### <a name="to-create-a-cloud-appliance"></a>建立雲端設備

1. 在 Azure 入口網站中，移至 **StorSimple 裝置管理員** 服務。
2. 移至 [裝置] 刀鋒視窗。 從 [服務摘要] 刀鋒視窗中的命令列，按一下 [建立雲端設備]。
    ![StorSimple 建立雲端設備](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. 在 [建立雲端設備] 刀鋒視窗中，指定下列詳細資料。
   
    ![StorSimple 建立雲端設備2](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **名稱** - 雲端設備的唯一名稱。
   2. **型號** - 選擇雲端設備的型號。 8010 裝置提供 30 TB 的標準儲存體，而 8020 提供 64 TB 的進階儲存體。 指定 8010 以從備份部署項目等級擷取案例。 選取 8020 來部署高效能、低延遲的工作負載，或作為災害復原的次要裝置。
   3. **版本** - 選擇雲端設備的版本。 版本對應到用來建立雲端設備之虛擬磁碟映像的版本。 給定版本的雲端設備會判斷您要容錯移轉或從其複製的實體裝置，請務必建立適當版本的雲端設備。
   4. **虛擬網路** - 指定您想要與此雲端設備搭配使用的虛擬網路。 如果使用進階儲存體，您必須選取進階儲存體帳戶支援的虛擬網路。 不支援的虛擬網路在下拉式清單中會顯示為灰色。 如果您選取不支援的虛擬網路，系統會警告您。
   5. **子網路** - 根據選取的虛擬網路，下拉式清單會顯示相關聯的子網路。 將子網路指派給您的雲端設備。
   6. **儲存體帳戶** - 選取儲存體帳戶於佈建期間保留雲端設備的映像。 此儲存體帳戶應該與雲端設備和虛擬網路位於相同的區域中。 實體或雲端設備不應使用它來儲存資料。 根據預設，會就此用途建立新的儲存體帳戶。 不過，如果您知道已經擁有適合此用途的儲存體帳戶，則可從清單中選取該帳戶。 如果建立的是高階雲端設備，下拉式清單中只會顯示進階儲存體帳戶。
      
      > [!NOTE]
      > 雲端設備只能使用 Azure 儲存體帳戶。
    
   7. 選取核取方塊，表示您了解雲端設備上儲存的資料將裝載於 Microsoft 資料中心。
       * 若您只使用實體裝置，加密金鑰就會與裝置放在一起。因此，Microsoft 無法將它解密。

       * 當您使用雲端設備時，加密金鑰和解密金鑰都會儲存於 Microsoft Azure 中。 如需詳細資訊，請參閱[使用雲端設備的安全性考量](../articles/storsimple/storsimple-security.md)。
   8. 按一下 [建立] 即可佈建雲端設備。 裝置可能需要大約 30 分鐘的時間，才能完成佈建。 成功建立雲端設備時，您會收到通知。 請移至 [裝置] 刀鋒視窗，將會重新整理裝置清單來顯示雲端設備。 設備的狀態是 [就緒可進行設定]。
      
      ![StorSimple 雲端設備就緒可進行設定](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)

