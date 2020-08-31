---
title: 什麼是 Azure 資料科學虛擬機器
titleSuffix: Azure Data Science Virtual Machine
description: Azure 資料科學虛擬機器概觀 - 透過預先安裝和設定的工具和程式庫，在 Azure 雲端平台上輕鬆使用虛擬機器，以執行資料科學。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 2bfcdfcb01e7908c199054e793d82cdfa1b726c7
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816332"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>適用於 Linux 和 Windows 的 Azure 資料科學虛擬機器是什麼？

資料科學虛擬機器 (DSVM) 是 Azure 雲端平台上的自訂 VM 映像，專為進行資料科學建置。 其已預先安裝和預先設定許多常用的資料科學工具，以開始建置智慧應用程式進行進階分析。

下列項目有提供 DSVM：

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>與 Azure Machine Learning 比較

DSVM 是資料科學的自訂 VM 映像，但 [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) (AzureML) 是一種端對端平台，其中包含：

+ 完全受控的計算
  + 計算執行個體
  + 分散式 ML 工作的計算叢集
  + 用於即時評分的推斷叢集
+ 資料存放區 (例如 Blob、ADLS Gen2、SQL DB)
+ 實驗追蹤
+ 模型管理
+ Notebooks
+ 環境 (管理 Conda 和 R 相依性)
+ 標記
+ 管線 (自動執行端對端資料科學工作流程)

### <a name="comparison-with-azureml-compute-instances"></a>與 AzureML 計算執行個體比較

[Azure Machine Learning 計算執行個體](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance)是完全經過設定和__受控__的 VM 映像，DSVM 則是__非受控__ VM。

這兩個產品供應項目之間的主要差異如下所述：


|功能 |資料科學<br>VM |AzureML<br>計算執行個體  | 
|---------|---------|---------|
| 完全受控 | 否        | 是        |
|語言支援     |  Python、R、Julia、SQL、C#、<br> Java、Node.js、F#       | Python 和 R        |
|作業系統     | Ubuntu<br>Windows         |    Ubuntu     |
|已預先設定的 GPU 選項     |  是       |    是     |
|擴大選項 | 是 | 是 |
|SSH 存取    | 是        |    是     |
|RDP 存取    | 是        |     否    |
|內建<br>裝載的 Notebook     |   否<br>(需要進行其他設定)      |      是   |
|內建 SSO     | 否 <br>(需要進行其他設定)         |    是     |
|內建共同作業     | 否         | 是        |
|預先安裝的工具     |  Jupyter(lab)、RStudio Server、VSCode、<br> Visual Studio、PyCharm、Juno、<br>Power BI Desktop、SSMS、 <br>Microsoft Office 365、Apache Drill       |     Jupyter(lab)<br> RStudio Server   |

## <a name="sample-use-cases"></a>範例使用案例

以下說明 DSVM 客戶的一些常見使用案例。

### <a name="short-term-experimentation-and-evaluation"></a>短期實驗和評估

您可以使用 DSVM 來評估或學習新的資料科學[工具](./tools-included.md)，特別是透過我們已發佈的一些[範例和逐步解說](./dsvm-samples-and-walkthroughs.md)。

### <a name="deep-learning-with-gpus"></a>使用 GPU 的深度學習

在 DSVM 中，您的訓練模型可以在以圖形處理器 (GPU) 為基礎的硬體上使用深度學習演算法。 藉由利用 Azure 平台的 VM 規模調整功能，DSVM 可協助您根據需求在雲端中使用 GPU 型硬體。 當訓練大型模型或需要高速計算同時保留相同的 OS 磁碟時，您可以切換到 GPU 型 VM。 您可以選擇任何已啟用 GPU、且含有 DSVM 的 N 系列虛擬機器 SKU。 注意，Azure 免費帳戶不支援已啟用 GPU 的虛擬機器 SKU。

Windows 版的 DSVM 預先安裝了 GPU 驅動程式、架構和 GPU 版本的深入學習架構。 在 Linux 版本上，GPU 的深度學習會在 Ubuntu DSVM 上啟用。 

您可以將 Ubuntu 或 Windows 版 DSVM 部署到非 GPU 型的 Azure 虛擬機器。 在此情況下，所有深度學習架構都會回復為 CPU 模式。

[深入了解可用的深度學習和 AI 架構](dsvm-tools-deep-learning-frameworks.md)。

### <a name="data-science-training-and-education"></a>資料科學訓練和教育

教導資料科學課程的企業訓練者和教育者通常會提供虛擬機器映像。 此映像可確保他們的學生擁有一致的設定且範例如預期般運作。

DSVM 會建立具有一致設定的隨選環境，緩解支援和不相容性挑戰。 在必須經常建置這些環境的案例中，特別是針對較短的訓練課程，可以有大幅獲益。


## <a name="whats-included-on-the-dsvm"></a>DSVM 包含哪些內容？

如需 Windows 和 Linux DSVM 的完整工具清單，請參閱[這裡](tools-included.md)。

## <a name="next-steps"></a>後續步驟

請透過下列文章來深入了解：

+ Windows：
  + [設定 Windows DSVM](provision-vm.md)
  + [Windows DSVM 上的資料科學](vm-do-ten-things.md)

+ Linux：
  + [設定 Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Linux DSVM 上的資料科學](linux-dsvm-walkthrough.md)
