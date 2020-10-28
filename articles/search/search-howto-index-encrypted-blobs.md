---
title: 搜尋經過加密的 Azure Blob 儲存體內容
titleSuffix: Azure Cognitive Search
description: 瞭解如何使用 Azure 認知搜尋在 Azure Blob 儲存體中編制加密檔的文字索引和解壓縮。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 6a4dcec2b50a13a256c82e4a5ec54c9b22aa973f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791982"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中使用 blob 索引子和技能集為加密的 blob 編制索引

本文說明如何使用[Azure 認知搜尋](search-what-is-azure-search.md)來編制先前使用[Azure Key Vault](../key-vault/general/overview.md)在[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)內加密檔的索引。 一般而言，索引子無法從加密的檔案中解壓縮內容，因為它無法存取加密金鑰。 不過，藉由運用 [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) 自訂技能，然後使用 [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)，您可以提供金鑰的受控制存取來解密檔案，然後再將內容解壓縮。 如此一來，就能將這些檔編制索引，而不需要擔心資料是以未加密的狀態儲存。

本指南使用 Postman 和搜尋 REST Api 來執行下列工作：

> [!div class="checklist"]
> * 從整份檔開始 (非結構化文字) 例如，已使用 Azure Key Vault 加密的 Azure Blob 儲存體中的 PDF、HTML、.DOCX 和 .PPTX。
> * 定義可解密檔並從中解壓縮文字的管線。
> * 定義用來儲存輸出的索引。
> * 執行用來建立和載入索引的管線。
> * 使用全文檢索搜尋和豐富的查詢語法來探索結果。

如果您沒有 Azure 訂用帳戶，請在開始前開啟[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

此範例假設您已將檔案上傳至 Azure Blob 儲存體，並已在程式中將它們加密。 如果您需要有關讓檔案一開始上傳及加密的協助，請參閱 [本教學](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) 課程以瞭解如何執行此操作。

+ [Azure 儲存體](https://azure.microsoft.com/services/storage/)
+ [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)
+ [Azure 函式](https://azure.microsoft.com/services/functions/)
+ [Postman 桌面應用程式](https://www.getpostman.com/)
+ [建立](search-create-service-portal.md)或[尋找現有的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用本指南的免費服務。 免費搜尋服務會限制您有三個索引、三個索引子、三個數據源和三個技能集。 本指南會建立每一個。 開始之前，請確定您的服務有空間可接受新的資源。

## <a name="1---create-services-and-collect-credentials"></a>1-建立服務並收集認證

### <a name="set-up-the-custom-skill"></a>設定自訂技能

此範例會使用來自[Azure 搜尋服務 Power 技能](https://github.com/Azure-Samples/azure-search-power-skills)GitHub 存放庫的範例[DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile)專案。 在本節中，您會將技能部署至 Azure 函式，讓它可以在技能集中使用。 內建的部署腳本會建立名為 "psdbf" 的 Azure 函數資源（以函式 **應用程式** 為開頭），並載入技能。 系統會提示您提供訂用帳戶和資源群組。 請務必選擇您的 Azure Key Vault 實例所在的相同訂用帳戶。

在操作上，DecryptBlobFile 技能會將每個 blob 的 URL 和 SAS 權杖作為輸入，並使用 Azure 認知搜尋預期的檔案參考合約來輸出下載的解密檔案。 回想一下，DecryptBlobFile 需要加密金鑰才能執行解密。 在安裝過程中，您也會建立存取原則，以授與 DecryptBlobFile 函數存取 Azure Key Vault 中的加密金鑰。

1. 按一下 [ [DecryptBlobFile] 登陸頁面](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment)上的 [ **部署至 Azure** ] 按鈕，這會在 Azure 入口網站中開啟提供的 Resource Manager 範本。

1. 選取 **Azure Key Vault 實例所在的訂** 用帳戶 (如果您選取不同的訂用帳戶) ，並選取現有的資源群組，或建立新的資源群組 (如果您建立新的資源群組，您也必須選取要部署到) 的區域。

1. 選取 [ **審核 + 建立** ]，確定您同意條款，然後選取 [ **建立** ] 以部署 Azure 函數。

    ![入口網站中的 ARM 範本](media/indexing-encrypted-blob-files/arm-template.jpg "入口網站中的 ARM 範本")

1. 等待部署完成。

1. 在入口網站中流覽至您的 Azure Key Vault 實例。 在 Azure Key Vault 中[建立存取原則](../key-vault/general/assign-access-policy-portal.md)，以授與自訂技能的金鑰存取權。
 
    1. 在 [ **設定** ] 底下，選取 [ **存取** 原則]，然後選取 [ **新增存取原則** ]。
     
       ![Keyvault 新增存取原則](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Keyvault 存取原則")

    1. 在 [ **從範本設定** ] 下，選取 **Azure Data Lake Storage 或 Azure 儲存體** 。

    1. 針對主體，選取您所部署的 Azure 函式實例。 您可以使用在步驟2中用來建立它的資源首碼來搜尋它，它的預設前置詞值為 **psdbf-function 應用程式** 。

    1. 請勿選取 [授權的應用程式] 選項的任何選項。
     
        ![Keyvault 新增存取原則範本](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Keyvault 存取原則範本")

    1. 請務必先按一下 [存取原則] 頁面上的 [ **儲存** ]，然後再流覽至實際新增存取原則。
     
         ![Keyvault 儲存存取原則](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "儲存 Keyvault 存取原則")

1. 在入口網站中流覽至 **psdbf 函式應用程式** 函式，並記下下列內容，因為您稍後會在本指南中使用這些屬性：

    1. 函式 URL，可以在函式主頁面的 [ **基本** 資訊] 下找到。
    
        ![函數 URL](media/indexing-encrypted-blob-files/function-uri.jpg "哪裡可以找到 Azure 函數 URL")

    1. 主機金鑰程式碼，可透過流覽至 **應用程式金鑰** 、按一下以顯示 **預設** 金鑰，然後複製值來找到。
     
        ![函數主機金鑰碼](media/indexing-encrypted-blob-files/function-host-key.jpg "哪裡可以找到 Azure 函數主機金鑰程式碼")

### <a name="cognitive-services"></a>認知服務

AI 擴充和技能集執行是由認知服務所支援，包括自然語言和影像處理的文字分析和電腦視覺。 如果您的目標是要完成實際的原型或專案，您應在此時佈建認知服務 (位於 Azure 認知搜尋所在的區域)，以便將其連結至索引作業。

不過，在此練習中，您可以略過資源佈建，因為 Azure 認知搜尋可以在幕後連線到認知服務，並為每個索引子執行提供 20 筆免費交易。 處理20份檔之後，除非將認知服務金鑰附加至技能集，否則索引子會失敗。 針對較大型的專案，請考慮以隨用隨付 S0 層來佈建認知服務。 如需詳細資訊，請參閱[連結認知服務](cognitive-search-attach-cognitive-services.md)。 請注意，即使您選取的認知 (技能未在) 中新增任何技能，也必須要有認知服務金鑰才能執行具有超過20份檔的技能集。

### <a name="azure-cognitive-search"></a>Azue 認知搜尋

最後一個元件是 Azure 認知搜尋，您可以 [在入口網站中建立](search-create-service-portal.md)。 您可以使用免費層來完成本指南。 

如同 Azure Function，請花點時間收集系統管理金鑰。 此外，當您開始結構化要求時，您必須提供用來驗證每個要求的端點和管理員 API 金鑰。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>取得 Azure 認知搜尋的管理員 API 金鑰和 URL

1.  頁面中取得您的搜尋服務名稱。 您可藉由檢閱端點 URL 來確認您的服務名稱。 如果您的端點 URL 為 `https://mydemo.search.windows.net`，您的服務名稱會是 `mydemo`。

2. 在 [設定]  >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   ![取得服務名稱及管理和查詢金鑰](media/search-get-started-javascript/service-name-and-keys.png)

在傳送至您服務的每個要求的標頭中都需要有 api-key。 有效的金鑰能為每個要求在傳送要求之應用程式與處理要求的服務間建立信任。

## <a name="2---set-up-postman"></a>2 - 設定 Postman

安裝並設定 Postman。

### <a name="download-and-install-postman"></a>下載並安裝 Postman

1. 下載 [Postman 集合原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)。
1. 選取 [檔案] > [匯入]，將原始程式碼匯入 Postman。
1. 選取 [集合] 索引標籤，然後選取 **...** (省略符號) 按鈕。
1. 選取 [編輯]。 
   
   ![顯示導覽的 Postman 應用程式](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "移至 Postman 中的 [編輯] 功能表")
1. 在 [編輯] 對話方塊中，選取 [變數] 索引標籤。 

在 [變數] 索引標籤上，您可以在每次遇到雙括弧內的特定變數時，新增 Postman 交換的值。 例如，Postman 會將符號 `{{admin-key}}` 取代為您針對 `admin-key` 所設定的目前值。 Postman 會在 URL、標頭、要求本文等項目中進行這項替代。 

若要取得的值 `admin-key` ，請使用您稍早記下的 Azure 認知搜尋管理 api 金鑰。 設定 `search-service-name` 為您所使用 Azure 認知搜尋服務的名稱。 `storage-connection-string`使用儲存體帳戶的 [ **存取金鑰** ] 索引標籤上的值進行設定，並將設定 `storage-container-name` 為儲存加密檔案之儲存體帳戶上的 blob 容器名稱。 設定 `function-uri` 為您先前記下的 azure 函式 URL，並設定 `function-code` 為您之前記下的 azure 函式主機金鑰程式碼。 您可以將其他值保留為預設值。

![Postman 應用程式變數索引標籤](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Postman 的變數視窗")


| 變數    | 從哪裡取得 |
|-------------|-----------------|
| `admin-key` | 在 Azure 認知搜尋服務的 [金鑰] 頁面上。  |
| `search-service-name` | Azure 認知搜尋服務的名稱。 URL 為 `https://{{search-service-name}}.search.windows.net`。 | 
| `storage-connection-string` | 在儲存體帳戶的 [存取金鑰] 索引標籤上，選取 [key1] > [連接字串]。 | 
| `storage-container-name` | 具有要編制索引之加密檔案的 blob 容器名稱。 | 
| `function-uri` |  在主要頁面上的 [ **基本** 功能] 下的 Azure 函式中。 | 
| `function-code` | 在 Azure 函式中，流覽至 [ **應用程式金鑰** ]，按一下以顯示 **預設** 金鑰，然後複製值。 | 
| `api-version` | 保持為 **2020-06-30** 。 |
| `datasource-name` | 保持為 **加密的 blob-ds** 。 | 
| `index-name` | 保持為 **加密的 blob-idx** 。 | 
| `skillset-name` | 保持為 **加密的 blob-ss** 。 | 
| `indexer-name` | 保留為 **加密-blob->hotels-reviews-ixr** 。 | 

### <a name="review-the-request-collection-in-postman"></a>檢閱 Postman 中的要求集合

當您執行本指南時，您必須發出四個 HTTP 要求： 

- **建立索引的 PUT 要求** ：此索引會保存 Azure 認知搜尋所使用並傳回的資料。
- **建立資料來源的 POST 要求** ：此資料來源會將您的 Azure 認知搜尋服務連接到您的儲存體帳戶，進而加密 blob 檔案。 
- **建立技能集的 PUT 要求** ：技能集會為 Azure 函式指定會解密 blob 檔案資料的自訂技能定義，以及在解密後將每份檔的文字解壓縮的 [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) 。
- **建立索引子的 PUT 要求** ：執行索引子會讀取資料、套用技能集，並儲存結果。 您必須最後再執行此要求。

[原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)包含具有四個要求的 Postman 集合，以及一些實用的後續追蹤要求。 若要發出要求，請在 Postman 中選取要求的索引標籤，然後針對每個要求選取 [ **傳送** ]。

## <a name="3---monitor-indexing"></a>3-監視索引編制

當您提交建立索引子的要求時，編制索引和擴充就會開始進行。 根據您的儲存體帳戶中有多少份檔，編制索引可能需要一些時間。 若要瞭解索引子是否仍在執行，請使用 Postman 集合中提供的 **Get 索引子狀態** 要求並查看回應，以瞭解索引子是否正在執行，或查看錯誤和警告資訊。  

如果您使用免費層，則預期會出現下列訊息： `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` 。 因為免費層上的 blob 索引在字元解壓縮中有 [32k 限制](search-limits-quotas-capacity.md#indexer-limits)，所以會出現此訊息。 若此資料集在較高的層級上，您就不會看到此訊息。 

## <a name="4---search"></a>4-搜尋

完成索引子執行之後，您可以執行一些查詢，以確認資料已成功解密並編制索引。 在入口網站中流覽至您的 Azure 認知搜尋服務，然後使用 [ [搜尋] explorer](search-explorer.md) 對索引資料執行查詢。

## <a name="next-steps"></a>後續步驟

現在您已成功編制加密檔案的索引，您可以藉 [由新增更多認知技能來逐一查看此管線](cognitive-search-defining-skillset.md)。 這可讓您擴充和深入瞭解您的資料。

如果您使用的是雙向加密資料，您可能會想要調查 Azure 認知搜尋中可用的索引加密功能。 雖然索引子需要解密的資料以供編制索引之用，但在索引存在之後，就可以使用客戶管理的金鑰來加密。 這可確保您的資料一律會在待用時加密。 如需詳細資訊，請參閱 [在 Azure 認知搜尋中設定客戶管理的金鑰以進行資料加密](search-security-manage-encryption-keys.md)。