---
title: 部署 Azure 雲端服務 (延伸支援) -Azure 入口網站
description: '使用 Azure 入口網站來部署 Azure 雲端服務 (延伸支援) '
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9c1abe1323bc095fe4dfbfc559ef7e159d1f7532
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880670"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>使用 Azure 入口網站部署 Azure 雲端服務 (延伸支援) 
本文說明如何使用 Azure 入口網站來建立雲端服務 (延伸支援) 部署。 

> [!IMPORTANT]
> 雲端服務 (延伸支援) 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="before-you-begin"></a>開始之前

請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 並建立相關聯的資源。 

## <a name="deploy-a-cloud-services-extended-support"></a> (延伸支援) 部署雲端服務 
1. 登入 [Azure 入口網站](https://portal.azure.com)

2.  使用位於 Azure 入口網站頂端的搜尋列，搜尋並選取 **雲端服務 (延伸支援)**。

    :::image type="content" source="media/deploy-portal-1.png" alt-text="影像會顯示 Azure 入口網站中的 [所有資源] 分頁。":::
 
3.  在 [雲端服務 (延伸支援) ] 窗格中，選取 [ **建立**]。 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="影像顯示從 marketplace 購買雲端服務。":::

4. [雲端服務 (延伸支援) 建立] 視窗將會開啟至 [ **基本** ] 索引標籤。 
    - 選取訂用帳戶。
    - 選擇資源群組或建立新的群組。
    - 輸入您的雲端服務所需的名稱， (延伸支援) 部署。
        - 雲端服務的 DNS 名稱是分開的，而且是由公用 IP 位址的 DNS 名稱標籤所指定，而且可以在 [設定] 索引標籤的 [公用 IP] 區段中加以修改。
    -  選取要部署的區域。

    :::image type="content" source="media/deploy-portal-3.png" alt-text="影像顯示) home blade (延伸支援的雲端服務。":::

5. 新增您的雲端服務設定、封裝和定義檔。 您可以從 blob 儲存體新增現有的檔案，或從您的本機電腦上傳這些檔案。 如果從本機電腦上傳，這些將會儲存在儲存體帳戶中。 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="影像會在建立期間顯示 [基本] 索引標籤的 [上傳] 區段。":::

6. 完成所有欄位之後，請移 **至 [設定** ] 索引標籤並完成。 
    - 請選取要與雲端服務建立關聯的虛擬網路，或建立一個新的虛擬網路。 
        - 雲端服務 (延伸支援) 部署 **必須** 在虛擬網路中。 您也 **必須** 在服務設定中參考虛擬網路 ( .cscfg) 檔案中的 `NetworkConfiguration` 一節。
    - 選取與雲端服務相關聯的現有公用 IP 位址，或建立一個新的。
        - 如果您的服務定義中定義了 **IP 輸入端點** () 檔案，則必須為您的雲端服務建立公用 IP 位址。 
        - 雲端服務 (延伸支援) 只支援基本 IP 位址 SKU。
        - 如果您的服務設定 ( .cscfg) 包含保留的 IP 位址，則必須將 [tp **靜態**] 設定為 [公用 IP] 的配置類型。 
        - （選擇性）藉由更新與雲端服務相關聯的公用 IP 位址的 DNS 標籤屬性，指派雲端服務端點的 DNS 名稱。  
    -  (選用) 啟動雲端服務。 選擇 [開始] 或 [不在建立後立即啟動服務]。
    - 選取 Key Vault 
        - 當您在服務設定中指定一或多個憑證 ( .cscfg) 檔案時，需要 Key Vault。 當您選取金鑰保存庫時，我們會嘗試從您的服務設定中尋找選取的憑證 ( .cscfg) 檔案（以其指紋為基礎）。 如果您的金鑰保存庫中遺失任何憑證，您可以立即上傳它們， **然後按一下 [** 重新整理]。   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="當建立雲端服務 (延伸支援) 時，影像會顯示 Azure 入口網站中的設定分頁。":::

7. 所有欄位都完成後，請移至 [ **審核] 和 [建立** ] 索引標籤來驗證您的部署設定，並 (延伸支援) 建立您的雲端服務。

## <a name="next-steps"></a>後續步驟 
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。
- 造訪[雲端服務 (延伸支援) 範例儲存](https://github.com/Azure-Samples/cloud-services-extended-support)機制