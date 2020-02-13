---
title: 教學課程：搭配使用表單辨識器和 Azure Logic Apps 來分析發票 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您會搭配使用表單辨識器和 Azure Logic Apps 來建立工作流程，以便將使用資料範例來定型和測試模型的流程自動化。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118277"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>教學課程：搭配使用表單辨識器和 Azure Logic Apps 來分析發票

在本教學課程中，您會在使用表單辨識器 (屬於 Azure 認知服務套件一部分的服務) 的 Azure Logic Apps 中建立工作流程，以擷取發票中的資料。 您可以先使用範例資料集來定型表單辨識器模型，然後再以另一個資料集測試該模型。

本教學課程涵蓋下列內容：

> [!div class="checklist"]
> * 要求表單辨識器的存取權
> * 建立 Azure 儲存體 Blob 容器
> * 將資料範例上傳至 Azure Blob 容器
> * 建立 Azure 邏輯應用程式
> * 設定邏輯應用程式以使用表單辨識器資源
> * 執行邏輯應用程式來測試工作流程

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="understand-the-invoice-to-be-analyzed"></a>了解要分析的發票

您用來定型模型和測試模型的範例資料集，可從 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451) 所提供的 .zip 檔案中取得。 請下載並解壓縮 .zip 檔案，並開啟 **/Train** 資料夾下的發票 PDF 檔案。 請注意其資料表中的發票編號、發票日期等等。 

> [!div class="mx-imgBorder"]
> ![發票範例](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

在本教學課程中，您將了解如何使用 Azure Logic Apps 工作流程，將這類資料表中的資訊以 JSON 的格式擷取。

## <a name="create-an-azure-storage-blob-container"></a>建立 Azure 儲存體 Blob 容器

您可以使用此容器來上傳為了定型模型所需的資料範例。

1. 遵循[建立 Azure 儲存體帳戶](../../storage/common/storage-account-create.md)中的指示來建立儲存體帳戶。 使用 **formrecostorage** 作為儲存體帳戶名稱。
1. 遵循[建立 Azure Blob 容器](../../storage/blobs/storage-quickstart-blobs-portal.md)中的指示，在 Azure 儲存體帳戶內建立容器。 使用 **formrecocontainer** 作為容器名稱。 確實地將公用存取層級設定為 [容器 (容器和 Blob 匿名讀取權限)]  。

    > [!div class="mx-imgBorder"]
    > ![建立 Blob 容器](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>將資料範例上傳至 Azure Blob 容器

下載 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451) 中所提供的資料範例。 將資料解壓縮至本機資料夾，並將 **/Train** 資料夾的內容上傳至您稍早建立的 **formrecocontainer**。 遵循[上傳區塊 Blob](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) 中的指示，將資料上傳至容器。

複製容器的 URL。 您稍後將在教學課程中用到此 URL。 如果您在建立儲存體帳戶和容器時所用的名稱和本教學課程所列的名稱一樣，則 URL 會是 *https:\//formrecostorage.blob.core.windows.net/formrecocontainer/* 。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>建立邏輯應用程式

您可以使用 Azure Logic Apps 來自動進行和協調工作與工作流程。 在本教學課程中，您會建立邏輯應用程式，並讓其於接收到您想要分析的發票 (電子郵件附件的形式) 時觸發。 在此工作流程中，您會執行下列工作：
* 設定邏輯應用程式，使其在收到附上發票的電子郵件時自動觸發。
* 設定邏輯應用程式，使其使用表單辨識器的**定型模型**作業，以您上傳至 Azure Blob 儲存體的資料範例來定型模型。
* 設定邏輯應用程式，使其使用表單辨識器的**分析表單**作業，來使用您已定型的模型。 此元件會根據其先前定型的模型，來分析您提供給這個邏輯應用程式的發票。

請遵循下列步驟來設定工作流程。

1. 在主要 Azure 功能表中，選取 [建立資源]   > [整合]   > [邏輯應用程式]  。

1. 在 [建立邏輯應用程式]  底下，提供有關邏輯應用程式的詳細資訊，如下所示。 在完成作業後，選取 [建立]  。

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **名稱** | <*logic-app-name*> | 您的邏輯應用程式名稱，其中只能包含字母、數字、連字號 (`-`)、底線 (`_`)、括弧(`(`、`)`) 和句點 (`.`)。 此範例使用 "My-First-Logic-App"。 |
   | **訂用帳戶** | <*Azure-subscription-name*> | 您的 Azure 訂用帳戶名稱 |
   | **資源群組** | <*Azure-resource-group-name*> | 用來組織相關資源之 [Azure 資源群組](./../../azure-resource-manager/management/overview.md)的名稱。 此範例使用 "My-First-LA-RG"。 |
   | **位置** | <*Azure-region*> | 用來存放邏輯應用程式資訊的區域。 此範例使用「美國西部」。 |
   | **Log Analytics** | 關閉 | 保留診斷記錄的 [關閉]  設定。 |
   ||||

1. 在 Azure 部署您的應用程式之後，請在 Azure 工具列上針對您部署的邏輯應用程式選取 [通知]   > [移至資源]  。 或者，您可以在搜尋方塊中輸入名稱，以尋找並選取您的邏輯應用程式。

   Logic Apps 設計工具會開啟並顯示含有簡介影片和常用觸發程序的頁面。 在 [範本]  底下，選取 [空白邏輯應用程式]  。

   > [!div class="mx-imgBorder"]
   > ![選取邏輯應用程式的空白範本](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>設定邏輯應用程式使其在電子郵件送達時觸發工作流程

在本教學課程中，您會在收到附有發票的電子郵件時觸發工作流程。 本教學課程使用 Office 365 作為電子郵件服務，但您也可以使用任何您想要使用的其他電子郵件提供者。

1. 選取 [全部] 索引標籤、選取 [Office 365 Outlook]  ，然後在 [觸發程序]  底下，選取 [新的電子郵件送達時]  。

    ![透過傳入的電子郵件觸發邏輯應用程式](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. 在 [Office 365 Outlook]  方塊中按一下 [登入]  ，然後輸入詳細資料以登入 Office 365 帳戶。

1. 在下一個對話方塊中，執行下列步驟。
    1. 選取應監視是否有任何新電子郵件的資料夾。
    1. 針對 [有附件]  ，選取 [是]  。 這可確保只有有附件的電子郵件才會觸發工作流程。
    1. 針對 [包含附件]  ，選取 [是]  。 這可確保下游處理中會使用附件的內容。

        > [!div class="mx-imgBorder"]
        > ![設定邏輯應用程式的電子郵件觸發程序](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. 按一下頂端工具列中的 [儲存]  。

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>設定邏輯應用程式以使用表單辨識器定型模型作業

若要使用表單辨識器服務來分析發票，您必須先為模型提供一些可供其分析並從中學習的發票資料範例，以便定型模型。

1. 選取 [新增步驟]  ，然後在 [選擇動作]  底下，搜尋**表單辨識器**。 從顯示的結果中，選取 [表單辨識器]  ，然後在表單辨識器可用的動作底下，選取 [定型模型]  。

    > [!div class="mx-imgBorder"]
    > ![定型表單辨識器模型](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. 在 [表單辨識器] 對話方塊中，提供連線的名稱，然後輸入您針對表單辨識器資源所擷取到的端點 URL 和金鑰。

    > [!div class="mx-imgBorder"]
    > ![表單辨識器的連線名稱](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    按一下頁面底部的 [新增]  。

1. 在 [定型模型]  對話方塊中，針對 [來源]  輸入作為資料範例上傳目的地容器的 URL。

    > [!div class="mx-imgBorder"]
    > ![發票範例的儲存體容器](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. 按一下頂端工具列中的 [儲存]  。

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>設定邏輯應用程式以使用表單辨識器分析表單作業

在本節中，您會將**分析表單**作業新增至工作流程。 此作業會使用已定型的模型來分析提供給邏輯應用程式的新發票。

1. 選取 [新增步驟]  ，然後在 [選擇動作]  底下，搜尋**表單辨識器**。 從顯示的結果中，選取 [表單辨識器]  ，然後在表單辨識器可用的動作底下，選取 [分析表單]  。

    > [!div class="mx-imgBorder"]
    > ![分析表單辨識器模型](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. 在 [分析表單]  對話方塊中，執行下列步驟：

    1. 按一下 [模型識別碼]  文字方塊，然後在開啟的對話方塊中，選取 [動態內容]  索引標籤底下的 [modelId]  。 如此一來，您就可以為流程應用程式提供您在上一節所定型模型的模型識別碼。

        > [!div class="mx-imgBorder"]
        > ![針對表單辨識器使用 ModelID](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. 按一下 [文件]  文字方塊，然後在開啟的對話方塊中，選取 [動態內容]  索引標籤底下的 [附件內容]  。 如此，就可以將流程設定為使用在電子郵件中附加用以觸發工作流程的範例發票檔案。

        > [!div class="mx-imgBorder"]
        > ![使用電子郵件附件來分析發票](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. 按一下頂端工具列中的 [儲存]  。

### <a name="extract-the-table-information-from-the-invoice"></a>從發票中擷取資料表資訊

在本節中，您會設定邏輯應用程式，使其從發票內的資料表中擷取資訊。

1. 選取 [新增動作]  、在 [選擇動作]  底下搜尋 [撰寫]  ，然後在可用的動作底下再次選取 [撰寫]  。
    ![從發票中擷取資料表資訊](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. 在 [撰寫]  對話方塊中，按一下 [輸入]  文字方塊，然後從彈出的對話方塊中選取 [資料表]  。

    > [!div class="mx-imgBorder"]
    > ![從發票中擷取資料表資訊](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. 按一下 [檔案]  。

## <a name="test-your-logic-app"></a>測試應用程式邏輯

為了測試邏輯應用程式，請在您於 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451) 下載的資料集範例中，使用位於 **/Test** 資料夾內的發票範例。 請遵循下列步驟：

1. 從應用程式的 Azure Logic Apps 設計工具中，選取頂端工具列上的 [執行]  。 工作流程現在已啟用，並且會等待收到有附上發票的電子郵件。
1. 將附有發票範例的電子郵件傳送到您在建立邏輯應用程式時所提供的電子郵件地址。 請確定電子郵件有傳遞至您在設定邏輯應用程式時所提供的資料夾。
1. 一旦電子郵件傳遞至資料夾，Logic Apps 設計工具就會顯示一個畫面，內有每個階段的進度。 在下面的螢幕擷取畫面中，您會看到系統已收到有附件的電子郵件，且工作流程正在進行中。

    > [!div class="mx-imgBorder"]
    > ![藉由傳送電子郵件來啟動工作流程](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. 在工作流程的所有階段都執行完成後，Logic Apps 設計工具就會針對每個階段顯示綠色核取記號。 在設計工具視窗中，選取 [For each 2]  ，然後選取 [撰寫]  。

    > [!div class="mx-imgBorder"]
    > ![工作流程已完成](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    從 [輸出]  方塊中，複製輸出並貼到任何文字編輯器中。

1. 將 JSON 輸出與您透過電子郵件附件形式傳送的發票範例進行比較。 確認 JSON 資料有對應到發票內資料表中的資料。

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    您已成功完成此教學課程！

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定 Azure Logic Apps 工作流程，使其使用表單辨識器來定型模型，並擷取發票的內容。 接下來，請了解如何建置訓練資料集，以便使用您自己的表單來建立類似案例。

> [!div class="nextstepaction"]
> [建置訓練資料集](build-training-data-set.md)