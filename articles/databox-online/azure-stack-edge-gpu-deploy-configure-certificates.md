---
title: 在 Azure 入口網站中設定搭配 GPU 的 Azure Stack Edge Pro 裝置憑證的教學課程 | Microsoft Docs
description: 部署搭配 GPU 的 Azure Stack Edge Pro GPU 的教學課程將說明如何在實體裝置上設定憑證。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 7854aff0b4194efae7c4df653dee18e2676fdd41
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446326"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>教學課程：為您搭配 GPU 的 Azure Stack Edge Pro 設定憑證

本教學課程說明如何使用本機 Web UI 來設定搭配上線 GPU 的 Azure Stack Edge Pro 裝置憑證。

此步驟所花費的時間會根據您選擇的特定選項，以及如何在您的環境中建立憑證流程而有所不同。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定實體裝置的憑證

## <a name="prerequisites"></a>必要條件

在您設定及安裝搭配 GPU 的 Azure Stack Edge Pro 裝置之前，請先確定：

* 您已依照[安裝 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中的詳細說明安裝實體裝置。
* 如果您要攜帶自己的憑證：
    - 您應該備妥適當格式的憑證，包括簽署鏈結憑證。 如需憑證的詳細資訊，請移至[管理憑證](azure-stack-edge-j-series-manage-certificates.md)

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>設定裝置憑證

1. 您會在**憑證**頁面上設定憑證。 根據您是否已在**裝置**頁面中變更裝置名稱或 DNS 網域，可以為憑證選擇下列其中一個選項。

    - 如果您在先前的步驟中未變更裝置名稱或 DNS 網域，而且不想要攜帶自己的憑證，則可以略過此步驟，並繼續進行下一個步驟。 裝置已自動產生自我簽署憑證，可以開始使用。 

        ![本機 web UI 的「憑證」頁面](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - 如果您變更了裝置名稱或 DNS 網域，就會看到憑證的狀態顯示為**無效**。 

        ![本機 Web UI 的「憑證」頁面 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        選取憑證以檢視狀態的詳細資料。

        ![本機 Web UI 的「憑證」頁面 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        這是因為憑證不會反映更新的裝置名稱和 DNS 網域 (用於主體名稱和主體替代項目)。 若要成功啟動您的裝置，請選擇下列其中一個選項： 
    
        - **產生所有裝置憑證**。 這些裝置憑證應該只用於測試，而不能用於生產工作負載。 如需詳細資訊，請移至[在您的 Azure Stack Edge Pro 中產生裝置憑證](#generate-device-certificates)。

        - **攜帶您自己的憑證**。 您可以攜帶自己的已簽署端點憑證和對應的簽署鏈結。 您必須先新增簽署鏈結，才能上傳端點憑證。 **建議您一律攜帶自己的憑證以供生產工作負載使用。** 如需詳細資訊，請移至[攜帶您自己的憑證以供 Azure Stack Edge Pro 裝置使用](#bring-your-own-certificates)。
    
        - 您可以攜帶一些自己的憑證，同時產生一些裝置憑證。 **產生憑證**選項只會重新產生裝置憑證。

    - 如果您變更了裝置名稱或 DNS 網域，但未產生憑證或攜帶您自己的憑證，則會**封鎖啟用**。


### <a name="generate-device-certificates"></a>產生裝置憑證

請依照下列步驟產生裝置憑證。

使用這些步驟來重新產生並下載 Azure Stack Edge Pro 裝置憑證：

1. 在裝置的本機 Web UI 中，移至**組態 > 憑證**。 選取 [產生憑證]。

    ![產生並下載憑證 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. 在**產生裝置憑證**中，選取 [產生]。 

    ![產生並下載憑證 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    現在系統會產生並套用裝置憑證。 產生並套用憑證需要幾分鐘。
    
    > [!IMPORTANT]
    > 正在進行憑證產生作業時，請勿攜帶您自己的憑證，並嘗試透過 [+ 新增憑證]選項來新增。

    成功完成作業時，系統將會通知您。 **若要避免任何可能的快取問題，請重新啟動瀏覽器。**
    
    ![產生並下載憑證 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. 產生憑證之後： 

    - 所有憑證的狀態都會顯示為**有效**。 

        ![產生並下載憑證 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - 您可以選取特定的憑證名稱，並檢視憑證詳細資料。 

        ![產生並下載憑證 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - 系統已填入**下載**資料行。 此資料行包含下載重新產生之憑證的連結。 

        ![產生並下載憑證 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. 選取憑證的下載連結，並在出現提示時儲存憑證。 

    ![產生並下載憑證 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. 針對您想要下載的所有憑證重複此程序。 
    
    ![產生並下載憑證 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    裝置產生的憑證會儲存為具有下列名稱格式的 DER 憑證： 

    `<Device name>_<Endpoint name>.cer`. 這些憑證包含安裝在裝置上之對應憑證的公開金鑰。 

您必須在用來存取 ASE 裝置上端點的用戶端系統上安裝這些憑證。 這些憑證會在用戶端與裝置之間建立信任關係。

若要在用來存取裝置的用戶端上匯入及安裝這些憑證，請遵循[在存取您 Azure Stack Edge Pro 裝置的用戶端上匯入憑證](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步驟。 

如果使用 Azure 儲存體總管，您需要以 PEM 格式將憑證安裝在用戶端上，而且必須將裝置產生的憑證轉換成 PEM 格式。 

> [!IMPORTANT]
> - 只有裝置產生的憑證才會提供下載連結，如果您攜帶自己的憑證，則不會提供。
> - 只要符合其他憑證需求，您就可以決定要混合使用裝置產生的憑證以及攜帶您自己的憑證。 如需詳細資訊，請參閱[憑證需求](azure-stack-edge-j-series-certificate-requirements.md)。
    

### <a name="bring-your-own-certificates"></a>攜帶您自己的憑證

請遵循這些步驟來新增您自己的憑證，包括簽署鏈結。

1. 若要上傳憑證，請在**憑證**頁面上，選取 [+ 新增憑證]。

    ![本機 Web UI 的「憑證」頁面 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. 先上傳簽署鏈結，然後選取 [驗證和新增]。

    ![本機 Web UI 的「憑證」頁面 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. 現在您可以上傳其他憑證。 例如，您可以上傳 Azure Resource Manager 和 Blob 儲存體端點憑證。

    ![本機 Web UI 的「憑證」頁面 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    您也可以上傳本機 Web UI 憑證。 上傳此憑證之後，您必須開啟瀏覽器並清除快取。 接著，您必須連線到裝置的本機 Web UI。  

    ![本機 Web UI 的「憑證」頁面 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    您也可以上傳節點憑證。

    ![本機 Web UI 的「憑證」頁面 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    您隨時都可以選取憑證並檢視詳細資料，確保這些憑證與您上傳的憑證相符。

    ![本機 Web UI 的「憑證」頁面 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    憑證頁面應該會更新，以反映新增的憑證。

    ![本機 Web UI 的「憑證」頁面 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > 除了 Azure 公用雲端以外，在啟用所有雲端設定 (Azure Government 或 Azure Stack) 之前，必須先放入簽署鏈結憑證。


您的裝置現在已準備就緒，可供啟動。 選取 [< 返回開始使用]。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定實體裝置的憑證

若要了解如何啟動您的 Azure Stack Edge Pro 裝置，請參閱：

> [!div class="nextstepaction"]
> [啟動 Azure Stack Edge Pro 裝置](./azure-stack-edge-gpu-deploy-activate.md)
