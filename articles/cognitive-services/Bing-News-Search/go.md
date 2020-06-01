---
title: 快速入門：使用 Bing 新聞搜尋 REST API 和 Go 來取得新聞
titleSuffix: Azure Cognitive Services
description: 本快速入門使用 Go 語言來呼叫 Bing 新聞搜尋 API。 搜尋結果中會包含查詢字串所識別新聞來源的名稱和 URL。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.openlocfilehash: e18605b75e4fcfcd8f2793e06801c309f9f23965
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869265"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>快速入門：使用 Bing 新聞搜尋 REST API 和 Go 來取得新聞搜尋結果

本快速入門使用 Go 語言來呼叫 Bing 新聞搜尋 API。 搜尋結果中會包含查詢字串所識別新聞來源的名稱和 URL。

## <a name="prerequisites"></a>Prerequisites
* 安裝 [Go 二進位檔](https://golang.org/dl/)。
* 安裝 go-spew 程式庫以使用深度美化印表機顯示結果。 使用此命令來安裝程式庫︰`$ go get -u https://github.com/davecgh/go-spew`。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-a-project-and-import-libraries"></a>建立專案並匯入程式庫

在 IDE 或編輯器中建立新的 Go 專案。 然後，匯入 `net/http` 以提出要求、`ioutil` 以讀取回應，以及 `encoding/json` 以處理結果的 JSON 文字，還有 `go-spew` 程式庫以剖析 JSON 結果。 

```go
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>建立結構以將新聞搜尋結果格式化

`NewsAnswer` 結構會將回應 JSON 中提供的資料格式化，這牽涉到多層層且相當複雜。 下列實作會涵蓋基本概念：

```go
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            } `json: "image"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>宣告 main 函式並定義變數  

下列程式碼會宣告主要函式，並指派必要的變數。 請確認端點正確，並將 `token` 值換成您的 Azure 帳戶中有效的訂用帳戶金鑰。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

```go
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>查詢和標頭

新增查詢字串和存取金鑰標題。

```go
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>GET 要求

建立用戶端並傳送 GET 要求。 

```go
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>傳送要求

使用 `ioutil` 傳送要求並讀取回應。

```go
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>處理回應

`Unmarshall` 函式會從 Bing 新聞搜尋 API 所傳回的 JSON 文字中擷取資訊。 然後，使用 `go-spew` 美化印表機顯示結果中的節點。

```go
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>結果

下列輸出包含每個結果的名稱和 URL：

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [什麼是 Bing 新聞搜尋](search-the-web.md)
