---
title: 參考
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 318b3e3f774a4381434fd56154f4c0d95c28c7a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479510"
---
# <a name="references"></a>參考

本文介紹 Azure 伺服器場節拍 API。

## <a name="rest-api"></a>REST API

Azure FarmBeats API 為農業企業提供具有基於 JSON 的回應的標準化 RESTful 介面，以説明您利用 Azure FarmBeats 功能，例如：

- API 獲取感應器、攝像機、無人機、天氣、衛星和精心策劃的地面資料。
- 跨通用資料提供程式的資料正常化和上下文化。
- 對所有引入的資料進行架構化訪問和查詢功能。
- 基於農藝功能自動生成可查詢的中繼資料。
- 自動生成快速模型構建的時間序列聚合。
- 集成 Azure 資料工廠引擎，輕鬆構建自訂資料處理管道。

## <a name="application-development"></a>應用程式開發

FarmBeats API 包含斯瓦格技術文檔。 有關所有 API 及其相應請求或回應的資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

下表匯總了 FarmBeats Datahub 中的所有物件和資源：

| 物件和資源 | 描述
--- | ---|
伺服器陣列 | 伺服器場對應于 FarmBeats 系統中感興趣的物理位置。 每個伺服器場都有一個伺服器場名稱和一個唯一的伺服器場 ID。 |
裝置  | 設備對應于伺服器場上存在的物理設備。 每個設備都有一個唯一的裝置識別碼。 設備通常預配到具有伺服器場 ID 的伺服器場。
DeviceModel  | DeviceModel 對應于設備的中繼資料，例如製造商和裝置類型（即閘道或節點）。
感應器  | 感應器對應于記錄值的物理感應器。 感應器通常連接到具有裝置識別碼 的設備。
感應器模型  | SensorModel 對應于感應器的中繼資料（如製造商、類比或數位感應器類型）和感應器測量（如環境溫度和壓力）。
遙測  | 遙測提供了讀取特定感應器和時間範圍的遙測消息的能力。
工作 (Job)  | 作業對應于在 FarmBeats 系統中執行的任何活動工作流，以獲得所需的輸出。 每個作業都與作業 ID 和作業類型相關聯。
JobType  | 作業類型對應于系統支援的不同作業類型。 包括系統定義和使用者定義的作業類型。
ExtendedType  | 擴展類型對應于系統中系統和使用者定義的類型清單。 擴展類型有助於在 FarmBeats 系統中設置新的感應器、場景或場景檔案類型。
Partner  | 合作夥伴對應于 FarmBeats 的感應器和影像集成合作夥伴。
場景  | 場景對應于伺服器場上下文中生成的任何輸出。 每個場景都有一個與其關聯的場景 ID、場景源、場景類型和伺服器場 ID。 每個場景 ID 可以有多個與其關聯的場景檔。
場景檔 |場景檔對應于為單個場景生成的所有檔。 單個場景 ID 可以有多個與其關聯的場景檔 ID。
規則  |規則對應于與伺服器場相關的資料以觸發警報的條件。 每個規則都位於伺服器場資料的上下文中。
警示  | 警報對應于通知，該通知在滿足規則條件時生成。 每個警報都位於規則的上下文中。
RoleDefinition  | 角色定義定義角色的允許和不允許的操作。
RoleAssignment  |角色指派對應于角色指派給使用者或服務主體。

### <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用資料格式，它提供了任意資料結構的簡單文本表示形式。 有關詳細資訊，請參閱[JSON 網站](https://www.json.org/)。

## <a name="authentication-and-authorization"></a>驗證和授權

對 REST API 的 HTTP 要求受 Azure 活動目錄 （Azure AD） 的保護。
要向 REST API 發出經過身份驗證的請求，用戶端代碼需要使用有效憑據進行身份驗證，然後才能調用 API。 Azure AD 協調各種參與者之間的身份驗證。 它為用戶端提供訪問權杖作為身份驗證的證明。 然後，在 REST API 請求的 HTTP 授權標頭中發送權杖。 要瞭解有關 Azure AD 身份驗證的更多內容，請參閱開發人員的[Azure 活動目錄](https://portal.azure.com)。

訪問權杖必須在後續 API 請求中（標頭部分）中發送，如：

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 要求標頭

以下是對 Azure FarmBeats Datahub 進行 API 呼叫時必須指定的最常見請求標頭。


**頭** | **描述及範例**
--- | ---
Content-Type  | 請求格式（內容類型：應用程式/<format>）。 對於 Azure 伺服器場資料庫 API，格式為 JSON。 Content-Type: application/json
授權  | 指定進行 API 呼叫所需的訪問權杖。 授權：承載<訪問權杖>
Accept | 回應格式。 對於 Azure 伺服器場資料庫 API，格式為 JSON。 接受：應用程式/json

### <a name="api-requests"></a>API 請求

要發出 REST API 請求，請組合 HTTP（GET、POST、PUT 或 DELETE）方法、API 服務的 URL、要查詢、將資料提交到、更新或刪除的資源的 URI，然後添加一個或多個 HTTP 要求標頭。

API 服務的 URL 是 Datahub URL，例如，HTTPs://\<您的資料中心網站名稱>.azure 網站.net。

或者，您可以在 GET 調用上包括查詢參數，以篩選 、限制 的大小和在回應中對資料進行排序。

以下示例請求用於獲取設備清單：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大多數 GET、POST 和 PUT 呼叫都需要 JSON 請求正文。

以下示例請求創建設備。 此請求具有與請求正文一起輸入 JSON。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>查詢參數

對於 REST GET 調用，您可以通過在請求 URI 上包含一個或多個查詢參數來篩選 、限制 大小和在 API 回應中排序資料。 有關查詢參數，請參閱 API 文檔和各個 GET 調用。
例如，當您查詢設備清單（GET 調用 /Device）時，可以指定以下查詢參數：

![設備清單](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>錯誤處理

Azure FarmBeats Datahub API 返回標準 HTTP 錯誤。 最常見的錯誤代碼如下所示：

 |錯誤碼             | 描述 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 創造（後）成功 |
 |400                    | 不正確的要求。 請求中出錯。 |
 |401                    | 未經授權。 API 的調用方無權訪問資源。 |
 |404                    | 未找到資源 |
 |5XX                    | 內部伺服器錯誤。 以 5XX 開頭的錯誤代碼表示伺服器上存在一些錯誤。 有關詳細資訊，請參閱伺服器日誌和以下部分。 |


除了標準 HTTP 錯誤之外，Azure FarmBeats Datahub API 還以以下格式返回內部錯誤：

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

在此示例中，創建伺服器場時，在輸入負載中未指定強制欄位"Name"。 生成的錯誤訊息是：

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>將使用者或應用註冊添加到 Azure 活動目錄

Azure FarmBeats API 可以由使用者或 Azure 活動目錄中的應用註冊訪問。 要在 Azure 活動目錄中創建應用註冊，請按照以下步驟操作：

1. 轉到[Azure 門戶](https://portal.azure.com)，然後選擇**Azure 活動目錄** > **應用註冊** > **"新註冊**"。 或者，您可以使用現有帳戶。
2. 對於新帳戶，執行以下操作：

    - 輸入名稱。
    - **僅選擇此組織目錄中的帳戶（單個租戶）。**
    - 使用欄位的其餘部分中的預設值。
    - 選取 [註冊]****。

3. 在新和現有應用註冊**概述**窗格中，執行以下操作：

    - 捕獲**用戶端 ID**和**租戶 ID**。
    - 轉到**證書和機密**以生成新的用戶端金鑰並捕獲**用戶端-機密**。
    - 返回**概述**，然後選擇**本地目錄中管理應用程式**旁邊的連結。
    - 轉到**屬性**以捕獲物件**ID**。

4. 轉到[Datahub Swagger，](https://<yourdatahub>.azurewebsites.net/swagger/index.html)執行以下操作：
    - 轉到**角色指派 API**。
    - 執行 POST 為剛剛創建**的物件識別碼**創建**角色指派**物件。

  > [!NOTE]
  > 有關如何添加使用者和活動目錄註冊的詳細資訊，請參閱[Azure 活動目錄](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

完成上述步驟後，應用註冊（用戶端）可以使用通過承載身份驗證的訪問權杖調用 Azure FarmBeats API。

使用訪問權杖在標頭部分中的後續 API 請求中將其發送，如：

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
