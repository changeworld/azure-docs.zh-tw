---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "66419956"
---
最後一個步驟是準備寄送裝置。 在此步驟中，裝置的所有共用都會離線。 在您開始此程序後，即無法存取共用。

> [!IMPORTANT]
> 準備寄送是必要步驟，因為它會將不符合 Azure 命名慣例的資料加上旗標。 略過此步驟，可能會導致未確認資料所造成的潛在資料上傳失敗。

1. 移至 [準備寄送]  ，然後按一下 [開始準備]  。 根據預設，系統會在複製資料時計算總和檢查碼。 「準備寄送」會完成總和檢查碼計算，並建立檔案清單 (也稱為 *BOM 檔案*或資訊清單)。 視您的資料大小而定，計算總和檢查碼可能需要數小時到數天的時間。
   
    ![準備寄送 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    如果您基於任何理由而想要停止裝置準備工作，請按一下 [停止準備]  。 您可以稍後繼續準備寄送。
        
    ![準備寄送 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. 準備寄送隨即開啟，而裝置共用會離線。 一旦裝置準備就緒，您就會看到提醒下載出貨標籤。

    ![下載出貨標籤提醒](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. 當裝置準備工作完成後，裝置狀態即會更新為 [已準備好寄送]  而且會鎖定裝置。
        
    ![準備寄送 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    如果想要將更多資料複製到裝置，您可以將裝置解除鎖定、複製更多資料，並且再次執行寄送準備。

    如果這個步驟中有錯誤，請下載錯誤記錄並解決錯誤。 解決錯誤之後，執行 [準備寄送]  。

4. 順利完成寄送準備 (沒有錯誤) 之後，請下載在此程序中複製的檔案清單 (也稱為 *BOM 檔案*或資訊清單)。 

    ![下載檔案清單或 BOM 檔案](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   您稍後可以使用這份清單來確認上傳至 Azure 的檔案。 如需詳細資訊，請參閱[在準備寄送期間檢查 BOM 檔案](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship)。
        
    ![範例 BOM 檔案](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. 關閉裝置。 移至 [關機或重新啟動]  頁面，然後按一下 [關閉]  。 系統提示您進行確認時，請按一下 [確定]  繼續作業。

    ![關閉第一個裝置節點](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. 對其他裝置節點重複上述所有步驟。
7. 在裝置完全關閉後，裝置背面的所有 LED 燈都會熄滅。 下一個步驟是卸下所有纜線並將裝置寄回給 Microsoft。
