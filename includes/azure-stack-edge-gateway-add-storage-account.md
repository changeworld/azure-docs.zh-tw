---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 5f39f727deaf3a53db5e2928e5af23779c298318
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97911557"
---
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure Stack Edge 資源，然後移至 [概觀]  。 您的裝置應在線上。 移至 [雲端儲存空間閘道] > [儲存體帳戶]。

2. 選取裝置命令列上的 [+ 新增儲存體帳戶]。 

   ![新增儲存體帳戶](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. 在 [新增 Edge 儲存體帳戶] 窗格中，指定下列設定：

    a. 裝置上 Edge 儲存體帳戶的唯一名稱。 儲存體帳戶名稱只能包含小寫數字和字母。 不允許使用特殊字元。 儲存體帳戶名稱在裝置內必須是唯一的 (跨裝置則不需要)。

    b. 儲存體帳戶所持有資料相關資訊的選擇性描述。  
    
    c. 根據預設，Edge 儲存體帳戶會對應到雲端中的 Azure 儲存體帳戶，而儲存體帳戶中的資料會自動推送至雲端。 指定您的 Edge 儲存體帳戶所對應的 Azure 儲存體帳戶。  

    d. 接下來建立新的容器，或是從 Azure 儲存體帳戶中的現有容器進行選取。 寫入 Edge 儲存體帳戶的裝置中任何資料，都會自動上傳至對應 Azure 儲存體帳戶中的所選取儲存體容器。

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. 指定所有儲存體帳戶選項之後，請選取 [新增] 以建立 Edge 儲存體帳戶。 成功建立 Edge 儲存體帳戶之後會通知您。 新的 Edge 儲存體帳戶會顯示在 Azure 入口網站的儲存體帳戶清單中。 

    
4. 如果您選取這個新的儲存體帳戶，並移至 [存取金鑰]，您可以找到 Blob 服務端點和對應的儲存體帳戶名稱。 將這些值連同存取金鑰這樣的資訊複製起來，可協助您連線到 Edge 儲存體帳戶。

    ![新增儲存體帳戶 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    您可以藉由[使用 Azure Resource Manager 連線到裝置本機 API](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)，來取得存取金鑰。 
