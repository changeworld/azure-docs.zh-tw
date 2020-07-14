---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: d099e33e7b35381f5404c9f8964d3ea90d4f3908
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959439"
---
最後一個步驟是準備寄送裝置。 在此步驟中，裝置的所有共用都會離線。 在您開始此程序後，即無法存取共用。

> [!IMPORTANT]
> 準備寄送是必要步驟，因為它會將不符合 Azure 命名慣例的資料加上旗標。 此步驟可避免因為未確認資料而造成潛在資料上傳失敗。

1. 移至 [準備寄送]，然後按一下 [開始準備]。 根據預設，系統會在複製資料時計算總和檢查碼。 「準備寄送」會完成總和檢查碼計算，並建立檔案清單 (-BOM 檔案)。 視您的資料大小而定，計算總和檢查碼可能需要數小時到數天的時間。 
   
    ![準備寄送 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    如果您基於任何理由而想要停止裝置準備工作，請按一下 [停止準備]。 您可以稍後繼續準備寄送。
        
    ![準備寄送 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. 準備寄送隨即開啟，而裝置共用會離線。 <!--You see a reminder to download the shipping label once the device is ready.--> 當裝置準備工作完成後，裝置狀態即會更新為 [已準備好寄送] 而且會鎖定裝置。
        
    ![準備寄送 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    如果想要將更多資料複製到裝置，您可以將裝置解除鎖定、複製更多資料，並且再次執行寄送準備。

    如果此步驟中有錯誤，狀態會更新為 [掃描已完成，但發生錯誤]。 解除鎖定裝置，然後移至 [連線並複製] 頁面，下載問題清單並解決錯誤。

    ![準備寄送 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    解決錯誤之後，執行 [準備寄送]。

4. 成功完成寄送準備後 (沒有錯誤)，請遵循下列步驟：

    1. 記下完成參考編號。 視您所在的國家/地區而定，不同作業可能會用到此編號。
    2. 下載在此程序中複製的檔案清單 (也稱為資訊清單)。 您稍後可以使用這份清單來確認上傳至 Azure 的檔案。 如需詳細資訊，請參閱[在準備寄送期間檢查 BOM 檔案](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship)。
        
        ![準備寄送 1](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. 選取並下載裝置的出貨指示。 出貨指示會根據您所在的國家/地區而有所不同。
    4. 如果電子墨水未顯示出貨標籤，您可以在這裡下載反向出貨標籤。 

5. 關閉裝置。 移至 [關機或重新啟動] 頁面，然後按一下 [關閉]。 系統提示您進行確認時，請按一下 [確定] 繼續作業。

6. 拔除纜線。 下一個步驟是將裝置寄回給 Microsoft。
