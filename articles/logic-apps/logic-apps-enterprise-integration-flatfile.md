---
title: 編碼或解碼一般檔案
description: 使用企業整合套件將一般檔案編碼或解碼 Azure Logic Apps 中的企業整合
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001480"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>在 Azure Logic Apps 中使用企業整合套件將一般檔案編碼或解碼

在企業對企業 (B2B) 案例中傳送 XML 內容給商務夥伴之前，您可能會想要先對該內容進行編碼。 藉由建立邏輯應用程式，您可以使用「一般檔案 **」連接器來** 編碼和解碼一般檔案。 您的邏輯應用程式可以從各種來源（例如要求觸發程式、另一個應用程式或 [Azure Logic Apps 所支援](../connectors/apis-list.md)的其他連接器）取得此 XML 內容。 如需詳細資訊，請參閱 [什麼是 Azure Logic Apps](logic-apps-overview.md)？

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要使用「一般檔案 **」連接器的** 邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程式。 「一般檔案 **」連接器只** 提供動作，而非觸發程式。 您可以使用觸發程式或其他動作，將 XML 內容饋送至邏輯應用程式，以進行編碼或解碼。 如果您還不熟悉邏輯應用程式，請檢閱[快速入門：如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與您的 Azure 訂用帳戶相關聯的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，並連結至您打算使用「一般檔案 **」連接器**[的邏輯應用程式](./logic-apps-enterprise-integration-create-integration-account.md#link-account)。 邏輯應用程式與整合帳戶必須位於相同位置或相同的 Azure 區域中。

* 您已上傳至整合帳戶以便編碼或解碼 XML 內容的一般檔案[架構](logic-apps-enterprise-integration-schemas.md)

* 您已在整合帳戶中定義至少兩個[交易夥伴](logic-apps-enterprise-integration-partners.md)

## <a name="add-flat-file-encode-action"></a>新增一般檔案編碼動作

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式的觸發程式或動作底下，選取 [**新增步驟] 新增**  >  **動作**。 此範例會使用要求觸發程式，此觸發程式會 **在收到 HTTP 要求時**命名，並處理來自邏輯應用程式外部的輸入要求。

   > [!TIP]
   > 提供 JSON 架構是選擇性的。 如果您有來自輸入要求的範例承載，請選取 [ **使用範例承載來產生架構**]，輸入範例承載，然後選取 [ **完成**]。 架構會顯示在 [ **要求本文 JSON 架構** ] 方塊中。

1. 在 **[選擇動作**] 下，輸入 `flat file` 。 從動作清單中，選取此動作：一般檔案 **編碼**

   ![選取 [一般檔案編碼] 動作](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. 在 [ **內容** ] 方塊內按一下，以顯示動態內容清單。 從清單中的 [ **收到 HTTP 要求時** ] 區段中，選取 [ **主體** ] 屬性，其中包含來自觸發程式的要求本文輸出，以及要編碼的內容。

   ![從動態內容清單中選取要編碼的內容](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > 如果您在動態內容清單中看不到 [ **Body** ] 屬性，請選取 [**收到 HTTP 要求時**] 區段標籤旁的 [**查看更多**]。
   > 您也可以直接在 [ **內容** ] 方塊中輸入要解碼的內容。

1. 從 [ **架構名稱** ] 清單中，選取您連結的整合帳戶中要用於編碼的架構，例如：

   ![選取用於編碼的架構](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > 如果清單中未出現任何架構，您的整合帳戶就不會包含任何要用於編碼的架構檔案。 將您想要使用的架構上傳至整合帳戶。

1. 儲存您的邏輯應用程式。 若要測試您的連接器，請對 HTTPS 端點提出要求，這會出現在要求觸發程式的 [ **HTTP POST URL** ] 屬性中，並包含您想要在要求本文中編碼的 XML 內容。

您現在已經完成設定一般檔案編碼動作。 在真實世界的應用程式中，您可能會想要將編碼的資料儲存在企業營運 (LOB) 應用程式（例如 Salesforce）。 或者，您可以將編碼的資料傳送給交易夥伴。 若要將編碼動作的輸出傳送到 Salesforce 或交易夥伴，請使用 [Azure Logic Apps 中提供](../connectors/apis-list.md)的其他連接器。

## <a name="add-flat-file-decode-action"></a>新增一般檔案解碼動作

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式的觸發程式或動作底下，選取 [**新增步驟] 新增**  >  **動作**。 此範例會使用要求觸發程式，此觸發程式會 **在收到 HTTP 要求時**命名，並處理來自邏輯應用程式外部的輸入要求。

   > [!TIP]
   > 提供 JSON 架構是選擇性的。 如果您有來自輸入要求的範例承載，請選取 [ **使用範例承載來產生架構**]，輸入範例承載，然後選取 [ **完成**]。 架構會顯示在 [ **要求本文 JSON 架構** ] 方塊中。

1. 在 **[選擇動作**] 下，輸入 `flat file` 。 從動作清單中，選取此動作：一般檔案 **解碼**

   ![選取 [一般檔案解碼] 動作](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. 在 [ **內容** ] 方塊內按一下，以顯示動態內容清單。 從清單中的 [ **收到 HTTP 要求時** ] 區段中，選取 [ **主體** ] 屬性，其中包含來自觸發程式的要求本文輸出，以及要解碼的內容。

   ![從動態內容清單中選取要解碼的內容](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > 如果您在動態內容清單中看不到 [ **Body** ] 屬性，請選取 [**收到 HTTP 要求時**] 區段標籤旁的 [**查看更多**]。 您也可以直接在 [ **內容** ] 方塊中輸入要解碼的內容。

1. 從 [ **架構名稱** ] 清單中，選取連結的整合帳戶中要用於解碼的架構，例如：

   ![選取要用於解碼的架構](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > 如果清單中未出現任何架構，您的整合帳戶就不會包含任何要用於解碼的架構檔案。 將您想要使用的架構上傳至整合帳戶。

1. 儲存您的邏輯應用程式。 若要測試您的連接器，請對 HTTPS 端點提出要求，這會出現在要求觸發程式的 **HTTP POST URL** 屬性中，並包含您想要在要求主體中解碼的 XML 內容。

您現在已完成設定一般檔案解碼動作。 在真實世界的應用程式中，您可能會想要將已解碼的資料儲存在企業營運 (LOB) 應用程式（例如 Salesforce）。 或者，您可以將已解碼的資料傳送給交易夥伴。 若要將解碼動作的輸出傳送到 Salesforce 或交易夥伴，請使用 [Azure Logic Apps 中提供](../connectors/apis-list.md)的其他連接器。

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解 [企業整合套件](logic-apps-enterprise-integration-overview.md)
