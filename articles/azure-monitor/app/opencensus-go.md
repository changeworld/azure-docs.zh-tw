---
title: 使用 Azure Application Insights 進行 OpenCensus Go 追蹤 | Microsoft Docs
description: 提供使用本機轉送工具和 Application Insights 來整合 OpenCensus Go 追蹤的指示
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: ddb8dfbf14f2c75e4d16693076e56a711d8861ad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669976"
---
# <a name="collect-distributed-traces-from-go-preview"></a>從 Go 收集分散式追蹤 (預覽)

透過與 [OpenCensus](https://opencensus.io) 和新型[本機轉送工具](./opencensus-local-forwarder.md)的整合，Application Insights 現在已可支援 Go 應用程式的分散式追蹤。 本文將逐步引導您完成程序，以設定適用於 Go 的 OpenCensus，以及將追蹤資料傳入 Application Insights。

## <a name="prerequisites"></a>必要條件

- 您需要 Azure 訂用帳戶。
- 您應安裝 Go，本文將使用 1.11 版 [Go Download](https://golang.org/dl/)。
- 請依照下列指示安裝[本機轉送工具作為 Windows 服務](./opencensus-local-forwarder.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-application-insights-resource"></a>建立 Application Insights 資源

首先，您必須建立會產生檢測金鑰 (ikey) 的 Application Insights 資源。 接著，ikey 會用來設定本機轉送工具，將分散式追蹤從 OpenCensus 所檢測的應用程式傳送到 Application Insights。   

1. 選取 [建立資源] > [開發人員工具] > [Application Insights]。

   ![新增 Application Insights 資源](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >如果這是您第一次建立 Application Insights 資源，您可以參閱[建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) \(部分機器翻譯\) 一文以深入了解。

   設定方塊隨即出現，請使用下表來填寫輸入欄位。

   | 設定        | 值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 通用唯一值 | 此名稱可識別您要監視的應用程式 |
   | **資源群組**     | myResourceGroup      | 用於裝載 App Insights 資料之新資源群組的名稱 |
   | **位置** | 美國東部 | 選擇您附近或接近應用程式裝載位置的地點 |

2. 按一下 [建立]。

## <a name="configure-local-forwarder"></a>設定本機轉送工具

1. 選取 [概觀] >  **[程式集]** > 複製應用程式的 [檢測金鑰]。

   ![檢測金鑰的螢幕擷取畫面](./media/opencensus-Go/0003-instrumentation-key.png)

2. 編輯 `LocalForwarder.config` 檔案並新增檢測金鑰。 如果您依照[必要條件](./opencensus-local-forwarder.md)中的指示操作，該檔案會位於 `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. 重新啟動應用程式的**本機轉送工具**服務。

## <a name="opencensus-go-packages"></a>OpenCensus Go 套件

1. 從命令列安裝適用於 Go 的 OpenCensus 套件：

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. 將下列程式碼新增至 .go 檔案，然後建置並執行。 (此範例衍生自已新增程式碼以利與本機轉送工具整合的官方 OpenCensus 指引)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. 一旦簡易 Go 應用程式執行後，請瀏覽至 `http://localhost:50030`。 每次重新整理瀏覽器都將會產生文字 "hello world"，並伴隨著由本機轉送工具挑選的對應範圍資料。

4. 若要確認**本機轉送工具**會挑出追蹤，請檢查 `LocalForwarder.config` 檔案。 如果您遵循了[必要條件](https://docs.microsoft.com/azure/application-insights/local-forwarder)中的步驟，該檔案會位於 `C:\LF-WindowsServiceHost`。

    在下列記錄檔影像中，您可以看到在執行第二個指令碼 (我們在此新增了匯出工具) 之前，`OpenCensus input BatchesReceived` 為 0。 一旦我們開始執行更新後的指令碼，遞增的 `BatchesReceived` 就等於我們所輸入數量的值：
    
    ![新增 App Insights 資源表單](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中開始監視

1. 現在，您可以在 Azure 入口網站中重新開啟 Application Insights 的 [概觀] 頁面，以檢視目前執行中應用程式的詳細資料。 選取 [即時計量串流]。

   ![紅色方塊中選取了 [即時計量串流] 的 [概觀] 窗格螢幕擷取畫面](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. 如果您再次執行第二個 Go 應用程式並開始重新整理 `http://localhost:50030` 的瀏覽器，則當即時追蹤資料從本機轉送工具服務抵達 Application Insights 時，您便會看到該資料。

   ![顯示了效能資料的即時計量串流螢幕擷取畫面](./media/opencensus-go/0006-stream.png)

3. 瀏覽回到 [概觀] 頁面，然後選取 [應用程式對應]，以顯示應用程式元件之間相依性關係和呼叫計時的視覺化配置。

    ![基本應用程式對應的螢幕擷取畫面](./media/opencensus-go/0007-application-map.png)

    因為我們只追蹤一個方法呼叫，所以應用程式對應並不有趣。 但是，應用程式對應經過調整後，即可顯示更多的分散式應用程式：

   ![應用程式對應](media/opencensus-go/application-map.png)

4. 選取 [調查效能] 以執行詳細的效能分析，並判斷效能緩慢的根本原因。

    ![效能窗格的螢幕擷取畫面](./media/opencensus-go/0008-performance.png)

5. 選取 [範例]，然後按一下出現在右窗格中的任何範例，就會啟動端對端交易詳細資料體驗。 儘管應用程式範例只會向我們顯示單一事件，但更複雜的應用程式將可讓您瀏覽到個別事件呼叫堆疊層級的端對端交易。

     ![端對端交易介面的螢幕擷取畫面](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>適用於 Go 的 OpenCensus 追蹤

我們只說明了使用本機轉送工具和 Application Insights 來整合「適用於 Go 的 OpenCensus」的基本概念。 [官方 OpenCensus Go 使用指引](https://godoc.org/go.opencensus.io)會說明更進階的主題。

## <a name="next-steps"></a>後續步驟

* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)
