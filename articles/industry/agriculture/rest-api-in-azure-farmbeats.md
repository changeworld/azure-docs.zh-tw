---
title: Azure FarmBeats Api
description: 深入瞭解 Azure FarmBeats Api，其為農業企業提供具有以 JSON 為基礎之回應的標準化 RESTful 介面。
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: f15bee7e802b04d04a3c87d7f84fc975b88bf260
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86536567"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats Api

本文說明 Azure FarmBeats Api。 Azure FarmBeats Api 為農業企業提供具有以 JSON 為基礎之回應的標準化 RESTful 介面，以協助您利用 Azure FarmBeats 功能，例如：

- 取得感應器、攝影機、無人機、氣象、衛星和策劃地面資料的 Api。
- 跨一般資料提供者的資料正規化和對脈絡化。
- 架構化所有內嵌資料的存取和查詢功能。
- 自動產生可根據 agronomic 功能來查詢的中繼資料。
- 自動產生的時間序列匯總，可快速建立模型。
- 整合式 Azure Data Factory 引擎，可輕鬆地建立自訂的資料處理管線。

## <a name="application-development"></a>XBOX Video Application Development

FarmBeats Api 包含 Swagger 技術檔。 如需所有 Api 及其對應要求或回應的詳細資訊，請參閱 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

下表摘要說明 FarmBeats Datahub 中的所有物件和資源：

| 物件和資源 | 說明
--- | ---|
Farm | 伺服器陣列對應于 FarmBeats 系統內的實體位置。 每個伺服器陣列都有伺服器陣列名稱和唯一的伺服器陣列識別碼。 |
裝置  | 裝置會對應至存在於伺服器陣列上的實體裝置。 每個裝置都有唯一的裝置識別碼。 裝置通常會布建到具有伺服器陣列識別碼的伺服器陣列。
DeviceModel  | DeviceModel 會對應至裝置的中繼資料，例如製造商和裝置類型，也就是閘道或節點。
Sensor  | 感應器對應于記錄值的實體感應器。 感應器通常會使用裝置識別碼連線到裝置。
SensorModel  | SensorModel 會對應至感應器的中繼資料，例如製造商、感應器類型（類比或數位）和感應器測量，例如環境溫度和壓力。
遙測  | 遙測能讓您讀取特定感應器和時間範圍的遙測訊息。
工作 (Job)  | 作業對應于 FarmBeats 系統中執行的任何活動工作流程，以取得所需的輸出。 每項作業都與作業識別碼和作業類型相關聯。
JobType  | JobType 對應至系統所支援的不同作業類型。 包含系統定義和使用者定義的作業類型。
ExtendedType  | ExtendedType 會對應至系統中的系統和使用者定義類型清單。 ExtendedType 可協助在 FarmBeats 系統中設定新的感應器、場景或場景檔案類型。
Partner  | 夥伴對應于 FarmBeats 的感應器和影像整合合作夥伴。
場景  | 場景對應到伺服器陣列內容中任何產生的輸出。 每個場景都有與其相關聯的場景識別碼、場景來源、場景類型和伺服器陣列識別碼。 每個場景識別碼都可以有多個相關聯的場景檔案。
SceneFile |SceneFile 會對應至為單一場景產生的所有檔案。 單一場景識別碼可以有多個相關聯的 SceneFile 識別碼。
規則  |規則對應到伺服器陣列相關資料的條件，以觸發警示。 每個規則都在伺服器陣列資料的內容中。
警示  | 警示對應于符合規則條件時所產生的通知。 每個警示都在規則的內容中。
RoleDefinition  | RoleDefinition 會為角色定義允許和不允許的動作。
RoleAssignment  |RoleAssignment 對應至使用者或服務主體的角色指派。

### <a name="data-format"></a>資料格式

JSON 是一種與語言無關的通用資料格式，可提供任意資料結構的簡單文字表示。 如需詳細資訊，請參閱 [JSON 網站](https://www.json.org/)。

## <a name="authentication-and-authorization"></a>驗證與授權

REST API 的 HTTP 要求會以 Azure Active Directory (Azure AD) 來保護。
若要對 REST Api 提出已驗證的要求，用戶端程式代碼需要使用有效的認證進行驗證，您才能呼叫 API。 Azure AD 的各動作專案之間會協調驗證。 它會為您的用戶端提供存取權杖作為驗證證明。 然後，權杖會在 REST API 要求的 HTTP 授權標頭中傳送。 若要深入瞭解 Azure AD authentication，請參閱適用于開發人員的 [Azure Active Directory](https://portal.azure.com) 。

存取權杖必須在標頭區段中的後續 API 要求中傳送，如下所示：

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 要求標頭

以下是當您對 Azure FarmBeats Datahub 進行 API 呼叫時，您必須指定的最常見要求標頭。


**標頭** | **描述及範例**
--- | ---
Content-Type  | 要求格式 (Content-Type: application/<format>)。 針對 Azure FarmBeats Datahub Api，格式為 JSON。 Content-Type: application/json
授權  | 指定進行 API 呼叫所需的存取權杖。 授權：Bearer <Access-Token>
Accept | 回應格式。 針對 Azure FarmBeats Datahub Api，格式為 JSON。 Accept: application/json

### <a name="api-requests"></a>API 要求

若要提出 REST API 要求，請結合 HTTP (GET、POST、PUT 或 DELETE) 方法、API 服務的 URL、要查詢之資源的 URI、提交資料至、更新或刪除，然後新增一或多個 HTTP 要求標頭。

API 服務的 URL 是您的 Datahub URL，例如 HTTPs:// \<yourdatahub-website-name> . azurewebsites.net。

您可選擇性地在 GET 呼叫上包含查詢參數來篩選回應中的資料、限制其大小及進行排序。

下列範例要求會用來取得裝置清單：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大多數 GET、POST 和 PUT 呼叫都需要 JSON 要求本文。

下列範例要求會建立裝置。 此要求的輸入 JSON 具有要求主體。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>查詢參數

若是 REST GET 呼叫，您可以在要求 URI 上包含一或多個查詢參數，以篩選、限制和排序 API 回應中的資料。 如需查詢參數，請參閱 API 檔和個別的 GET 呼叫。
例如，當您查詢裝置清單時 (在/Device) 上取得呼叫時，可以指定下列查詢參數：

![裝置清單](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>錯誤處理

Azure FarmBeats Datahub Api 會傳回標準 HTTP 錯誤。 最常見的錯誤碼如下：

 |錯誤碼             | 描述 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 建立 (Post) 成功 |
 |400                    | 不正確的要求。 要求中發生錯誤。 |
 |401                    | 未經授權。 API 的呼叫端沒有存取資源的授權。 |
 |404                    | 找不到資源 |
 |5XX                    | 內部伺服器錯誤。 從5XX 開始的錯誤碼表示伺服器上有一些錯誤。 如需詳細資訊，請參閱伺服器記錄檔和下一節。 |


除了標準 HTTP 錯誤，Azure FarmBeats Datahub Api 也會以下列格式傳回內部錯誤：

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

在此範例中，當建立伺服器陣列時，輸入承載中未指定強制欄位 "Name"。 產生的錯誤訊息會是：

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>將使用者或應用程式註冊新增至 Azure Active Directory

Azure Active Directory 中的使用者或應用程式註冊可以存取 Azure FarmBeats Api。 若要在 Azure Active Directory 中建立應用程式註冊，請遵循下列步驟：

1. 移至[Azure 入口網站](https://portal.azure.com)，然後選取**Azure Active Directory**  >  **應用程式註冊**  >  **新註冊**。 或者，您可以使用現有的帳戶。
2. 針對新帳戶，請執行下列動作：

    - 輸入名稱。
    - 選取 **此組織目錄中的帳戶，只 (單一租使用者) **。
    - 在其餘欄位中使用預設值。
    - 選取 [註冊]。

3. 在 [新的和現有的應用程式註冊 **總覽** ] 窗格中，執行下列動作：

    - 捕獲 **用戶端識別碼** 和 **租使用者識別碼**。
    - 移至 [ **憑證及秘密** ] 以產生新的用戶端密碼，並捕獲 **用戶端密碼**。
    - 返回 **總覽**，然後選取 [ **管理本機目錄中的應用程式**] 旁的連結。
    - 移至 [ **屬性** ] 以抓取 **物件識別碼**。

4. 移至您的 Datahub Swagger (HTTPs:// <yourdatahub> . azurewebsites.net/swagger/index.html) ，然後執行下列動作：
    - 移至 **ROLEASSIGNMENT API**。
    - 針對您剛才建立的**物件識別碼**，執行 POST 來建立**RoleAssignment**物件。
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > 如需如何新增使用者和 Active Directory 註冊的詳細資訊，請參閱 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

完成先前的步驟之後，您的應用程式註冊 (用戶端) 可以透過持有人驗證使用存取權杖來呼叫 Azure FarmBeats Api。

使用存取權杖將標頭區段中後續的 API 要求傳送給它，如下所示：

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
