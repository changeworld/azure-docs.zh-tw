---
title: 公用和主權區域之間的同位檢查
titleSuffix: Azure Machine Learning
description: 某些 Azure Machine Learning 的功能（例如公用預覽功能）只能在公用雲端區域中使用。 本文列出 Azure Government、Azure 德國和 Azure 中國的世紀區域中也可以使用的功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2020
ms.custom: references_regions
ms.openlocfilehash: 4a6f09fdff82b8e86c7fe75018c5267dba3c1b4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892974"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning 主權雲端同位

瞭解主權雲端區域中有哪些 Azure Machine Learning 功能。 

在全球 Azure 區域清單中，有數個「主權」區域可提供特定市場。 例如，Azure Government 和 Azure 中國的世紀地區。 目前 Azure Machine Learning 會部署至下列主權雲端區域：

* Azure Government 區域 **美國-亞利桑那州** 和 **美國-弗吉尼亞州**。
* Azure 中國世紀地區 **中國-中東-2**。

> [!TIP]
> 為了區分主權與非主權區域，本文將使用「 __公用雲端__ 」一詞來參考非主權區域。

我們的目標是要在我們的公用雲端與主權區域之間提供最大的同位檢查。 這些區域中的所有 Azure Machine Learning 功能均可在 GA (正式運作) 的公開雲端中 **的30天** 內，于這些區域中使用。 我們也會在這些區域中啟用選取的預覽功能。 以下顯示我們的主權與公用雲端之間的目前同位差異。

## <a name="azure-government"></a>Azure Government 

| 功能 | 公用雲端狀態  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **自動化機器學習** | | | |
| 在筆記本中建立及執行實驗                                    | GA                   | YES                | YES         |
| 在 studio web 體驗中建立和執行實驗                        | 公開預覽       | YES                | YES         |
| 領先業界的預測功能                                  | GA                   | YES                | YES         |
| 深度學習和其他先進學習的支援                      | GA                   | YES                | YES         |
|  (高達 100 GB) 的大型資料支援                                          | 公開預覽       | YES                | YES         |
| Azure Databricks 整合                                              | GA                   | 否                 | 否          |
| SQL、CosmosDB 和 HDInsight 整合                                   | GA                   | YES                | YES         |
| **Machine Learning 管線** |   |  | | 
| 使用 Azure ML SDK 建立、執行及發佈管線                   | GA                   | YES                | YES         |
| 使用 Azure ML SDK 建立管線端點                           | GA                   | YES                | YES         |
| 使用 Azure ML SDK 建立、編輯和刪除管線的排程執行 | GA                   | 好               | 好        |
| 在 studio 中查看管線執行詳細資料                                        | GA                   | YES                | YES         |
| 在 Azure ML 設計工具中建立、執行、視覺化及發佈管線          | GA      | YES                | YES         |
| Azure Databricks 與 ML 管線整合                             | GA                   | 否                 | 否          |
| 在 Azure ML 設計工具中建立管線端點                             | GA      | YES                | YES         |
| **整合式筆記本** |   |  | | 
| 工作區筆記本和檔案共用                                        | GA                   | YES                | YES         |
| R 和 Python 支援                                                       | GA                   | YES                | YES         |
| 虛擬網路支援                                                    | 公開預覽       | 否                 | 否          |
| **計算執行個體** |   |  | | 
| 整合式筆記本的受控計算實例                         | GA                   | YES                | YES         |
| Jupyter，JupyterLab 整合                                            | GA                   | YES                | YES         |
| 虛擬網路 (VNet) 支援                                             | 公開預覽       | YES                | YES         |
| **SDK 支援** |  |  | | 
| R SDK 支援                                                              | 公開預覽       | YES                | YES         |
| Python SDK 支援                                                         | GA                   | YES                | YES         |
| **安全性** |   | | | 
| 虛擬網路 (VNet) 支援定型                                | GA                   | YES                | YES         |
| 虛擬網路 (VNet) 支援推斷                               | GA                   | YES                | YES         |
| 評分端點驗證                                            | 公開預覽       | YES                | YES         |
| Workplace Private link                                                     | 公開預覽       | 否                 | 否          |
| VNet 背後的 ACI                                                            | 公開預覽       | 否                 | 否          |
| VNet 背後的 ACR                                                            | 公開預覽       | 否                 | 否          |
| AKS 叢集的私人 IP                                                  | 公開預覽       | 否                 | 否          |
| **計算** |   | | |
| 跨工作區的配額管理                                         | GA                   | YES                | YES         |
| **機器學習服務的資料** |   | | |
| 從 SDK 建立、查看或編輯資料集和資料存放區                  | GA                   | YES                | YES         |
| 從 UI 建立、查看或編輯資料集和資料存放區                   | GA                   | YES                | YES         |
| 從 SDK 查看、編輯或刪除資料集漂移監視器                   | 公開預覽       | YES                | YES         |
| 從 UI 中查看、編輯或刪除資料集漂移監視器                    | 公開預覽       | YES                | YES         |
| **機器學習服務生命週期** |   | | |
| 模型分析                                                            | GA                   | YES                | PARTIAL     |
| Machine Learning 的 Azure DevOps 擴充功能 & Azure ML CLI         | GA                   | YES                | YES         |
| 以 FPGA 為基礎的硬體加速模型                                     | GA                   | 否                 | 否          |
| Visual Studio Code 整合                                             | 公開預覽       | 否                 | 否          |
| 事件方格整合                                                     | 公開預覽       | 否                 | 否          |
| 整合 Azure 串流分析與 Azure Machine Learning               | 公開預覽       | 否                 | 否          |
| **標記** |   | | |
| 標記專案管理入口網站                                        | GA                   | YES                | YES         |
| 標籤人員入口網站                                                            | GA                   | YES                | YES         |
| 使用私用員工標記                                          | GA                   | YES                | YES         |
| ML 輔助標記 (影像分類和物件偵測)            | 公開預覽       | YES                | YES         |
| **負責任的 ML** |   | | |
| UI 中的可解釋性                                                       | 公開預覽       | 否                 | 否          |
| 差異隱私權 WhiteNoise 工具組                                    | Oss                  | 否                 | 否          |
| Azure Machine Learning 中用來執行資料表的自訂標記              | GA                   | 否                 | 否          |
| 公平 AzureML 整合                                               | 公開預覽       | 否                 | 否          |
| 可解譯性 SDK                                                      | GA                   | YES                | YES         |
| **訓練** |   | | |
| 實驗記錄資料流程                                              | GA                   | YES                | YES         |
| 增強式學習                                                     | 公開預覽       | 否                 | 否          |
| 實驗 UI                                                         | GA                   | YES                | YES         |
| .NET integration ML.NET 1。0                                                | GA                   | YES                | YES         |
| **推理** |   | | |
| 批次推斷                                                          | GA                   | YES                | YES         |
| Data Box Edge 與 FPGA                                                    | 公開預覽       | 否                 | 否          |
| **其他** |   | | |
| 開放資料集                                                              | 公開預覽       | YES                | YES         |
| 自訂認知搜尋                                                    | 公開預覽       | YES                | YES         |
| 許多模型                                                                | 公開預覽       | 否                 | 否          |


### <a name="azure-government-scenarios"></a>Azure Government 案例

| 狀況                                                    | US-Virginia | US-Arizona| 限制  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **一般安全性設定** |   | | |
| 服務之間的私人網路通訊                                     | 否 | 否 | 目前沒有 Private Link | 
| 停用/控制網際網路存取 (輸入和輸出) 和特定 VNet | PARTIAL| PARTIAL   | 在 ACI 上的 Azure Government 雙重檢查中無法使用 VNet 背後的 ACR | 
| 所有相關聯資源/服務的放置  | YES | YES |  |
| 靜態加密和傳輸中。                                                 | YES | YES |  |
| 計算資源的根和 SSH 存取權。                                          | YES | YES |  |
| 維護已部署系統的安全性 (實例、端點等 ) ，包括 endpoint protection、修補和記錄 |  PARTIAL|  PARTIAL |VNet 和私人端點背後的 ACI 目前無法使用 |                                  
| 控制 (停用/限制/限制) 使用 ACI/AKS 整合                    | PARTIAL| PARTIAL |VNet 和私人端點背後的 ACI 目前無法使用|
| Role-Based 存取控制 (RBAC) -自訂角色建立                           | YES | YES |  |
| 控制 ML 服務所使用的 ACR 映射存取， (Azure 提供/維護和自訂)   |PARTIAL|  PARTIAL | Azure Government 中不支援私人端點和 VNet 背後的 ACR |
| **一般 Machine Learning 服務使用量** |  | | |
| 能夠讓開發環境建立模型、將該模型定型、將其裝載為端點，並透過 webapp 使用     | YES | YES |  |
| 能夠從 ADLS (Data Lake Storage 提取資料)                                  |YES | YES |  |
| 從 Azure Blob 儲存體提取資料的能力                                       |YES | YES |  |



### <a name="additional-azure-government-limitations"></a>其他 Azure Government 限制

* 針對 Azure Machine Learning 計算實例，Azure Government 不提供重新整理權杖的能力。
* 模型分析不支援 US-Arizona 區域中的4個 Cpu。   
* 如果範例筆記本需要存取公用資料，Azure Government 可能無法在中運作。
* IP 位址： [VNet 和強制通道](how-to-secure-training-vnet.md#forced-tunneling) 指示中使用的 CLI 命令不會傳回 IP 範圍。 請改用 [AZURE IP 範圍和服務標籤來進行 Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) 。
* 針對已排程的管線，我們也會提供以 blob 為基礎的觸發程式機制。 CMK 工作區不支援此機制。 若要為 CMK 工作區啟用以 blob 為基礎的觸發程式，您必須進行額外的設定。 如需詳細資訊，請參閱 [觸發從邏輯應用程式執行機器學習管線](how-to-trigger-published-pipeline.md)。
* 防火牆：使用 Azure Government 區域時，請將下列其他主機新增至您的防火牆設定：

    * 若為亞利桑那州，請使用： `usgovarizona.api.ml.azure.us`
    * 針對弗吉尼亞州，請使用： `usgovvirginia.api.ml.azure.us`
    * 適用于兩者： `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure 中國 21Vianet 

| 功能                                       | 公用雲端狀態 | CH-東部-2 | CH-北-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **自動化機器學習** |    | | |
| 在筆記本中建立及執行實驗                                    | GA               | YES       | N/A        |
| 在 studio web 體驗中建立和執行實驗                        | 公開預覽   | YES       | N/A        |
| 領先業界的預測功能                                  | GA               | YES       | N/A        |
| 深度學習和其他先進學習的支援                      | GA               | YES       | N/A        |
|  (高達 100 GB) 的大型資料支援                                          | 公開預覽   | YES       | N/A        |
| Azure Databricks 整合                                              | GA               | 否        | N/A        |
| SQL、CosmosDB 和 HDInsight 整合                                   | GA               | YES       | N/A        |
| **Machine Learning 管線** |    | | |
| 使用 Azure ML SDK 建立、執行及發佈管線                   | GA               | YES       | N/A        |
| 使用 Azure ML SDK 建立管線端點                           | GA               | YES       | N/A        |
| 使用 Azure ML SDK 建立、編輯和刪除管線的排程執行 | GA               | YES       | N/A        |
| 在 studio 中查看管線執行詳細資料                                        | GA               | YES       | N/A        |
| 在 Azure ML 設計工具中建立、執行、視覺化及發佈管線          | GA  | YES       | N/A        |
| Azure Databricks 與 ML 管線整合                             | GA               | 否        | N/A        |
| 在 Azure ML 設計工具中建立管線端點                             | GA   | YES       | N/A        |
| **整合式筆記本** |   | | |
| 工作區筆記本和檔案共用                                        | GA               | YES       | N/A        |
| R 和 Python 支援                                                       | GA               | YES       | N/A        |
| 虛擬網路支援                                                    | 公開預覽   | 否        | N/A        |
| **計算執行個體** |    | | |
| 整合式筆記本的受控計算實例                         | GA               | 否        | N/A        |
| Jupyter，JupyterLab 整合                                            | GA               | YES       | N/A        |
| 虛擬網路 (VNet) 支援                                             | 公開預覽   | YES       | N/A        |
| **SDK 支援** |    | | |
| R SDK 支援                                                              | 公開預覽   | YES       | N/A        |
| Python SDK 支援                                                         | GA               | YES       | N/A        |
| **安全性** |   | | |
| 虛擬網路 (VNet) 支援定型                                | GA               | YES       | N/A        |
| 虛擬網路 (VNet) 支援推斷                               | GA               | YES       | N/A        |
| 評分端點驗證                                            | 公開預覽   | YES       | N/A        |
| Workplace Private link                                                     | 公開預覽   | 否        | N/A        |
| VNet 背後的 ACI                                                            | 公開預覽   | 否        | N/A        |
| VNet 背後的 ACR                                                            | 公開預覽   | 否        | N/A        |
| AKS 叢集的私人 IP                                                  | 公開預覽   | 否        | N/A        |
| **計算** |   | | |
| 跨工作區的配額管理                                         | GA               | YES       | N/A        |
| **機器學習服務的資料** | | | |
| 從 SDK 建立、查看或編輯資料集和資料存放區                  | GA               | YES       | N/A        |
| 從 UI 建立、查看或編輯資料集和資料存放區                   | GA               | YES       | N/A        |
| 從 SDK 查看、編輯或刪除資料集漂移監視器                   | 公開預覽   | YES       | N/A        |
| 從 UI 中查看、編輯或刪除資料集漂移監視器                    | 公開預覽   | YES       | N/A        |
| **機器學習服務生命週期** |    | | |
| 模型分析                                                            | GA               | PARTIAL   | N/A        |
| Machine Learning 的 Azure DevOps 擴充功能 & Azure ML CLI         | GA               | YES       | N/A        |
| 以 FPGA 為基礎的硬體加速模型                                     | GA               | 否        | N/A        |
| Visual Studio Code 整合                                             | 公開預覽   | 否        | N/A        |
| 事件方格整合                                                     | 公開預覽   | YES       | N/A        |
| 整合 Azure 串流分析與 Azure Machine Learning               | 公開預覽   | 否        | N/A        |
| **標記** |    | | |
| 標記專案管理入口網站                                        | GA               | YES       | N/A        |
| 標籤人員入口網站                                                            | GA               | YES       | N/A        |
| 使用私用員工標記                                          | GA               | YES       | N/A        |
| ML 輔助標記 (影像分類和物件偵測)            | 公開預覽   | YES       | N/A        |
| **負責任的 ML** |    | | |
| UI 中的可解釋性                                                       | 公開預覽   | 否        | N/A        |
| 差異隱私權 WhiteNoise 工具組                                    | Oss              | 否        | N/A        |
| Azure Machine Learning 中用來執行資料表的自訂標記              | GA               | 否        | N/A        |
| 公平 AzureML 整合                                               | 公開預覽   | 否        | N/A        |
| 可解譯性 SDK                                                      | GA               | YES       | N/A        |
| **訓練** |    | | |
| 實驗記錄資料流程                                              | GA               | YES       | N/A        |
| 增強式學習                                                     | 公開預覽   | 否        | N/A        |
| 實驗 UI                                                         | GA               | YES       | N/A        |
| .NET integration ML.NET 1。0                                                | GA               | YES       | N/A        |
| **推理** |   | | |
| 批次推斷                                                          | GA               | YES       | N/A        |
| Data Box Edge 與 FPGA                                                    | 公開預覽   | 否        | N/A        |
| **其他** |    | | |
| 開放資料集                                                              | 公開預覽   | YES       | N/A        |
| 自訂認知搜尋                                                    | 公開預覽   | YES       | N/A        |
| 許多模型                                                                | 公開預覽   | 否        | N/A        |



### <a name="additional-azure-china-limitations"></a>其他 Azure 中國限制

* Azure 中國具有有限的 VM SKU，尤其是針對 GPU SKU。 它只有 NCv3 系列 (V100) 。
* REST API 端點與全域 Azure 不同。 使用下表來尋找 Azure 中國區域的 REST API 端點：

    | REST 端點                 | 全域 Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | 管理平面 | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | 資料平面       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* 如果需要存取公用資料，範例筆記本可能無法運作。
* IP 位址範圍： [VNet 強制通道](how-to-secure-training-vnet.md#forced-tunneling) 指示中使用的 CLI 命令不會傳回 IP 範圍。 請改用 [AZURE IP 範圍和服務標籤來進行 Azure 中國](https://www.microsoft.com//download/details.aspx?id=57062) 。
* 目前已啟用 Private Link 的工作區不支援 Azure Machine Learning 計算實例預覽，但在下一次部署中，將會支援 CI，以便將服務擴充至所有 AML 區域。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure Machine learning 的可用區域，請參閱 [依區域](https://azure.microsoft.com/global-infrastructure/services/)提供的產品。
