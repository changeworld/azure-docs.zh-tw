---
title: 手動執行非 HTTP 觸發的 Azure 函式
description: 使用 HTTP 要求執行非 HTTP 觸發的 Azure 函式
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: fd7b0be967c7a0bbc605c51408448917b5222d36
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83121724"
---
# <a name="manually-run-a-non-http-triggered-function"></a>手動執行非 HTTP 觸發的函式

本文示範如何透過特殊格式的 HTTP 要求手動執行非 HTTP 觸發的函式。

在某些內容中，您可能需要「隨選」執行間接觸發的 Azure 函式。  間接觸發程序的範例包括[排程的函式](./functions-create-scheduled-function.md)或因[另一個資源的動作](./functions-create-storage-blob-triggered-function.md)而執行的函式。 

下列範例會使用 [Postman](https://www.getpostman.com/)，但您可以使用 [cURL](https://curl.haxx.se/)、[Fiddler](https://www.telerik.com/fiddler) 或任何其他類似的工具來傳送 HTTP 要求。

## <a name="define-the-request-location"></a>定義要求位置

若要執行非 HTTP 觸發的函式，您必須要有適當方式可將要求傳送至 Azure 以執行函式。 用來提出此要求的 URL 會採用特定的格式。

![定義要求位置：主機名稱 + 資料夾路徑 + 函式名稱](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **主機名稱：** 函式應用程式的公用位置，由函式應用程式的名稱加上 *azurewebsites.net* 或您的自訂網域而組成。
- **資料夾路徑：** 若要透過 HTTP 要求來存取非 HTTP 觸發的函式，您必須透過資料夾 *admin/functions* 傳送要求。
- **函式名稱：** 您要執行的函式名稱。

在要求 Azure 執行函式時，您在 Postman 中使用此要求位置以及函式的主要金鑰。

> [!NOTE]
> 在本機執行時，不需要函式的主要金鑰。 您可以直接[呼叫函式](#call-the-function)，省略 `x-functions-key` 標頭。

## <a name="get-the-functions-master-key"></a>取得函式的主要金鑰

1. 在 Azure 入口網站中流覽至您的函式，然後選取 [**函數金鑰**]。 然後，選取您想要複製的功能鍵。 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="找出要複製的主要金鑰。" border="true":::

1. 在 [**編輯金鑰**] 區段中，將 [金鑰] 值複製到您的剪貼簿，然後選取 **[確定]**。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="將主要金鑰複製到剪貼簿。" border="true":::

1. 複製 *_master*金鑰之後，請選取 [程式**代碼 + 測試**]，然後選取 [**記錄**]。 您會看到您從 Postman 手動執行函式時所傳回的函式訊息記錄在此處。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="查看記錄以查看主要金鑰測試結果。" border="true":::

> [!CAUTION]  
> 由於主要金鑰會在您的函數應用程式中授與提高的權限，因此您不應與第三方共用此金鑰，或是在應用程式中加以散發。

## <a name="call-the-function"></a>呼叫函式

開啟 Postman 並遵循下列步驟：

1. **在 URL 文字方塊中輸入要求位置**。
1. 確定 HTTP 方法設定為 [POST]****。
1. 選取 [標頭]**** 索引標籤。
1. 輸入**x 函式-key**作為第一個索引鍵，並貼上主要金鑰（從剪貼簿）做為值。
1. 輸入**Content-type**作為第二個金鑰，並輸入**application/json**做為值。

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Postman 標頭設定。" border="true":::

1. 選取 [Body]**** \(本文\) 索引標籤。
1. 輸入 **{"input"： "test"}** 做為要求的主體。

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Postman 主體設定。" border="true":::

1. 選取 [傳送]。
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="使用 Postman 傳送要求。" border="true":::

    然後，Postman 會報告狀態 **202 已接受**。

1. 接著請返回您在 Azure 入口網站中的函式。 查看記錄檔，您會看到來自函式手動呼叫的訊息。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="查看記錄以查看主要金鑰測試結果。" border="true":::

## <a name="next-steps"></a>後續步驟

- [在 Azure Functions 中測試程式碼的策略](./functions-test-a-function.md)
- [Azure Functions 事件方格觸發程序本機偵錯](./functions-debug-event-grid-trigger-local.md)
