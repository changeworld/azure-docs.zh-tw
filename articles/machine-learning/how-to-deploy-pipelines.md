---
title: 發佈 ML 管線
titleSuffix: Azure Machine Learning
description: 使用機器學習管線和適用于 Python 的 Azure Machine Learning SDK 來執行機器學習工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: 9eb07721f9d507d69de10b6de6c8c2a358dab020
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034083"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>發佈和追蹤機器學習管線



本文將說明如何與您的同事或客戶共用機器學習管線。

機器學習管線是機器學習工作的可重複使用工作流程。 管線的其中一個優點是會增加共同作業。 您也可以在使用新版本時，讓客戶使用目前的模型，來為管線進行版本處理。 

## <a name="prerequisites"></a>必要條件

* 建立 [Azure Machine Learning 工作區](how-to-manage-workspace.md) 以保存您的所有管線資源

* [設定您的開發環境](how-to-configure-environment.md)以安裝 Azure Machine Learning sdk，或使用已安裝 SDK 的[Azure Machine Learning 計算實例](concept-compute-instance.md)

* 建立並執行機器學習管線，例如依照下列教學課程 [：建立批次計分的 Azure Machine Learning 管線](tutorial-pipeline-batch-scoring-classification.md)。 如需其他選項，請參閱 [使用 AZURE MACHINE LEARNING SDK 來建立及執行機器學習管線](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>發佈管線

當管線啟動並執行之後，您就可以發佈管線，使其以不同的輸入執行。 針對已發佈之管線的 REST 端點接受參數，您必須設定管線以 `PipelineParameter` 針對將會改變的引數使用物件。

1. 若要建立管線參數，請使用 [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?preserve-view=true&view=azure-ml-py) 物件搭配預設值。

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. 將這個 `PipelineParameter` 物件以參數形式新增至管線中的任何步驟，如下所示：

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. 發佈這個將在被叫用時接受參數的管線。

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>執行已發佈的管線

所有已發佈的管線都有 REST 端點。 使用管線端點時，您可以從任何外部系統（包括非 Python 用戶端）觸發管線的執行。 此端點可在批次評分和重新訓練案例中，提供「受控的可重複性」。

> [!IMPORTANT]
> 如果您使用 Azure 角色型存取控制 (Azure RBAC) 來管理管線的存取權，請 [設定管線案例的許可權 (定型或計分) ](how-to-assign-roles.md#common-scenarios)。

若要叫用上述管線的執行，您需要 Azure Active Directory authentication 標頭權杖。 取得這類權杖的說明位於 [>azurecliauthentication 類別](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?preserve-view=true&view=azure-ml-py) 參考和 Azure Machine Learning 筆記本中的 [驗證](https://aka.ms/pl-restep-auth) 。

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

`json`POST 要求的引數必須包含索引 `ParameterAssignments` 鍵的字典，其中包含管線參數和其值。 此外， `json` 引數可能包含下列索引鍵：

| Key | 描述 |
| --- | --- | 
| `ExperimentName` | 與此端點相關聯的實驗名稱 |
| `Description` | 描述端點的自由格式文字 | 
| `Tags` | 可以用來標記和標注要求的自由格式索引鍵/值組  |
| `DataSetDefinitionValueAssignments` | 用於變更資料集而不需要重新定型的字典 (請參閱下面的討論)  | 
| `DataPathAssignments` | 用於變更 datapaths 而不需要重新定型的字典 (請參閱以下討論)  | 

### <a name="run-a-published-pipeline-using-c"></a>使用 C 執行已發佈的管線# 

下列程式碼示範如何以非同步方式從 c # 呼叫管線。 部分程式碼片段只會顯示呼叫結構，而不是 Microsoft 範例的一部分。 它不會顯示完整的類別或錯誤處理。 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>使用 JAVA 執行已發佈的管線

下列程式碼顯示需要驗證之管線的呼叫 (請參閱 [設定 Azure Machine Learning 資源和工作流程](how-to-setup-authentication.md)) 的驗證。 如果您的管線是公開部署的，您就不需要產生的呼叫 `authKey` 。 部分程式碼片段不會顯示 JAVA 類別和例外狀況處理的重複套用。 程式碼會使用將可能會傳回空白的函式 `Optional.flatMap` 連結在一起 `Optional` 。 使用 `flatMap` 可縮短並清楚說明程式碼，但請注意， `getRequestBody()` 抑制例外狀況。

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>在不重新訓練的情況下變更資料集和 datapaths

您可能想要在不同的資料集和 datapaths 上進行定型和推斷。 例如，您可能想要針對較小的資料集進行定型，但在整個資料集上進行推斷。 您可以使用 `DataSetDefinitionValueAssignments` 要求的引數中的索引鍵來切換資料集 `json` 。 您可以使用切換 datapaths `DataPathAssignments` 。 兩者的技巧很類似：

1. 在您的管線定義腳本中， `PipelineParameter` 為資料集建立。 `DatasetConsumptionConfig` `DataPath` 從下列建立或 `PipelineParameter` ：

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. 在您的 ML 腳本中，使用下列方式存取動態指定的資料集 `Run.get_context().input_datasets` ：

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    請注意，ML 腳本會存取針對 () 指定的值 `DatasetConsumptionConfig` `tabular_dataset` ，而非 `PipelineParameter` () 的值 `tabular_ds_param` 。

1. 在您的管線定義腳本中，將設定 `DatasetConsumptionConfig` 為的參數 `PipelineScriptStep` ：

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. 若要在推斷 REST 呼叫中動態切換資料集，請使用 `DataSetDefinitionValueAssignments` ：
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

筆記本 [展示資料集和 >pipelineparameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) 和 [展示資料路徑和 >pipelineparameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) 都有這項技術的完整範例。

## <a name="create-a-versioned-pipeline-endpoint"></a>建立已建立版本的管線端點

您可以建立管線端點，其中包含多個已發佈的管線。 當您逐一查看和更新 ML 管線時，這項技術可提供您固定的 REST 端點。

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>將作業提交至管線端點

您可以將作業提交至管線端點的預設版本：

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

您也可以將作業提交至特定版本：

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

使用 REST API 可以完成相同動作：

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>在 studio 中使用已發佈的管線

您也可以從 studio 執行已發佈的管線：

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. [查看您的工作區](how-to-manage-workspace.md#view)。

1. 選取左側的 [ **端點**]。

1. 在頂端，選取 **管線端點**。
 ![機器學習已發佈管線的清單](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. 選取特定管線以執行、取用或查看管線端點先前執行的結果。

## <a name="disable-a-published-pipeline"></a>停用已發佈的管線

若要從已發佈的管線清單中隱藏管線，請在 studio 或從 SDK 中停用管線：

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

您可以使用重新啟用它 `p.enable()` 。 如需詳細資訊，請參閱 [PublishedPipeline 類別](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?preserve-view=true&view=azure-ml-py) 參考。

## <a name="next-steps"></a>後續步驟

- 使用 [GitHub 上的這些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 來進一步探索機器學習管線。
- 請參閱 [azureml-管線-核心](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) 封裝和 [azureml-管線-步驟](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) 套件的 SDK 參考說明。
- 請參閱 [如何](how-to-debug-pipelines.md) 取得對管線進行偵錯工具和疑難排解的秘訣。