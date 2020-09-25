---
title: 連接至 Azure 上儲存體服務中的資料
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning studio 建立資料存放區和資料集，以安全地連接到 Azure 儲存體服務中的資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to
ms.openlocfilehash: feb79a2a077f819cce22925f23f5ed640d05e8d3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296666"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio 連接到資料

在本文中，您將瞭解如何使用 [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)存取您的資料。 使用 [Azure Machine Learning 資料存放區](how-to-access-data.md)連線到 Azure 儲存體服務中的資料，然後使用 [Azure Machine Learning 資料集](how-to-create-register-datasets.md)將該資料封裝到 ML 工作流程中的工作。

下表定義並摘要說明資料存放區和資料集的優點。 

|Object|描述| 優點|   
|---|---|---|
|資料存放區| 將您的連線資訊（例如您的訂用帳戶識別碼和權杖授權）儲存在與工作區相關聯的 [Key Vault](https://azure.microsoft.com/services/key-vault/) ，以安全地連線到您在 Azure 上的儲存體服務 | 因為您的資訊已安全地儲存，所以您 <br><br> <li> 請勿 &nbsp; 將 &nbsp; 驗證 &nbsp; 認證 &nbsp; 或 &nbsp; 原始 &nbsp; 資料來源放在風險中。 <li> 您不再需要在腳本中硬編碼。
|資料集| 藉由建立資料集，您可以建立資料來源位置的參考，以及其中繼資料的複本。 您可以使用資料集 <br><br><li> 在模型定型期間存取資料。<li> 共用資料並與其他使用者共同作業。<li> 利用開放原始碼程式庫（例如 pandas）來探索資料。 | 因為資料集會進行延遲的評估，且資料會保留在現有的位置，所以您 <br><br><li>在您的儲存體中保留單一的資料複本。<li> 不會產生額外的儲存體成本 <li> 不會有意外變更原始資料來源的風險。<li>改善 ML 工作流程的效能速度。 

若要瞭解資料存放區和資料集如何符合 Azure Machine Learning 的整體資料存取工作流程，請參閱 [安全存取資料](concept-data.md#data-workflow) 檔。

如需程式碼優先體驗，請參閱下列文章，以使用 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true) ：
* [使用資料存放區連接到 Azure 儲存體服務](how-to-access-data.md)。 
* [建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。 

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- [Azure Machine Learning studio](https://ml.azure.com/)的存取權。

- Azure Machine Learning 工作區。 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

    -  當您建立工作區時，Azure blob 容器和 Azure 檔案共用會自動註冊為工作區的資料存放區。 這兩者分別會命名為 `workspaceblobstore` 和 `workspacefilestore`。 如果 blob 儲存體足以滿足您的需求，則 `workspaceblobstore` 會設定為預設資料存放區，並已設定為使用。 否則，您需要 Azure 上具有 [支援之儲存體類型](how-to-access-data.md#matrix)的儲存體帳戶。
    

## <a name="create-datastores"></a>建立資料存放區

您可以從[這些 Azure 儲存體解決方案](how-to-access-data.md#matrix)建立資料存放區。 **針對不支援的儲存體解決方案**，以及在 ML 實驗期間儲存資料輸出成本，您必須 [將資料移](how-to-access-data.md#move) 至支援的 Azure 儲存體解決方案。 [深入瞭解資料存放區](how-to-access-data.md)。 



在 Azure Machine Learning studio 的幾個步驟中建立新的資料存放區。

> [!IMPORTANT]
> 如果您的資料儲存體帳戶位於虛擬網路中，則需要額外的設定步驟，以確保 studio 具有您資料的存取權。 請參閱 [網路隔離 & 隱私權](how-to-enable-virtual-network.md#machine-learning-studio) ，以確保套用適當的設定步驟。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。
1. 在左窗格中，選取 [管理] 底下的 [資料存放區]。
1. 選取 [+ 新增資料存放區]。
1. 完成新資料存放區的表單。 此表單會根據您選取的 Azure 儲存體類型和驗證類型，以智慧方式自行更新。 請參閱「 [儲存體存取和許可權」一節](#access-validation) ，以瞭解在哪裡可以找到您需要的驗證認證才能填入此表單。

下列範例示範當您建立 **Azure blob 資料**存放區時，表單看起來的樣子：

![新資料存放區的表單](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>建立資料集

建立資料存放區之後，請建立資料集來與您的資料互動。 資料集會將您的資料封裝成機器學習工作的延遲評估取用物件（例如定型）。 [深入了解資料集](how-to-create-register-datasets.md)。

有兩種類型的資料集： FileDataset 和 TabularDataset。 
[FileDatasets](how-to-create-register-datasets.md#filedataset) 會建立單一或多個檔案或公用 url 的參考。 然而， [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) 會以表格格式來表示您的資料。 

下列步驟和動畫示範如何在 [Azure Machine Learning studio](https://ml.azure.com)中建立資料集。

> [!Note]
> 透過 Azure Machine Learning studio 建立的資料集會自動註冊到工作區。

![使用 UI 建立資料集](./media/how-to-connect-data-ui/create-dataset-ui.gif)

若要在 studio 中建立資料集：
1. 登入 [Azure Machine Learning studio](https://ml.azure.com/)。
1. 在左窗格的 [**資產**] 區段中，選取 [**資料集**]。
1. 選取 [ **建立資料集** ] 以選擇資料集的來源。 此來源可以是本機檔案、資料存放區、公用 Url 或 [Azure 開放資料集](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)。
1. 選取**Tabular** [表格式 **] 或 [** 檔案] 做為資料集類型。
1. 選取 **[下一步]** 以開啟資料存放區 **和檔案選取** 表單。 在此表單上，您可以選取要在建立資料集之後保留資料集的位置，以及選取要用於資料集的資料檔案。
    1. 如果您的資料位於虛擬網路中，請啟用 [略過驗證]。 深入瞭解 [虛擬網路隔離和隱私權](how-to-enable-virtual-network.md#machine-learning-studio)。
1. 選取 **[下一步]** 以填入 **設定和預覽** 和 **架構** 表單;系統會根據檔案類型，以智慧方式填入它們，而且您可以在建立這些表單之前進一步設定您的資料集。 
1. 選取 **[下一步]** 以查看 [ **確認詳細資料** ] 表單。 檢查您的選取專案，並為您的資料集建立選擇性的資料設定檔。 深入了解[資料分析](#profile)。
1. 選取 [ **建立** ] 以完成建立資料集。

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>資料設定檔和預覽

建立資料集之後，請確認您可以使用下列步驟在 studio 中查看設定檔和預覽。 

1. 登入 [Azure Machine Learning studio](https://ml.azure.com/)
1. 在左窗格的 [**資產**] 區段中，選取 [**資料集**]。
1. 選取您要查看的資料集名稱。 
1. 選取 [瀏覽]  索引標籤。 
1. 選取 [ **預覽** ] 或 [ **設定檔** ] 索引標籤。 

![查看資料集設定檔和預覽](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

您可在資料集中取得各式各樣的摘要統計資料來驗證資料集是否已針對 ML 準備就緒。 針對非數值資料行，其僅會包含如最小值、最大值和錯誤計數等基本統計資料。 針對數值資料行，您也可以檢閱其統計時間和估計分位數。 

具體而言，Azure Machine Learning 資料集的資料設定檔包括：

>[!NOTE]
> 針對無關類型特徵顯示的空白項目。

|統計資料|描述
|------|------
|功能| 要進行摘要的資料行名稱。
|設定檔| 以推斷類型為基礎的內嵌視覺效果。 例如，字串、布林值和日期會具備值的計數，小數 (數值) 則會包含近似長條圖。 這可供快速了解資料的分佈。
|類型分佈| 資料行中類型的內嵌值計數。 Null 為其自身的類型，因此此視覺效果在偵測奇數或遺漏值時相當實用。
|類型|資料行的推斷類型。 可能的值包括：字串、布林值、日期和小數。
|最小值| 資料行的最小值。 針對類型沒有固有順序 (例如布林值) 的功能，會出現空白專案。
|最大值| 資料行的最小值。 
|Count| 資料行中遺漏和未遺漏項目的總數。
|未遺漏計數| 資料行中未遺漏的項目數。 空字串和錯誤都會視為值，因此不會計入「未遺漏計數」中。
|分位數| 每個分位數的近似值可供了解資料其分佈。
|平均值| 資料行的算術平均數或平均。
|標準差| 此資料行資料的離散或變異量量值。
|Variance| 此資料行資料從其平均值分散程度的量值。 
|偏度| 此資料行資料與常態分佈相異程度的量值。
|峰度| 相較於常態分佈，此資料行資料集中於尾端程度的量值。

## <a name="storage-access-and-permissions"></a>儲存體存取和許可權

為了確保您可以安全地連線到您的 Azure 儲存體服務，Azure Machine Learning 要求您有權存取對應的資料儲存體。 此存取權取決於用來註冊資料存放區的驗證認證。

### <a name="virtual-network"></a>虛擬網路

如果您的資料儲存體帳戶位於 **虛擬網路**中，則需要額外的設定步驟，以確保 Azure Machine Learning 可以存取您的資料。 請參閱 [網路隔離 & 隱私權](how-to-enable-virtual-network.md#machine-learning-studio) ，以確保當您建立和註冊資料存放區時，會套用適當的設定步驟。  

### <a name="access-validation"></a>存取驗證

**在初始資料存放區建立和註冊**程式中，Azure Machine Learning 會自動驗證基礎儲存體服務是否存在，以及使用者提供的主體 (使用者名稱、服務主體或 SAS 權杖，) 可以存取指定的儲存體。

**建立資料**存放區之後，只會針對需要存取基礎儲存體容器的方法執行這項驗證， **而不** 是每次抓取資料存放區物件。 例如，如果您想要從資料存放區下載檔案，則會進行驗證；如果您只是要變更預設資料存放區，則不會進行驗證。

若要驗證您對基礎儲存體服務的存取權，您可以根據您想要建立的資料存放區類型，提供您的帳戶金鑰、共用存取簽章 (SAS) 權杖或服務主體。 [儲存體類型矩陣](how-to-access-data.md#matrix)會列出對應至每個資料存放區類型的支援驗證類型。

您可以在 [Azure 入口網站](https://portal.azure.com)上找到帳戶金鑰、SAS 權杖和服務主體資訊。

* 如果您打算使用帳戶金鑰或 SAS 權杖進行驗證，請在左窗格中選取 [儲存體帳戶]，然後選擇您要註冊的儲存體帳戶。
  * [概觀] 頁面會提供帳戶名稱、容器和檔案共用名稱等資訊。
      1. 針對帳戶金鑰，請移至 [設定] 窗格上的 [存取金鑰]。
      1. 針對 SAS 權杖，請移至 [設定] 窗格上的 [共用存取簽章]。

* 如果您打算使用 [服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 進行驗證，請移至您的 **應用程式註冊** ，然後選取您要使用的應用程式。
    * 其對應的 **總覽** 頁面將包含必要的資訊，例如租使用者識別碼和用戶端識別碼。

> [!IMPORTANT]
> 基於安全性理由，您可能需要變更 Azure 儲存體帳戶的存取金鑰 (帳戶金鑰或 SAS 權杖) 。 當您這樣做時，請務必將新的認證與您的工作區和連線的資料存放區同步。 瞭解如何 [同步處理已更新的認證](how-to-change-storage-access-key.md)。

### <a name="permissions"></a>權限

針對 Azure blob 容器和 Azure Data Lake Gen 2 儲存體，請確定您的驗證認證具有 **儲存體 Blob 資料讀取器** 存取權。 深入瞭解 [儲存體 Blob 資料讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)。 

## <a name="train-with-datasets"></a>使用資料集定型

在機器學習實驗中使用您的資料集來定型 ML 模型。 [深入瞭解如何使用資料集定型](how-to-train-with-datasets.md)

## <a name="next-steps"></a>後續步驟

* [使用 TabularDatasets 和自動化機器學習訓練的逐步範例](tutorial-first-experiment-automated-ml.md)。

* [定型模型](how-to-train-ml-models.md)。

* 如需更多資料集定型範例，請參閱 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)。
