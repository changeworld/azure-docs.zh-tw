---
title: Azure IoT 邊緣模組 SKU |Azure 應用商店
description: 建立 IoT Edge 模組的的 SKU。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: 49ae69263735d6ec35cd911a20100472b32c0b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286552"
---
# <a name="iot-edge-module-skus-tab"></a>IoT Edge 模組 SKU 索引標籤

可使用 [新增供應項目]**** 頁面的 [SKU]**** 索引標籤，建立一個或一個以上的 SKU，並建立 SKU 與新供應項目之間的關聯性。  可以使用不同的 SKU，依功能集、帳單模式或其他部分特性，讓解決方案彼此互有差異。


## <a name="sku-settings"></a>SKU 設定

開始建立新的供應項目時，沒有任何與該供應項目相關聯的 SKU。 若要建立新的 SKU，請依照下列步驟進行：

- 在 [IoT Edge 模組 > 新增供應項目]**** 頁面中，選取 [SKU]**** 索引標籤。
- 在 SKU 下方，選取 [+新增 SKU]****，即會開啟對話方塊。

  ![IoT Edge 模組的 [新增供應項目] 索引標籤上的 [新 SKU] 按鈕](./media/iot-edge-module-skus-tab-new-sku.png)

- 在 [新 SKU]**** 對話方塊中，輸入 SKU 的識別碼，然後選取 [確定]****。
(下表提供識別碼命名慣例。)

[SKU]**** 索引標籤隨即會重新整理，顯示設定 SKU 的編輯欄位。 欄位名稱上附加的星號 (*) 表示此為必填欄位。

|  **領域**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU 識別碼\***       | 此 SKU 的識別碼。 此名稱的長度上限為 50 個字元，由小寫英數字元或連字號 (-) 組成，但不能以連字號結尾。 **注意：** 發佈供應項目之後，即無法變更此名稱。 該名稱會在產品網址中公開顯示。 |
|  |  |


## <a name="sku-details"></a>SKU 詳細資料

設定 [SKU 詳細資料]****，定義您的 SKU 在 Azure Marketplace 和 Azure 入口網站上的顯示方式。

![IoT Edge 模組 SKU 中繼資料](media/iot-edge-module-skus-tab-metadata.png)

下表說明 [SKU 詳細資料]**** 下的欄位用途、內容和格式。 必要欄位由星號 (*) 標示。

|  **領域**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **標題\***        | 此 SKU 的標題。 長度上限為 50 個字元。 <br/> 會顯示在 Azure 入口網站中，並在部署時做為預設的模組名稱 (不含空格和特殊字元)。 請參閱下圖，以確切了解此欄位的顯示位置。|
| **總結\***      | SKU 的簡短摘要。 長度上限是 100 個字元。 **請勿**提供供應項目的摘要，僅需 SKU 的摘要即可  此摘要會顯示在 Azure Marketplace 中。 請參閱下圖，以確切了解此欄位的顯示位置。|
| **描述\***  | 此 SKU 的簡短說明。長度上限為 3000 個字元。 請勿提供供應項目的說明，僅需 SKU 的說明即可。 說明會顯示在 Azure Marketplace 和 Azure 入口網站中。 在 Azure 門戶中，它將追加到描述應用商店選項卡中定義的產品/服務的市場描述。 它可以與 SKU 摘要相同。 請參閱下圖，以確切了解此欄位的顯示位置。|
| **隱藏此 SKU\*** | 請保留預設設定值，即**否**。 |
|  |  |


### <a name="sku-example"></a>SKU 範例

 下列範例顯示了 SKU 的 [標題]****、[摘要]**** 以及 [說明]**** 欄位會以何種方式顯示在不同的檢視畫面上。
 

#### <a name="on-the-azure-marketplace-website"></a>在 Azure Marketplace 網站上：

- 查看 SKU 詳細資料時：

    ![Azure.com Marketplace 網站中 SKU 的顯示方式](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>在 Azure 入口網站上：

- 瀏覽 SKU 時：

    ![瀏覽 Azure 入口網站時，IoT Edge 模組的顯示方式 #1](media/iot-edge-module-portal-browse.png)

    ![瀏覽 Azure 入口網站時，IoT Edge 模組的顯示方式 #2](media/iot-edge-module-portal-product-picker.png)

- 搜尋 SKU 時：

    ![搜尋 Azure 入口網站時，IoT Edge 模組的顯示方式](media/iot-edge-module-portal-search.png)

- 查看 SKU 詳細資料時：

    ![在入口網站中查看產品詳細資料時，IoT Edge 模組的顯示方式](./media/iot-edge-module-portal-pdp.png)

- 部署模組時：
    
    ![IoT Edge 模組在部署時的顯示方式](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU 內容

在**邊緣模組映射**下，提供上傳 IoT 邊緣模組所需的資訊。

請讓我們得以存取您含有 IoT Edge 模組映像的 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR)，以便我們將之上傳並加以認證。 發佈後，會使用 Azure Marketplace 託管的公共容器登錄來複製和散發您的 IoT Edge 模組。

您可以設定多個平台為目標，並透過標籤提供多個版本。 請至[「準備 IoT Edge 模組的技術資產」一文的標籤和版本設定段落](./cpp-create-technical-assets.md)進一步了解。

![IoT Edge 模組映像](./media/iot-edge-module-skus-tab-acr.png)

下表描述了**部分圖像存儲庫詳細資訊**和**圖像版本**欄位的用途、內容和格式。  必要欄位由星號 (*) 標示。


|  **領域**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***映像存放庫詳細資料***    |
| **訂閱 ID\***        | ACR 的 Azure 訂用帳戶識別碼。|
| **資源組名稱\***      | ACR 的資源群組名稱。|
| **註冊表名稱\***  | 您的 ACR 登錄名稱。 只會複製登錄名稱，而不是登入伺服器名稱 (例如，不含 `azurecr.io`。) |
| **儲存機制名稱\***  | ACR 的存放庫名稱包含您的 IoT Edge 模組。 **注意：** 設定名稱之後，即無法變更。 請使用唯一不重複的名稱，以確保您帳戶中不會有其他相同名稱的供應項目。 |
| **使用者\*** | 與 ACR 相關聯的使用者名稱 (系統管理員使用者名稱)。 |
| **密碼\*** | 與 ACR 相關聯的密碼。 |
|    |  ***圖像版本***   |
| **圖像標記或摘要\*** | 其必須至少包含 `latest` 標籤和版本標籤 (例如，以 `xx.xx.xx-` 開頭，其中 xx 是數字)， 且應是[資訊清單標籤](https://github.com/estesp/manifest-tool)，以將多個平台設為目標。 也請務必新增資訊清單標籤參考的所有標籤，以便上傳。 您可以使用標籤來新增多個版本的 IoT Edge 模組。 所有資訊清單標籤 (除了 `latest` 以外) 必須以 `X.Y-` 或 `X.Y.Z-` 開頭，其中 X、Y、Z 是整數。 請至[「準備 IoT Edge 模組的技術資產」一文的標籤和版本設定段落](./cpp-create-technical-assets.md)進一步了解。 <br/> 例如，如果 `latest` 標籤指向 `1.0.1-linux-x64`、`1.0.1-linux-arm32`，以及 `1.0.1-windows-arm32`，則需要在此處加入這 6 個標籤。 |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>使用預設設置説明客戶啟動

請定義您的 IoT Edge 模組最常用的部署設定。 如此便能讓客戶立即以預設值啟動 IoT Edge 模組，以此方式使客戶的部署達到最佳化。

![部署時的 IoT Edge 模組預設設定](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

下表說明 [預設路由]****、[預設對應項的所需屬性]****、[預設環境變數]****，以及 [預設 CreateOptions]**** 欄位的用途、內容和格式。

|  **領域**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
| **預設路由**        | 每個預設路由名稱和值必須少於 512 個字元。 最多可以定義 5 個預設路由。 請務必在您的路由值中使用正確的[路由語法](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)。 若要參考您的模組，請使用預設模組名稱，該名稱將是您的 **SKU 標題**，不含空格和特殊字元。 若要參考尚為未知的其他模組，請使用 `<FROM_MODULE_NAME>` 慣例，讓客戶知道他們需要更新此資訊。 深入了解 [IoT Edge 路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)。 <br/> 例如，如果模組 `ContosoModule` 接聽 `ContosoInput` 上的輸入和 `ContosoOutput` 的輸出資料，則應該定義以下 2 個預設路由：<br/>- Name #1: `ToContosoModule`<br/>- Value #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Name #2: `FromContosoModuleToCloud`<br/>- Value #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **預設對應項的所需屬性**      | 每個預設對應項的所需屬性名稱和值必須少於 512 個字元。 最多可以定義 5 個名稱/值對應項的所需屬性。 對應項所需屬性的值必須是有效的 JSON、非逸出、無陣列，且包含最多 4 個巢狀階層。 深入了解[對應項的所需屬性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。 <br/> 例如，如果模組透過對應項的所需屬性支援可動態設定的重新整理頻率，請定義以下預設的對應項所需屬性：<br/> - Name #1: `RefreshRate`<br/>- Value #1: `60`|
| **預設環境變數**  | 每個預設環境變數名稱和值必須少於 512 個字元。 最多可以定義 5 個名稱/值的環境變數。 <br/>例如，如果模組在啟動之前需要接受使用條款，您可以定義以下環境變數：<br/> - Name #1: `ACCEPT_EULA`<br/>- Value #1: `Y`|
| **預設 createOptions**  | createOptions 必須少於 512 個字元， 必須是非逸出的有效 JSON。 深入了解 [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules)。 <br/> 例如，如果模組需要繫結連接埠，您可以定義下列 createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

選取 [儲存]**** 以儲存您的 SKU 設定。 


## <a name="next-steps"></a>後續步驟

使用 [Marketplace](./cpp-marketplace-tab.md) 索引標籤建立供應項目的 Marketplace 說明。
