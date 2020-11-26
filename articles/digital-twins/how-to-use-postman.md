---
title: 使用 Postman 提出要求
titleSuffix: Azure Digital Twins
description: 瞭解如何設定和使用 Postman 來測試 Azure 數位 Twins Api。
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: f9be9dd94aad8c206b562f2c984ec944f70d3957
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187931"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>如何使用 Postman 將要求傳送至 Azure 數位 Twins Api

[Postman](https://www.getpostman.com/) 是 REST 測試控管，可在桌面和外掛程式型 GUI 中提供重要的 HTTP 要求功能。 您可以使用它來製作 HTTP 要求，並將其提交至 [Azure 數位 TWINS REST api](how-to-use-apis-sdks.md)。

本文說明如何透過下列步驟，設定 [POSTMAN REST 用戶端](https://www.getpostman.com/) 與 Azure 數位 Twins api 互動：

1. 使用 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 取得您將用來在 Postman 中提出 API 要求的持有人權杖。
1. 設定 Postman 集合，並將 Postman REST 用戶端設定為使用您的持有人權杖進行驗證。
1. 使用已設定的 Postman 來建立要求，並將其傳送至 Azure 數位 Twins Api。

## <a name="prerequisites"></a>Prerequisites

若要繼續使用 Postman 來存取 Azure 數位 Twins Api，您必須設定 Azure 數位 Twins 實例並下載 Postman。 本節的其餘部分會引導您完成這些步驟。

### <a name="set-up-azure-digital-twins-instance"></a>設定 Azure Digital Twins 執行個體

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>下載 Postman

接下來，下載 Postman 用戶端的桌上出版本。 流覽至 [*www.getpostman.com/apps*](https://www.getpostman.com/apps) ，並遵循提示來下載應用程式。

## <a name="get-bearer-token"></a>取得持有人權杖

既然您已設定 Postman 和 Azure 數位 Twins 實例，您將需要取得持有人權杖，讓 Postman 要求可以用來授權 Azure 數位 Twins Api。

有幾種可能的方法可以取得此權杖。 本文會使用 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 來登入您的 Azure 帳戶，並以該方式取得權杖。

如果您在 [本機安裝](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)了 Azure CLI，您可以在您的電腦上啟動命令提示字元，以執行下列命令。
否則，您可以在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com) 視窗，然後在該處執行命令。

1. 首先，請確定您已使用適當的認證登入 Azure，方法是執行下列命令：

    ```azurecli-interactive
    az login
    ```

1. 接下來，使用 [az 帳戶的 get 存取權杖](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token) 命令來取得持有人權杖，以存取 Azure 數位 Twins 服務。

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. 複製 `accessToken` 結果中的值，並將其儲存以供下一節使用。 這是您將提供給 Postman 來驗證要求的 **權杖值** 。

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="顯示顯示 az 帳戶 get 存取權杖命令結果的本機主控台視窗。結果中的其中一個欄位稱為 accessToken，而其範例值（以 ey 開頭）會反白顯示。":::

>[!TIP]
>此權杖的有效時間至少為5分鐘，最大值為60分鐘。 如果您已用完目前權杖分配的時間，您可以重複此區段中的步驟，以取得新的權杖。

## <a name="set-up-postman-collection-and-authorization"></a>設定 Postman 收集與授權

接下來，設定 Postman 以提出 API 要求。
這些步驟會在您的本機 Postman 應用程式中發生，因此請繼續並在您的電腦上開啟 Postman 應用程式。

### <a name="create-a-postman-collection"></a>建立 Postman 集合

Postman 中的要求會) 的要求 (群組中儲存在 **集合** 中。 當您建立集合來將要求分組時，可以一次將一般設定套用至許多要求。 如果您打算針對 Azure 數位 Twins Api 建立多個要求，這可以大幅簡化授權，因為您只需要針對整個集合設定一次驗證。

1. 若要建立集合，請按 [ *+ 新增集合* ] 按鈕。

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="新開啟的 Postman 視窗的視圖。[新增集合] 按鈕會反白顯示":::

1. 在接下來的 [ *建立新集合* ] 視窗中，提供集合的 **名稱** 和選擇性 **描述** 。

接下來，繼續進行下一節，將持有人權杖新增至集合以供授權之用。

### <a name="add-authorization-token-and-finish-collection"></a>新增授權權杖和完成收集

1. 在 [*建立新集合*] 對話方塊中，移至 [*授權*] 索引標籤。您將在 [[取得持有人權杖](#get-bearer-token)] 區段中放置您收集的 **權杖值**，以便將它用於集合中的所有 API 要求。

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="[建立新集合] Postman 視窗，其中顯示 [授權] 索引標籤。":::

1. 將 *類型* 設定為 _**OAuth 2.0**_，並將您的存取權杖貼到 [ *存取權杖* ] 方塊中。

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="[建立新集合] Postman 視窗，其中顯示 [授權] 索引標籤。已選取 [OAuth 2.0] 類型，而 [存取權杖] 方塊中會反白顯示存取權杖值。":::

1. 貼上您的持有人權杖之後，請按 [ *建立* ] 以完成建立您的集合。

您現在可以從主要 Postman 視圖的 [ *集合*] 底下看到新的集合。

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="主要 Postman 視窗的視圖。新建立的集合會在 [集合] 索引標籤中反白顯示。":::

## <a name="create-a-request"></a>建立要求

完成先前的步驟之後，您可以建立 Azure 數位對應項 Api 的要求。

1. 若要建立要求，請按 [ *+ 新增* ] 按鈕。

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="主要 Postman 視窗的視圖。[新增] 按鈕已反白顯示":::

1. 選擇 [ *要求*]。

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="您可以選取的選項，以建立新的內容。[要求] 選項已反白顯示":::

1. 此動作會開啟 [ *儲存要求* ] 視窗，您可以在其中輸入要求的名稱，提供選擇性的描述，然後選擇其所屬的集合。 填入詳細資料，並將要求儲存至您稍早建立的集合。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="查看 [儲存要求] 視窗，您可以在其中填寫所述的欄位。[儲存至 Azure 數位 Twins 集合] 按鈕會反白顯示":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

您現在可以在集合下查看您的要求，然後選取它來提取其可編輯的詳細資料。

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="主要 Postman 視窗的視圖。Azure 數位 Twins 集合已展開，而 [查詢 Twins] 要求已反白顯示。要求的詳細資料會顯示在頁面的中央。" lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>設定要求詳細資料

若要對其中一個 Azure 數位 Twins Api 提出 Postman 要求，您需要 API 的 URL，以及其所需詳細資料的相關資訊。 您可以在 [Azure 數位 Twins REST API 參考檔](/rest/api/azure-digitaltwins/)中找到這項資訊。

為了繼續進行範例查詢，本文將使用查詢 API (及其 [參考檔](/rest/api/digital-twins/dataplane/query/querytwins)) 來查詢實例中的所有數位 twins。

1. 從參考檔中取得要求 URL 和類型。 針對查詢 API，這是 *POST https://digitaltwins-name.digitaltwins.azure.net/query?api-version=2020-10-31*。
1. 在 Postman 中，設定要求的類型，並輸入要求 URL，並視需要填入 URL 中的預留位置。 您將從 [[*必要條件*](#prerequisites)] 區段使用實例的 **主機名稱**。
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="在新要求的詳細資料中，參考檔中的查詢 URL 已填入 [要求 URL] 方塊中。" lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. 檢查 [ *參數] 索引* 標籤中針對要求所顯示的參數是否符合參考檔中所述的參數。 針對 Postman 中的這項要求，在 `api-version` 上一個步驟中輸入要求 URL 時，會自動填入參數。 針對查詢 API，這是唯一必要的參數，所以會完成此步驟。
1. 在 [ *授權* ] 索引標籤中，將 [ *類型* ] 設定為 [ *從父系繼承驗證*]。 這表示此要求會使用您稍早針對整個集合所設定的驗證。
1. 檢查 [ *標頭* ] 索引標籤中針對要求所顯示的標頭是否符合參考檔中所述的標頭。 針對此要求，會自動填入數個標頭。 若為查詢 API，則不需要任何標頭選項，因此會完成此步驟。
1. 請檢查 [ *主體* ] 索引標籤中針對要求所顯示的主體是否符合參考檔中所述的需求。 針對查詢 API，需要 JSON 主體才能提供查詢文字。 以下是此要求的範例主體，可查詢實例中的所有數位 twins：

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="在新要求的詳細資料中，會顯示 [主體] 索引標籤。它包含原始的 JSON 主體，並有 ' SELECT * FROM DIGITALTWINS ' 的查詢。"lightbox="media/how-to-use-postman/postman-request-body.png":::

   如需有關製作 Azure 數位 Twins 查詢的詳細資訊，請參閱 [*如何：查詢對應項圖形*](how-to-query-graph.md)。

1. 請查看參考檔，以瞭解您的要求類型可能需要的任何其他欄位。 針對查詢 API，現在已符合 Postman 要求中的所有需求，所以會完成此步驟。
1. 使用 [ *傳送* ] 按鈕傳送完成的要求。
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="在接近新要求的詳細資料時，[傳送] 按鈕會反白顯示。" lightbox="media/how-to-use-postman/postman-request-send.png":::

傳送要求之後，回應詳細資料將會出現在要求底下的 [Postman] 視窗中。 您可以查看回應的狀態碼和任何主體文字。

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="在傳送要求的詳細資料底下，會反白顯示回應的詳細資料。狀態為 200 OK 和 body 文字，描述查詢所傳回的數位 twins。" lightbox="media/how-to-use-postman/postman-request-response.png":::

您也可以將回應與參考檔中提供的預期回應資料進行比較，以驗證結果或深入瞭解所發生的任何錯誤。

## <a name="next-steps"></a>後續步驟

若要深入瞭解數位 Twins Api，請參閱 how [*to：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)，或查看 [REST api 的參考檔](/rest/api/azure-digitaltwins/)。