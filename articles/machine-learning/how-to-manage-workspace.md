---
title: 在入口網站中建立工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何建立、查看和刪除 Azure 入口網站中的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: d0b5d3678c3d9c7e55eede13c630510df89d5128
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045562"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理 Azure Machine Learning 工作區


在本文中，您將會在[Azure Machine Learning](overview-what-is-azure-ml.md)的 Azure 入口網站中建立、查看和刪除[**Azure Machine Learning 工作區**](concept-workspace.md)。  入口網站是開始使用工作區的最簡單方式，但隨著您的需求變更或自動化需求的增加，您也可以 [使用 CLI](reference-azure-machine-learning-cli.md)、Python 程式 [代碼](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 或透過 [VS Code 擴充](tutorial-setup-vscode-extension.md)功能來建立和刪除工作區。

## <a name="create-a-workspace"></a>建立工作區

若要建立工作區，您將需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

1. 使用您 Azure 訂閱的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 

1. 在 Azure 入口網站的左上角，選取 [+建立資源]****。

      ![建立新的資源](./media/how-to-manage-workspace/create-workspace.gif)

1. 使用搜尋列尋找 **Machine Learning**。

1. 選取 [Machine Learning]****。

1. 在 [Machine Learning]**** 窗格中選取 [建立]**** 來開始操作。

1. 提供下列資訊來設定新的工作區：

   欄位|描述 
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。 工作區名稱不區分大小寫。
   訂用帳戶 |選取您要使用的 Azure 訂用帳戶。
   資源群組 | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **docs-aml**。 您需要 *參與者* 或 *擁有* 者角色，才能使用現有的資源群組。  如需存取的詳細資訊，請參閱 [管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)。
   區域 | 選取最接近您的使用者與資料資源的 Azure 區域，以建立您的工作區。

1. 當您完成工作區的設定時，請選取 [ **審核 + 建立**]。 （選擇性）使用 [ [網路](#networking) ] 和 [ [Advanced](#advanced) ] 區段來設定工作區的更多設定。

1. 檢查設定，並進行任何額外的變更或修正。 當您對設定感到滿意之後，請選取 [ **建立**]。

   > [!Warning] 
   > 在雲端中建立工作區可能需要數分鐘的時間。

   程序完成後，會出現部署成功訊息。 
 
 1. 若要檢視新的工作區，選取 [前往資源]****。

### <a name="networking"></a>網路  

> [!IMPORTANT]  
> 如需搭配使用私人端點和虛擬網路與工作區的詳細資訊，請參閱 [網路隔離和隱私權](how-to-enable-virtual-network.md)。
    
1. 預設的網路設定是使用可在公用網際網路上存取的 __公用端點__。 若要將您工作區的存取許可權制為您所建立的 Azure 虛擬網路，您可以改為選取 __私人端點__ 作為連線 __方法__，然後使用 [ __+ 新增__ ] 來設定端點。 
    
   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="私人端點選取":::  

1. 在 [ __建立私人端點__ ] 表單上，設定要使用的位置、名稱和虛擬網路。 如果您想要使用端點搭配私人 DNS 區域，請選取 [ __與私人 dns 區域整合__ ]，然後使用 [ __私人 dns 區域__ ] 欄位選取區域。 選取 __[確定]__ 以建立端點。   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="私人端點選取":::   

1. 當您完成網路的設定時，您可以選取 [ __審核] + [建立__]，或前進至選擇性的 [ __Advanced__ configuration]。 

    > [!WARNING]    
    > 當您建立私人端點時，會建立名為 __privatelink.api.azureml.ms__ 的新私人 DNS 區域。 這包含虛擬網路的連結。 如果您在相同的資源群組中建立具有私人端點的多個工作區，則只有第一個私人端點的虛擬網路可以新增至 DNS 區域。 若要新增其他工作區/私人端點所使用之虛擬網路的專案，請使用下列步驟： 
    >   
    > 1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取包含工作區的資源群組。 然後選取名為 __privatelink.api.azureml.ms__的私人 DNS 區域資源。    
    > 2. 在 [ __設定__] 中，選取 [ __虛擬網路連結__]。 
    > 3. 選取 [新增]  。 從 [ __新增虛擬網路連結__ ] 頁面中，提供唯一的 __連結名稱__，然後選取要新增的 __虛擬網路__ 。 選取 __[確定]__ 以新增網路連結。    
    >   
    > 如需詳細資訊，請參閱 [Azure 私人端點 DNS](/azure/private-link/private-endpoint-dns)設定。   

### <a name="vulnerability-scanning"></a>弱點掃描

Azure 資訊安全中心為混合式雲端工作負載提供統一的安全性管理和進階威脅防護。 您應允許 Azure 資訊安全中心掃描您的資源，並遵循其建議。 如需詳細資訊，請參閱資訊安全中心和[Azure Kubernetes Services 與資訊安全中心整合](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)的[Azure Container Registry 映射掃描](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)。

### <a name="advanced"></a>進階    

根據預設，工作區的計量和中繼資料會儲存在 Microsoft 所維護的 Azure Cosmos DB 實例中。 這項資料會使用 Microsoft 管理的金鑰進行加密。  

若要限制 Microsoft 在您的工作區上收集的資料，請選取 [ __高業務影響] 工作區__。 如需此設定的詳細資訊，請參閱待用 [加密](concept-enterprise-security.md#encryption-at-rest)。

> [!IMPORTANT]  
> 只有在建立工作區時，才能選取 [高業務衝擊]。 建立工作區之後，就無法變更此設定。   
如果您使用 __企業__ 版的 Azure Machine Learning，您可以改為提供您自己的金鑰。 這麼做會建立 Azure Cosmos DB 實例，以在您的 Azure 訂用帳戶中儲存計量和中繼資料。 使用下列步驟來使用您自己的金鑰：    

> [!IMPORTANT]  
> 在遵循這些步驟之前，您必須先執行下列動作：   
>   
> 1. 使用訂用帳戶的參與者許可權，在身分識別和存取管理) 中授權 __Machine Learning 應用程式__ (。  
> 1. 遵循 [設定客戶管理的金鑰](/azure/cosmos-db/how-to-setup-cmk) 的步驟：   
>     * 註冊 Azure Cosmos DB 提供者   
>     * 建立並設定 Azure Key Vault 
>     * 產生金鑰  
>   
>     您不需要手動建立 Azure Cosmos DB 實例，系統會在工作區建立期間為您建立一個實例。 此 Azure Cosmos DB 實例會根據此模式使用名稱，在個別的資源群組中建立： `<your-workspace-resource-name>_<GUID>` 。   
>   
> 建立工作區之後，就無法變更此設定。 如果您刪除工作區所使用的 Azure Cosmos DB，也必須刪除正在使用該工作區的工作區。

1. 選取 [ __客戶管理的金鑰__]，然後選取 [ __按一下以選取金鑰__]。   

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="私人端點選取":::   

1. 在 [ __從 Azure Key Vault 選取金鑰__ ] 表單中，選取現有的 Azure Key Vault、其包含的金鑰，以及金鑰的版本。 此金鑰用來加密 Azure Cosmos DB 中儲存的資料。 最後，使用 [ __選取__ ] 按鈕來使用此索引鍵。 

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="私人端點選取":::

### <a name="download-a-configuration-file"></a>下載設定檔

1. 如果您將建立 [計算實例](tutorial-1st-experiment-sdk-setup.md#azure)，請略過此步驟。

1. 如果您打算在參考此工作區的本機環境上使用程式碼， 請從工作區的 [概觀]**** 區段中，選取 [下載 config.xml]****。  

   ![下載 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 指令碼或 Jupyter Notebook 將文件置於目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。 當您建立計算實例時，會為您將此檔案新增至 VM 上的正確目錄。
## <a name="find-a-workspace"></a><a name="view"></a>尋找工作區

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在 [頂端搜尋] 欄位中，輸入 **Machine Learning**。  

1. 選取 [Machine Learning]****。

   ![搜尋 Azure Machine Learning 工作區](./media/how-to-manage-workspace/find-workspaces.png)

1. 查看找到的工作區清單。 您可以根據訂用帳戶、資源群組和位置來篩選。  

1. 選取工作區以顯示其屬性。

## <a name="delete-a-workspace"></a>刪除工作區

在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您要刪除的工作區頂端的 [ **刪除**  ]。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="私人端點選取":::

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新租用戶。 這麼做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>刪除 Azure Container Registry

Azure Machine Learning 工作區會使用 Azure Container Registry (ACR) 進行某些作業。 它會在第一次需要 ACR 執行個體時自動建立一個。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>後續步驟

遵循完整的教學課程，瞭解如何使用工作區來建立、定型和部署具有 Azure Machine Learning 的模型。

> [!div class="nextstepaction"]
> [教學課程：訓練模型](tutorial-train-models-with-aml.md)
