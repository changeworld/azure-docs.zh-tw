---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185525"
---
如果這是您第一次使用「儲存體總管」，則必須執行下列步驟。

1. 從頂端命令列，移至 **編輯 > 目標 Azure Stack API**。

    ![設定儲存體總管](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. 重新啟動「儲存體總管」以讓變更生效。


請依照下列步驟來連線到儲存體帳戶及驗證連線。

1. 在儲存體總管中選取儲存體帳戶。 以滑鼠右鍵按一下，然後選取 [連線到 Azure 儲存體] 選項。 

    ![設定儲存體總管 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. 在 [連線至 Azure 儲存體] 對話方塊中，選取 [使用儲存體帳戶名稱和金鑰]。

    ![設定儲存體總管 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. 在 **與名稱和金鑰連線** 對話方塊中，採取下列步驟：

    1. 輸入 Edge 儲存體帳戶的顯示名稱。 
    2. 提供 Edge 儲存體帳戶名稱。
    3. 貼上您透過 Azure Resource Manager 從裝置本機 API 取得的存取金鑰。
    4. 選取 [其他 (在下方輸入)] 作為儲存體網域，然後提供 Blob 服務端點尾碼，格式如下：`<appliance name>.<DNSdomain>`。 
    5. 檢查 **使用 HTTP** 選項，因為會透過 http 傳輸。 
    6. 選取 [下一步]  。

    ![設定儲存體總管 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. 在 [連線摘要] 對話方塊中，檢閱所提供的資訊。 選取 [連線]。

    ![設定儲存體總管 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. 您成功新增的帳戶會顯示在「儲存體總管」的左側窗格中，其名稱會附加「(外部，其他)」。 選取 [Blob 容器] 來檢視該容器。

    ![檢視 Blob 容器](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

要確認的下一個步驟是，資料傳輸實際透過此連線正常運作。

請採取下列步驟，將資料載入裝置上的 Edge 儲存體帳戶，系統應該會將資料自動分層至對應的 Azure 儲存體帳戶。

1. 選取您想要在 Edge 儲存體帳戶中載入資料的容器。 依序選取 [上傳] 和 [上傳檔案]。

    ![確認資料傳輸](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. 在 **上傳檔案** 對話方塊中，瀏覽至您想要上傳的檔案並選取。 選取 [下一步]  。

    ![確認資料傳輸 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. 確認檔案已上傳。 上傳的檔案會顯示在容器中。

    ![確認資料傳輸 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. 接下來，您將連線到與此 Edge 儲存體帳戶對應的 Azure 儲存體帳戶。 任何上傳至 Edge 儲存體帳戶的資料，都應該會自動分層到 Azure 儲存體帳戶。 
    
    若要取得 Azure 儲存體帳戶的連接字串，請移至 **Azure 儲存體帳戶 > 存取金鑰** 並複製連接字串。

    ![確認資料傳輸 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    使用連接字串以連接至 Azure 儲存體帳戶。  

    ![確認資料傳輸 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. 在 [連線摘要] 對話方塊中，檢閱所提供的資訊。 選取 [連線]。

    ![確認資料傳輸 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. 您會看到在 Edge 儲存體帳戶中上傳的檔案已傳輸至 Azure 儲存體帳戶。

    ![確認資料傳輸 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
