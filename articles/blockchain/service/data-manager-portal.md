---
title: 使用 Azure 門戶設定區塊鏈資料管理器 - Azure 區塊鏈服務
description: 使用 Azure 門戶創建和管理 Azure 區塊鏈服務的區塊鏈數據管理器。
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 08f5a4a807087afce13dd4a6e96c0e9dd0a36103
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260593"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>使用 Azure 入口網站設定區塊鏈資料管理員

為 Azure 區塊鏈服務配置區塊鏈數據管理員,以捕捉區塊鏈資料並將其發送到 Azure 事件網格主題。

要設定區塊鏈資料管理員, 您需要:

* 為 Azure 區塊鏈服務事務節點建立區塊鏈資料管理員實體
* 新增區塊鏈應用程式

## <a name="prerequisites"></a>Prerequisites

* 完整的[快速啟動:使用 Azure 門戶或快速入門建立區塊鏈成員](create-member.md)[:使用 Azure CLI 建立 Azure 區塊鏈服務區塊鏈成員](create-member-cli.md)。 使用區塊鏈數據管理員時,建議使用 Azure 區塊鏈服務*標準*層。
* 建立[事件格線主題](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件方格中的事件處理常式](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>建立執行個體

區塊鏈資料管理員執行個體都連線至 Azure 區塊鏈服務交易節點，並加以監視。 只有有權訪問事務節點的使用者才能創建連接。 執行個體會從該交易節點中擷取所有的原始區塊和原始交易資料。 區塊鏈數據管理器發佈**RawBlock 和交易Msg**消息,該消息是從 Web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock)返回的資訊集,獲取[交易](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)查詢。

輸出連線會將區塊鏈資料傳送至 Azure 事件方格。 您在建立執行個體時，會設定單一輸出連線。 區塊鏈資料管理員會針對任何指定的區塊鏈資料管理員執行個體，支援多個事件方格主題輸出連線。 您可將區塊鏈資料傳送至單一目的地，或將區塊鏈資料傳送至多個目的地。 要添加其他目標,只需向實例添加其他出站連接。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 轉到要連接到區塊鏈數據管理器的 Azure 區塊鏈服務成員。 選取 [區塊鏈資料管理員]****。
1. 選取 [新增]  。

    ![新增區塊鏈資料管理員](./media/data-manager-portal/add-instance.png)

    輸入下列詳細資料：

    設定 | 描述
    --------|------------
    名稱 | 為已連線的區塊鏈資料管理員輸入唯一名稱。 區塊鏈數據管理員名稱可以包含小寫字母和數位,最大長度為 20 個字元。
    交易節點 | 選擇事務節點。 僅列出具有讀取訪問許可權的事務節點。
    連接名稱 | 為用來傳送區塊鏈交易資料的輸出連線輸入唯一名稱。
    事件方格端點 | 在與區塊鏈數據管理器實例相同的訂閱中選擇事件網格主題。

1. 選取 [確定]  。

    區塊鏈資料管理員執行個體不到一分鐘即可建立。 執行個體在部署後將會自動啟動。 正在運行的區塊鏈數據管理器實例從事務節點捕獲區塊鏈事件,並將數據發送到出站連接。

    新實例將顯示在 Azure 區塊鏈服務成員的區塊鏈數據管理器實例清單中。

    ![區塊鏈資料成員實體清單](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>新增區塊鏈應用程式

如果您添加區塊鏈應用程式,區塊鏈數據管理器會解碼應用程式的事件和屬性狀態。 否則,僅發送原始塊和原始事務數據。 區塊鏈數據管理器還會在部署合同時發現合同位址。 您可以將多個區塊鏈應用程式添加到區塊鏈數據管理器實例中。

> [!IMPORTANT]
> 目前,聲明實體陣列[類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays)或[映射類型的](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)區塊鏈應用程式並不完全受支援。 聲明為陣列或映射類型的屬性不會在*合同屬性Msg*或*解碼合同事件Mg*消息中解碼。

區塊鏈資料管理員需要智慧合約 ABI 和部署的位元組碼檔來添加應用程式。

### <a name="get-contract-abi-and-bytecode"></a>取得合約 ABI 與位元組碼

合約 ABI 會定義智慧型合約的介面。 此介面會說明如何與智慧型合約互動。 您可以使用[適用於 Ethereum 的 Azure 區塊鏈服務開發套件擴充功能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)，將合約 ABI 複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製合約 ABI]****。

    ![Visual Studio Code 窗格，已選取 [複製合約 ABI]](./media/data-manager-portal/abi-devkit.png)

    合約 ABI 會複製到剪貼簿中。

1. 將 **abi** 陣列儲存為 JSON 檔案。 例如 *abi.json*。 您在後續步驟中會用到此檔案。

區塊鏈資料管理員需要將已部署的位元組程式碼用於智慧型合約。 已部署的位元組程式碼與智慧型合約位元組程式碼不同。 您可以使用 Azure 區塊鏈開發套件擴充功能，將位元組程式碼複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製交易位元組程式碼]****。

    ![Visual Studio Code 窗格，已選取 [複製交易位元組程式碼]](./media/data-manager-portal/bytecode-devkit.png)

    位元組程式碼會複製到剪貼簿中。

1. 將**位元組程式碼**值儲存為 JSON 檔案。 例如 *bytecode.json*。 您在後續步驟中會用到此檔案。

下列範例顯示在 VS Code 編輯器中開啟的 *abi.json* 和 *bytecode.json* 檔案。 您的檔案應會與其相似。

![abi.json 和 bytecode.json 檔案的範例](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>建立合約 ABI 和位元組程式碼 URL

區塊鏈資料管理員在新增應用程式時，必須可透過 URL 存取合約 ABI 和位元組程式碼檔案。 您可以使用 Azure 儲存體帳戶來提供可私下存取的 URL。

#### <a name="create-storage-account"></a>建立儲存體帳戶

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>上傳合約檔案

1. 為儲存體帳戶建立新的容器。 選取 [容器] > [容器]****。

    ![建立儲存體帳戶容器](./media/data-manager-portal/create-container.png)

    | 欄位 | 描述 |
    |-------|-------------|
    | 名稱  | 為容器命名。 例如 *smartcontract* |
    | 公用存取層級 | 選擇 [私人 (沒有匿名存取)]** |

1. 選取 [確定]**** 以建立容器。
1. 選取容器，然後選取 [上傳]****。
1. 選擇您在[取得合約 ABI 和位元組程式碼](#get-contract-abi-and-bytecode)一節中所建立的兩個 JSON 檔案。

    ![上傳 Blob](./media/data-manager-portal/upload-blobs.png)

    選取 [上傳]****。

#### <a name="generate-url"></a>產生 URL

為每個 Blob 分別產生一個共用存取簽章。

1. 選取 ABI JSON Blob。
1. 選取 [產生 SAS]****
1. 設定所需的存取簽章到期日，然後選取 [產生 Blob SAS 權杖和 URL]****。

    ![產生 SAS Token](./media/data-manager-portal/generate-sas.png)

1. 複製 **Blob SAS URL** 並加以儲存，以供下一節使用。
1. 針對位元組程式碼 JSON Blob，重複[產生 URL](#generate-url) 步驟。

### <a name="add-application-to-instance"></a>新增應用程式到實體

1. 從執行個體清單中，選取您的區塊鏈資料管理員執行個體。
1. 選取 [區塊鏈應用程式]****。
1. 選取 [新增]  。

    ![新增區塊鏈應用程式](./media/data-manager-portal/add-application.png)

    輸入區塊鏈應用程式的名稱，以及智慧型合約 ABI 和位元組程式碼 URL。

    設定 | 描述
    --------|------------
    名稱 | 輸入要追蹤的區塊鏈應用程式的唯一名稱。
    合約 ABI | 合約 ABI 檔案的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組程式碼 URL](#create-contract-abi-and-bytecode-url)。
    合約位元組程式碼 | 位元組程式碼檔案的 URL 路徑。 如需詳細資訊，請參閱[建立合約 ABI 和位元組程式碼 URL](#create-contract-abi-and-bytecode-url)。

1. 選取 [確定]  。

    應用程式建立後，應用程式會出現在區塊鏈應用程式清單中。

    ![區塊鏈應用程式清單](./media/data-manager-portal/artifact-list.png)

您可以刪除 Azure 儲存體帳戶，或用它來設定更多區塊鏈應用程式。 如果您要刪除 Azure 儲存體帳戶，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

## <a name="stop-instance"></a>停止實體

當您要停止捕獲區塊鏈事件並將數據發送到出站連接時,停止區塊鏈管理器實例。 當實例停止時,區塊鏈數據管理器不收取任何費用。 有關詳細資訊,請參閱[定價](https://azure.microsoft.com/pricing/details/blockchain-service)。

1. 轉到 **「概述」** 並選擇 **「停止**」。

    ![停止實體](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>後續步驟

嘗試使用區塊鏈資料管理器和 Azure Cosmos DB 創建區塊鏈事務消息資源管理器的下一教程。

> [!div class="nextstepaction"]
> [使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB](data-manager-cosmosdb.md)