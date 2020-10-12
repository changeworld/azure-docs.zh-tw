---
title: 使用 Azure 入口網站 Azure 區塊鏈服務設定區塊鏈資料管理員
description: 使用 Azure 入口網站建立和管理 Azure 區塊鏈服務的區塊鏈資料管理員。
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85200676"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>使用 Azure 入口網站設定區塊鏈資料管理員

設定 Azure 區塊鏈服務的區塊鏈資料管理員，以捕獲區塊鏈資料並將其傳送至 Azure 事件方格主題。

若要設定區塊鏈資料管理員的實例，您可以：

* 建立 Azure 區塊鏈服務交易節點的區塊鏈資料管理員實例
* 新增您的區塊鏈應用程式

## <a name="prerequisites"></a>Prerequisites

* 完成 [快速入門：使用 Azure 入口網站或快速入門建立區塊鏈成員](create-member.md) [：使用 Azure CLI 建立 Azure 區塊鏈服務的區塊鏈成員](create-member-cli.md)。 使用區塊鏈資料管理員時，建議使用 Azure 區塊鏈服務 *標準* 層。
* 建立[事件方格主題](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* 了解 [Azure 事件方格中的事件處理常式](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>建立執行個體

區塊鏈資料管理員執行個體都連線至 Azure 區塊鏈服務交易節點，並加以監視。 只有具有交易節點存取權的使用者可以建立連接。 執行個體會從該交易節點中擷取所有的原始區塊和原始交易資料。 區塊鏈資料管理員發佈 **RawBlockAndTransactionMsg** 訊息，此訊息是從 web3 [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) 和 [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) 查詢傳回的資訊超集合。

輸出連線會將區塊鏈資料傳送至 Azure 事件方格。 您在建立執行個體時，會設定單一輸出連線。 區塊鏈資料管理員會針對任何指定的區塊鏈資料管理員執行個體，支援多個事件方格主題輸出連線。 您可將區塊鏈資料傳送至單一目的地，或將區塊鏈資料傳送至多個目的地。 若要加入另一個目的地，請只將額外的輸出連接新增至實例。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您想要連接區塊鏈資料管理員的 Azure 區塊鏈服務成員。 選取 [區塊鏈資料管理員]  。
1. 選取 [新增]  。

    ![新增區塊鏈資料管理員](./media/data-manager-portal/add-instance.png)

    輸入下列詳細資料：

    設定 | 描述
    --------|------------
    名稱 | 為已連線的區塊鏈資料管理員輸入唯一名稱。 區塊鏈資料管理員名稱可包含小寫字母和數位，且長度上限為20個字元。
    交易節點 | 選擇交易節點。 只會列出您擁有讀取權限的交易節點。
    連接名稱 | 為用來傳送區塊鏈交易資料的輸出連線輸入唯一名稱。
    事件方格端點 | 選擇與區塊鏈資料管理員實例位於相同訂用帳戶中的事件方格主題。

1. 選取 [確定]  。

    區塊鏈資料管理員執行個體不到一分鐘即可建立。 執行個體在部署後將會自動啟動。 執行中的區塊鏈資料管理員實例會從交易節點捕獲區塊鏈事件，並將資料傳送至輸出連接。

    新的實例會出現在 Azure 區塊鏈服務成員的區塊鏈資料管理員實例清單中。

    ![區塊鏈資料成員實例的清單](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>新增區塊鏈應用程式

如果您新增區塊鏈應用程式，區塊鏈資料管理員會將應用程式的事件和屬性狀態解碼。 否則，只會傳送原始區塊和原始交易資料。 當部署合約時，區塊鏈資料管理員也會探索合約位址。 您可以將多個區塊鏈應用程式新增至區塊鏈資料管理員實例。

> [!IMPORTANT]
> 目前，未完全支援宣告密度 [陣列類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) 或 [對應類型](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) 的區塊鏈應用程式。 宣告為數組或對應類型的屬性不會在 *ContractPropertiesMsg* 或 *DecodedContractEventsMsg* 訊息中解碼。

區塊鏈資料管理員需要智慧型合約 ABI 和部署的位元組程式碼檔案來新增應用程式。

### <a name="get-contract-abi-and-bytecode"></a>取得合約 ABI 和位元組碼

合約 ABI 會定義智慧型合約的介面。 此介面會說明如何與智慧型合約互動。 您可以使用[適用於 Ethereum 的 Azure 區塊鏈服務開發套件擴充功能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)，將合約 ABI 複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製合約 ABI]  。

    ![Visual Studio Code 窗格，已選取 [複製合約 ABI]](./media/data-manager-portal/abi-devkit.png)

    合約 ABI 會複製到剪貼簿中。

1. 將 **abi** 陣列儲存為 JSON 檔案。 例如 *abi.json*。 您在後續步驟中會用到此檔案。

區塊鏈資料管理員需要將已部署的位元組程式碼用於智慧型合約。 已部署的位元組程式碼與智慧型合約位元組程式碼不同。 您可以使用 Azure 區塊鏈開發套件擴充功能，將位元組程式碼複製到剪貼簿。

1. 在 Visual Studio Code 總管窗格中，展開 Solidity 專案的 **build/contracts** 資料夾。
1. 以滑鼠右鍵按一下合約中繼資料 JSON 檔案。 檔案名稱是智慧型合約名稱再加上 **.json** 副檔名。
1. 選取 [複製交易位元組程式碼]  。

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

1. 為儲存體帳戶建立新的容器。 選取 [容器] > [容器]  。

    ![建立儲存體帳戶容器](./media/data-manager-portal/create-container.png)

    | 欄位 | 描述 |
    |-------|-------------|
    | 名稱  | 為容器命名。 例如 *smartcontract* |
    | 公用存取層級 | 選擇 [私人 (沒有匿名存取)]  |

1. 選取 [確定]  以建立容器。
1. 選取容器，然後選取 [上傳]  。
1. 選擇您在[取得合約 ABI 和位元組程式碼](#get-contract-abi-and-bytecode)一節中所建立的兩個 JSON 檔案。

    ![上傳 Blob](./media/data-manager-portal/upload-blobs.png)

    選取 [上傳]  。

#### <a name="generate-url"></a>產生 URL

為每個 Blob 分別產生一個共用存取簽章。

1. 選取 ABI JSON Blob。
1. 選取 [產生 SAS] 
1. 設定所需的存取簽章到期日，然後選取 [產生 Blob SAS 權杖和 URL]  。

    ![產生 SAS Token](./media/data-manager-portal/generate-sas.png)

1. 複製 **Blob SAS URL** 並加以儲存，以供下一節使用。
1. 針對位元組程式碼 JSON Blob，重複[產生 URL](#generate-url) 步驟。

### <a name="add-application-to-instance"></a>將應用程式新增至實例

1. 從執行個體清單中，選取您的區塊鏈資料管理員執行個體。
1. 選取 [區塊鏈應用程式]  。
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

## <a name="stop-instance"></a>停止實例

當您想要停止捕捉區塊鏈事件並將資料傳送至輸出連接時，請停止區塊鏈管理員實例。 當實例停止時，區塊鏈資料管理員不會產生任何費用。 如需詳細資訊，請參閱 [定價](https://azure.microsoft.com/pricing/details/blockchain-service)。

1. 移至 **[總覽** ]，然後選取 [ **停止**]。

    ![停止實例](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>後續步驟

請嘗試下一個教學課程，使用區塊鏈資料管理員和 Azure Cosmos DB 來建立區塊鏈 transaction message explorer。

> [!div class="nextstepaction"]
> [使用區塊鏈資料管理員將資料傳送至 Azure Cosmos DB](data-manager-cosmosdb.md)