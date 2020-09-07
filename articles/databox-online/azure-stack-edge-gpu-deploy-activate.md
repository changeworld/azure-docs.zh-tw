---
title: 在 Azure 入口網站中啟用搭配 GPU 的 Azure Stack Edge 裝置的教學課程 | Microsoft Docs
description: 部署 Azure Stack Edge GPU 的教學課程將說明如何啟動實體裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267933"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>教學課程：啟動 搭配 GPU 的 Azure Stack Edge

本教學課程說明如何使用本機 Web UI 來啟動搭配上線 GPU 的 Azure Stack Edge 裝置。

啟用程序可能需要約 5 分鐘才能完成。

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 必要條件
> * 啟動實體裝置

## <a name="prerequisites"></a>必要條件

在您設定及安裝搭配 GPU 的 Azure Stack Edge 裝置之前，請先確定：

* 對於實體裝置： 
    
    - 您已依照[安裝 Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md) 中的詳細說明安裝實體裝置。
    - 您已依照[設定網路、計算網路 Web Proxy](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) 中的詳細說明，設定網路和計算網路設定
    - 如果您已透過**裝置**頁面變更裝置名稱或 DNS 網域，則您已在裝置上傳自己或產生的裝置憑證。 如果尚未完成此步驟，您會在裝置啟用期間看到錯誤，而且系統會封鎖啟用。 如需詳細資訊，請移至[設定憑證](azure-stack-edge-gpu-deploy-configure-certificates.md)。
    
* 您具有先前建立用以管理 Azure Stack Edge 裝置的 Azure Stack Edge 服務適用的啟用金鑰。 如需詳細資訊，請移至[準備部署 Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)。


## <a name="activate-the-device"></a>啟用裝置

1. 在裝置的本機 Web UI 中，移至**開始使用**頁面。
2. 在**啟用**圖格上，選取 [啟動]。 

    ![本機 Web UI 的「雲端詳細資料」頁面](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. 在**啟動**窗格中，輸入您在[取得 Azure Stack Edge 啟用金鑰](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)中取得的**啟用金鑰**。

4. 選取 [套用]。

    ![本機 Web UI 的「雲端詳細資料」頁面](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. 首先，該裝置會啟動。 系統接著會提示您下載金鑰檔案。
    
    ![本機 Web UI 的「雲端詳細資料」頁面](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    選取 [下載金鑰檔案]，並將 keys.json 檔案儲存在裝置以外的安全位置。 **此金鑰檔案包含您裝置上的 OS 磁碟和資料磁碟的修復金鑰**。 以下是 keys.json 檔案的內容：

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    下表會說明這些金鑰：
    
    |欄位  |描述  |
    |---------|---------|
    |`Id`    | 這是裝置識別碼。        |
    |`DataVolumeBitLockerExternalKeys`|這些是資料磁碟的 BitLockers 金鑰，可用來復原裝置上的本機資料。|
    |`SystemVolumeBitLockerRecoveryKey`| 這是系統磁碟區的 BitLocker 金鑰。 此金鑰可協助復原裝置的系統組態和系統資料。 |
    |`ServiceEncryptionKey`| 此金鑰可保護流經 Azure 服務的資料。 此金鑰可確保 Azure 服務遭盜用不會導致儲存的資訊外洩。 |

6. 移至**概觀**頁面。 裝置狀態應該會顯示為**已啟用**。

    ![本機 Web UI 的「雲端詳細資料」頁面](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
裝置啟用已完成。 您現在可以在裝置上新增共用。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 必要條件
> * 啟動實體裝置

若要了解如何使用 Azure Stack Edge 裝置來傳輸資料，請參閱：

> [!div class="nextstepaction"]
> [使用 Azure Stack Edge 傳輸資料](./azure-stack-edge-j-series-deploy-add-shares.md)
