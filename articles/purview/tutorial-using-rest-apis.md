---
title: 教學課程：使用 REST API
description: 本教學課程說明如何使用 Azure Purview REST API 來存取目錄的內容。
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951238"
---
# <a name="tutorial-use-the-rest-apis"></a>教學課程：使用 REST API

在本教學課程中，您將了解如何使用 Azure Purview REST API。 任何人若想要將資料提交至 Azure Purview 目錄、將目錄納入為自動化程序的一部分，或在目錄上建置其本身的使用者體驗，都可以使用 REST API 來完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立服務主體 (應用程式)。
> * 將您的目錄設定為信任服務主體 (應用程式)。
> * 檢視 REST API 文件。
> * 收集使用 REST API 所需的值。
> * 使用 Postman 用戶端來呼叫 REST API。
> * 產生 .NET 用戶端以呼叫 REST API。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="prerequisites"></a>必要條件

* 若要開始使用，您必須擁有現有的 Azure Purview 帳戶。 如果您沒有目錄，請參閱[建立 Azure Purview 帳戶的快速入門](create-catalog-portal.md)。

* 如果您需要將資料新增至目錄，請參閱[執行入門套件和掃描資料的教學課程](tutorial-scan-data.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-service-principal-application"></a>建立服務主體 (應用程式)

若要讓 REST API 用戶端存取目錄，用戶端必須具有服務主體 (應用程式)，以及目錄可辨識且已設定為信任的身分識別。 當您對目錄進行 REST API 呼叫時，將會使用服務主體的身分識別。

已使用現有服務主體 (應用程式識別碼) 的客戶會有很高的失敗率。 因此，我們建議建立新的服務主體來呼叫 API。

若要建立新的服務主體：

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [Azure Active Directory]。
1. 在 [Azure Active Directory] 頁面中，從左窗格中選取 [應用程式註冊]。
1. 選取 [新增註冊]。
1. 在 [註冊應用程式] 頁面上：
    1. 輸入應用程式的 **名稱** (服務主體名稱)。
    1. 選取 [僅限此組織目錄中的帳戶] **_(僅限&lt;&gt;您的租用戶名稱_** - 單一租用戶)。
    1. 針對 [重新導向 URI (選擇性)] 選取 [Web]，然後輸入一個值。 此值不需要是有效的 URI。
    1. 選取 [註冊]。
1. 在 [新增服務主體] 頁面上，複製 [顯示名稱] 和 [應用程式 (用戶端) 識別碼] 的值，以儲存供後續使用。

   應用程式識別碼是範例程式碼中的 `client_id` 值。

若要使用服務主體 (應用程式)，您必須取得其密碼。 其做法如下：

1. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]，然後從左窗格中選取 [應用程式註冊]。
1. 從清單中選取您的服務主體 (應用程式)。
1. 從左窗格中選取 [憑證和秘密]。
1. 選取 [新增用戶端密碼]。
1. 在 [新增用戶端密碼] 頁面上輸入 [說明]，在 [到期] 底下選取到期時間，然後選取 [新增]。

   在 [用戶端密碼] 頁面上，新秘密的 [值] 資料行中的字串就是您的密碼。 儲存這個值。

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="顯示用戶端密碼的螢幕擷取畫面。":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>將您的目錄設定為信任服務主體 (應用程式)

若要將 Azure Purview 設定為信任新的服務主體：

1. 瀏覽至您的 Purview 帳戶

1. 在 [Purview 帳戶] 頁面上，選取 [存取控制 (IAM)] 索引標籤

1. 按一下 [+ 新增]

1. 選取 [新增角色指派]

1. 針對 [角色]，輸入 **Purview 資料編者**

    > [!Note]
    > 如需 Azure Purview 資料目錄權限的詳細資訊，請參閱[目錄權限](catalog-permissions.md)。 例如，如果您需要觸發掃描的權限，則角色類型必須是 [Purview 資料來源管理員]。

1. 保留 [存取權指派對象] 中的預設 **使用者、群組或服務主體**

1. 針對 [ **選取** ]，輸入您想要指派之先前已建立服務主體的名稱，然後在 [結果] 窗格中按一下其名稱。

1. 按一下 [儲存] 

您現在已將服務主體設定為應用程式系統管理員，而使其能夠將內容傳送至目錄。

## <a name="view-the-rest-apis-documentation"></a>檢視 REST API 文件

若要檢視 API Swagger 文件，請下載 [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip) 並將其檔案解壓縮，然後開啟 index.html。

如果您想要深入了解 Azure Purview 提供的進階搜尋/建議 API，請參閱 Rest API 搜尋篩選文件。 AutoRest 產生的用戶端目前不支援自訂的搜尋參數。 若要解決此問題，請依照搜尋篩選文件的指示，將程式碼中的篩選類別定義為 API 呼叫參數。 index.html 文件包含這些 API 的範例。

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>收集使用 REST API 所需的值

尋找並儲存下列值：

* 租用戶識別碼：
  * 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [Azure Active Directory]。
  * 在左窗格的 [管理] 區段中，選取 [屬性]，尋找 [租用戶識別碼]，然後選取 [複製到剪貼簿] 圖示以儲存其值。
* Atlas 端點：
  * 在 Azure 入口網站的 [Azure Purview 帳戶頁面](https://aka.ms/purviewportal)中，從清單中尋找並選取您的 Azure Purview 帳戶。
  * 選取 [屬性]，尋找 [Atlas 端點]，然後選取 [複製到剪貼簿] 圖示以儲存其值。 在後續使用時，請移除字串的 *https://* 部分。
* 帳戶名稱：
  * 從 Atlas 端點字串中擷取您的目錄名稱。 例如，如果您的 Atlas 端點是 `https://ThisIsMyCatalog.catalog.purview.azure.com`，則帳戶名稱為 `ThisIsMyCatalog`。

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>使用 Postman 用戶端來呼叫 REST API

1. 安裝 [Postman 用戶端](https://www.getpostman.com/)。
1. 在用戶端中選取 [匯入]，然後使用 [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json)。
1. 選取 [集合]，然後選取 [測試]。
1. 選取 [取得權杖]：
    1. 在 POST 旁的 URL 中，將 *&lt;your-tenant-id&gt;* 取代為您在上一節中複製的租用戶識別碼。
    1. 選取 [本文] 索引標籤，並取代上一個步驟的路徑和本文中的預留位置。

       在您選取 [傳送] 後，回應本文會具有 JSON 結構，其中包含名稱 *access_token* 和加上引號的字串值。 
    1. 複製持有人權杖值 (不含引號)，以在下一個步驟中使用。

1. 選取 **/v2/types/typedefs**：
    1. 將路徑中的預留位置取代為您的 atlas 端點值。 在 Ibiza 入口網站上瀏覽至目錄執行個體，然後按一下 [概觀]，即可取得此值。 

       持有人權杖是在第一個步驟中設定的 (也可以手動在 [授權] 索引標籤中複製)。

    1. 選取 [傳送] 以取得回應。

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>產生 .NET 用戶端以呼叫 REST API

### <a name="install-autorest"></a>安裝 AutoRest



1. [安裝 Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md)。
1. 開啟 PowerShell 並執行下列命令：

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>下載 rest-api-specs.zip 並建立用戶端

1. 下載 [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip)，並將其檔案解壓縮。
1. 在 PowerShell 中，從解壓縮 rest-api-specs 的資料夾執行下列命令：

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   此程序的輸出會在 rest-api-specs 資料夾中建立資料夾 PurviewCatalogClient 和子資料夾 CSharp。

### <a name="create-a-sample-net-console-application"></a>建立範例 .NET 主控台應用程式

1. 開啟 Visual Studio 2019。 這些指示已經過免費 Community 版本的測試。
1. 從 [建立新專案] 頁面上，以 C# 建立 **主控台應用程式 (.NET Core)** 專案。
1. 複製並貼上提供的[範例程式碼](#sample-code-for-the-console-application)。
1. 將 *accountName*、*servicePrincipalId*、*servicePrincipalKey* 和 *tenantId* 您先前收集的值。
1. 使用 **方案總管**，在 Visual Studio 專案中新增名為 **Generated** 的資料夾。
1. 將您先前建立的 rest-api-specs\PurviewCatalogClient\CSharp 資料夾複製到 \Generated 資料夾。 使用檔案總管或命令列提示字元進行複製作業，這會使 Visual Studio 自動將檔案新增至專案。
1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]。
1. 選取 [瀏覽] 索引標籤。尋找並選取 **Microsoft.Rest.ClientRuntime**。
1. 確定版本至少為 2.3.21，然後選取 [安裝]。
1. 建置並執行應用程式。

範例程式碼會傳回目錄中的 typedef 計數，並顯示如何處理角色指派。 如需詳細資訊，請參閱範例程式碼中的 `DoRoleAssignmentOperations()`。 如需專案的詳細資訊，請參閱[專案設定](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md)。

### <a name="sample-code-for-the-console-application"></a>主控台應用程式的範例程式碼

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [管理資料來源](manage-data-sources.md)
