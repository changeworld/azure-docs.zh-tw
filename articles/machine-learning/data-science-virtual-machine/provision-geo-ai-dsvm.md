---
title: 快速入門：建立地區人工智慧資料科學虛擬機器
titleSuffix: Azure Data Science Virtual Machine
description: 在 Azure 上設定和建立 Geo AI 資料科學虛擬機器，以進行地理空間分析和機器學習。
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77525884"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>快速入門：在 Azure 上設定地區人工智慧虛擬機器 

地區人工智慧資料科學虛擬機器 (Geo-DSVM) 是熱門 [Azure 資料科學虛擬機器](https://aka.ms/dsvm)的擴充功能，特別設定來合併人工智慧與地理空間分析。 VM 中的地理空間分析是由 [ArcGIS Pro](https://www.arcgis.com/features/index.html) 驅動。 資料科學虛擬機器 (DSVM) 可以對快速訓練機器學習模型，甚至是深度學習模型。 為了開發這些模型，其會使用以地理資訊擴充的資料。 只有 Windows 2016 DSVM 會支援 Geo-DSVM。 

Geo-DSVM 中包含的 AI 工具包括下列各項：

- GPU 版本的熱門深度學習架構，像是 Microsoft Cognitive Toolkit、TensorFlow、Keras、Caffe2 和 Chainer
- 用來取得和前置處理影像與文字資料的工具
- 適用於開發活動的工具，例如 Microsoft Machine Learning Server Developer 版本、Anaconda Python、Python 和 R 適用的 Jupyter Notebook、Python 和 R 適用的 IDE，以及 SQL 資料庫
- ESRI 的 ArcGIS Pro 桌面軟體，以及可與來自人工智慧應用程式之地理空間資料搭配運作的 Python 和 R 介面
 

## <a name="create-your-geo-ai-data-science-vm"></a>建立您的地區人工智慧資料科學 VM

若要建立地區人工智慧資料科學 VM 的執行個體，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)上的虛擬機器清單。
1. 選取底部的 [建立]  來產生精靈：

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. 此精靈的四個步驟都需要輸入資訊。 如需關於此輸入的詳細資訊，請參閱下一節。

### <a name="wizard-details"></a>精靈詳細資料 ###

**基本**：

- **Name**：您所建立的資料科學伺服器名稱。
    
- **使用者名稱**：系統管理員帳戶登入識別碼。
    
- **密碼**：系統管理員帳戶密碼。
    
- 訂用帳戶  ：如果您有多個訂用帳戶，請選取要用來建立機器和開立帳單的訂用帳戶。
    
- **資源群組**：您可以建立新的群組，也可以使用訂用帳戶中**空白**的現有 Azure 資源群組。
    
- **位置**：選取最適合的資料中心。 一般來說，這會是擁有您大部分資料的資訊中心，或是最接近您實際位置而可進行最快速網路存取的資料中心。 如果您打算對 GPU 執行深度學習，您必須在 Azure 中選擇具有 NC 系列 GPU VM 執行個體的其中一個位置。 這些位置目前有：**美國東部、美國中北部、美國中南部、美國西部 2、北歐、西歐**。 如需最新的清單，請參閱[依據區域的 Azure 產品](https://azure.microsoft.com/regions/services/)頁面，並在 [計算]  底下尋找 **NC 系列**。 
    
    
**設定**：如果您打算在「地區 DSVM」的 GPU 上執行深度學習，請選取其中一個 NC 系列 GPU 虛擬機器大小。 否則，您可以選擇其中一個以 CPU 為基礎的執行個體。 為您的 VM 建立儲存體帳戶。 
       
**摘要**：請確認您輸入的所有資訊都正確無誤。
    
 ：若要開始佈建程序，請按一下 [購買]  。 系統會提供服務條款的連結。 VM 除了您在 [大小]  步驟中所選伺服器大小的計算費用之外，不會收取任何其他費用。 
 
 >[!NOTE]
 > 佈建大約需要 20 到 30 分鐘。 在 Azure 入口網站中會顯示佈建的狀態。

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>如何存取地區人工智慧資料科學虛擬機器

 在建立 VM 後，您就可以開始使用其上安裝和預先設定的工具。 我們為許多工具提供了開始功能表磚和桌面圖示。 您可以使用您在 [基本]  區段中設定的系統管理員帳戶認證，透過遠端桌面存取 VM。

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>使用 VM 中安裝的 ArcGIS Pro

Geo-DSVM 已經預先安裝 ArcGIS Pro 桌面及預先設定環境，以搭配 DSVM 中的所有工具運作。 當您啟動 ArcGIS 時，系統會提示您輸入 ArcGIS 帳戶的認證。 如果您已經有 ArcGIS 帳戶且具有軟體的授權，您可以使用現有的認證。  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

否則，您可以註冊新的 ArcGIS 帳戶和授權，或者取得[免費試用](https://www.arcgis.com/features/free-trial.html)。 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

在您註冊標準 ArcGIS 帳戶或免費試用後，即可藉由遵循[開始使用 ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf) 中的指示，針對您的帳戶授權 ArcGIS Pro。

透過您的 ArcGIS 帳戶登入 ArcGIS Pro 桌面之後，您就可以針對地理空間分析和機器學習專案，開始使用在 VM 上安裝及設定的資料科學工具。

## <a name="next-steps"></a>後續步驟

利用下列資源中的指引來開始使用地區人工智慧資料科學 VM：

* [使用地區 AI 資料科學 VM](use-geo-ai-dsvm.md)
