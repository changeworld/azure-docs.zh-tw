---
title: 教學課程 - 從主要區域模擬資料讀取失敗
titleSuffix: Azure Storage
description: 當儲存體帳戶設定為讀取權限異地區域備援儲存體 (RA-GZRS) 時，模擬從主要區域讀取資料時發生的錯誤。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91280346"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>教學課程：從主要區域模擬資料讀取失敗

本教學課程是一個系列的第二部分。 在其中，您可藉由模擬失敗來了解[讀取權限異地區域備援儲存體](../common/storage-redundancy.md) (RA-GZRS) 的優點。

為了模擬失敗，您可使用[靜態路由](#simulate-a-failure-with-an-invalid-static-route)或 [Fiddler](#simulate-a-failure-with-fiddler)。 這兩種方法都會允許您模擬對[讀取權限異地備援](../common/storage-redundancy.md) (RA-GZRS) 儲存體帳戶的主要端點要求失敗，而導致從次要端點讀取應用程式。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

在本系列的第二部分中，您將瞭解如何：

> [!div class="checklist"]
> * 執行和暫停應用程式
> * 使用[無效的靜態路由](#simulate-a-failure-with-an-invalid-static-route)或 [Fiddler](#simulate-a-failure-with-fiddler) 來模擬失敗
> * 模擬主要端點還原

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，請先完成前一個教學課程：[使用 Azure 儲存體讓應用程式資料具有高可用性][previous-tutorial]。

若要使用靜態路由來模擬失敗，請使用提升權限的命令提示字元。

若要使用 Fiddler 來模擬失敗，請下載和[安裝 Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>使用無效的靜態路由模擬失敗

您可以建立無效的靜態路由，並將其用於所有對您[讀取權限異地備援](../common/storage-redundancy.md) (RA-GZRS) 儲存體帳戶之主要端點的要求。 在本教學課程中，會使用本機主機作為將要求路由傳送到儲存體帳戶的閘道。 以本機主機作為閘道，會導致所有對儲存體帳戶主要端點的要求在主機內形成迴圈，進而導致失敗。 請遵循下列步驟，以使用無效的靜態路由模擬失敗和主要端點還原。

### <a name="start-and-pause-the-application"></a>啟動和暫停應用程式

使用[上一個教學課程][previous-tutorial]中的指示來啟動範例和下載測試檔案，並確認其來自主要儲存體。 然後，您可以根據目標平台，以手動方式暫停範例或等候提示。

### <a name="simulate-failure"></a>模擬失敗

在應用程式暫停的情況下，於 Windows 上以系統管理員身分開啟命令提示字元，或在 Linux 上以 root 身分執行終端機。

在命令提示字元或終端機上輸入下列命令，以取得儲存體帳戶主要端點網域的相關資訊，並將 `STORAGEACCOUNTNAME` 取代為儲存體帳戶的名稱。

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

將儲存體帳戶的 IP 位址複製到文字編輯器，供後續使用。

若要取得本機主機的 IP 位址，請在 Windows 命令提示字元上輸入 `ipconfig`，或在 Linux 終端機上輸入 `ifconfig`。

若要為目的地主機新增靜態路由，請在 Windows 命令提示字元或 Linux 終端機上輸入下列命令，並將 `<destination_ip>` 取代為儲存體帳戶的 IP 位址，以及將 `<gateway_ip>` 取代為本機主機的 IP 位址。

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

在具有執行中範例的視窗內繼續執行應用程式，或按下適當按鍵來下載範例檔案，並確認其來自次要儲存體。 然後，您可以再次暫停範例，或等候提示。

### <a name="simulate-primary-endpoint-restoration"></a>模擬主要端點還原

若要模擬再次恢復運作的主要端點，請從路由表中刪除無效的靜態路由。 這可讓所有對主要端點的要求透過預設閘道路由傳送。 請在 Windows 命令提示字元或 Linux 終端機上輸入下列命令。

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

然後，您就可以繼續執行應用程式，或按下適當按鍵來重新下載範例檔案，但這次請確認其再次來自主要儲存體。

## <a name="simulate-a-failure-with-fiddler"></a>使用 Fiddler 模擬失敗

若要使用 Fiddler 模擬失敗，您必須將要求的失敗回應插入 RA-GZRS 儲存體帳戶的主要端點。

下列各節說明如何使用 fiddler 來模擬失敗和主要端點還原。

### <a name="launch-fiddler"></a>啟動 Fiddler

開啟 Fiddler，選取 [規則] 和 [自訂規則]。

![自訂 Fiddler 規則](media/simulate-primary-region-failure/figure1.png)

Fiddler ScriptEditor 會啟動並顯示 **SampleRules.js** 檔案。 此檔案是用來自訂 Fiddler。

在 `OnBeforeResponse` 函式中貼入下列程式碼範例，並將 `STORAGEACCOUNTNAME` 取代為儲存體帳戶的名稱。 視範例而定，您可能還需要將 `HelloWorld` 取代為所要下載的測試檔案名稱 (或前置詞，例如 `sampleFile`)。 新的程式碼會標為註解，以確保其不會立即執行。

完成後，選取 [檔案] 和 [儲存] 以儲存變更。 讓 ScriptEditor 視窗保持開啟以供下列步驟使用。

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![貼上自訂規則](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>啟動和暫停應用程式

使用[上一個教學課程][previous-tutorial]中的指示來啟動範例和下載測試檔案，並確認其來自主要儲存體。 然後，您可以根據目標平台，以手動方式暫停範例或等候提示。

### <a name="simulate-failure"></a>模擬失敗

在應用程式暫停的情況下切換回 Fiddler，並將 `OnBeforeResponse` 函式中儲存的自訂規則取消註解。 請務必選取 [檔案] 和 [儲存] 來儲存變更，以便讓規則生效。 此程式碼會尋找 RA-GZRS 儲存體帳戶要求，若路徑包含範例檔案的名稱，則會傳回 `503 - Service Unavailable` 的回應碼。

在具有執行中範例的視窗內繼續執行應用程式，或按下適當按鍵來下載範例檔案，並確認其來自次要儲存體。 然後，您可以再次暫停範例，或等候提示。

### <a name="simulate-primary-endpoint-restoration"></a>模擬主要端點還原

在 Fiddler 中，將自訂規則再次移除或標為註解。 選取 [檔案] 和 [儲存] 來確保規則不再有效。

在具有執行中範例的視窗內繼續執行應用程式，或按下適當按鍵來下載範例檔案，並確認其再次來自主要儲存體。 然後您就可以結束範例。

## <a name="next-steps"></a>後續步驟

在系列的第二部分中，您已了解模擬失敗以測試讀取權限異地備援儲存體的相關資訊。

若要深入了解 RA-GZRS 儲存體的運作方式及其相關聯的風險，請閱讀以下文章：

> [!div class="nextstepaction"]
> [使用 RA-GZRS 設計 HA 應用程式](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
