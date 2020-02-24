---
title: 快速入門：使用範例標籤工具為表單加上標籤、定型模型以及分析表單 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將使用表單辨識器範例標籤工具，手動為表單文件加上標籤。 接著，您將使用已加上標籤的文件來定型自訂模型，並使用模型來擷取索引鍵/值組。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 812680e587ac5c5c8b3d949199a615fcd85fa610
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485347"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>使用範例標籤工具，以標籤定型表單辨識器模型

在本快速入門中，您將搭配使用表單辨識器 REST API 與範例標籤工具，以手動加上標籤的資料定型自訂模型。 若要深入了解此功能，請參閱概觀的[以標籤定型](../overview.md#train-with-labels)一節。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若要完成此快速入門，您必須：

- 至少有六個相同類型的表單。 您將使用此資料來定型模型和測試表單。 您可以使用本快速入門的[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)。 將訓練檔案上傳至 Azure 儲存體帳戶中 Blob 儲存體容器的根目錄。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>設定範例標籤工具

您將使用 Docker 引擎來執行範例標籤工具。 請依照下列步驟來設定 Docker 容器。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。
1. 首先，在主機電腦上安裝 Docker。 主機電腦可以是您的本機電腦 ([Windows](https://docs.docker.com/docker-for-windows/)、[macOS](https://docs.docker.com/docker-for-mac/) 或 [Linux](https://docs.docker.com/install/))。 或者，您可以使用 Azure 中的 Docker 主控服務，例如[部署至 Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy?view=azs-1910) 的 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/index)、[Azure 容器執行個體](https://docs.microsoft.com/azure/container-instances/index)或 Kubernetes 叢集。 主機電腦必須符合下列硬體需求：

    | 容器 | 最小值 | 建議|
    |:--|:--|:--|
    |範例標籤工具|2 核心、4 GB 記憶體|4 核心，8 GB 記憶體|
    
1. 使用 `docker pull` 命令取得範例標籤工具容器。
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. 現在您已準備就緒，可使用 `docker run` 執行容器。
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   此命令會讓範例標籤工具可透過網頁瀏覽器來使用。 移至 [http://localhost:3000](http://localhost:3000)。

> [!NOTE]
> 您也可以使用表單辨識器 REST API 為文件加上標籤及定型模型。 若要使用此 REST API 進行定型和分析，請參閱[使用 REST API 和 Python以標籤定型](./python-labeled-data.md)。

## <a name="set-up-input-data"></a>設定輸入資料

首先，請確定所有訓練文件都有相同的格式。 如果您的表單有多種格式，請根據共同的格式將這些表單組織成子資料夾。 在定型時，您必須將 API 導向子資料夾。

### <a name="configure-cross-domain-resource-sharing-cors"></a>設定跨網域資源共用 (CORS)

在您的儲存體帳戶上啟用 CORS。 在 Azure 入口網站中選取您的儲存體帳戶，然後按一下左窗格中的 [CORS]  索引標籤。 在底線上填入下列值。 然後，按一下頂端的 [儲存]  。

* 允許的原始來源 = * 
* 允許的方法 = \[全選\]
* 允許的標頭 = *
* 公開的標頭 = * 
* 存留期上限 = 200

> [!div class="mx-imgBorder"]
> ![Azure 入口網站中的 CORS 設定](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>連線至範例標籤工具

範例標籤工具會連線至來源 (原始表單所在之處) 和目標 (此工具匯出所建立標籤和輸出資料時所選擇的目的地)。

您可以跨專案設定和共用連線。 連線會使用可延伸提供者模型，因此您可以輕鬆地新增來源/目標提供者。

若要建立新的連線，請按一下左側瀏覽列中的 [新增連線]  (插頭) 圖示。

在欄位中填入下列值：

* **顯示名稱** - 連線的顯示名稱。
* **描述** - 您的專案描述。
* **SAS URL** - Azure Blob 儲存體容器的共用存取簽章 (SAS) URL。 若要擷取 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]  。 請將到期時間設定為您用完服務後的時間。 確定 [讀取]  、[寫入]  、[刪除]  和 [列出]  權限均已勾選，然後按一下 [建立]  。 然後，複製 [URL]  區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。

![範例標籤工具的連線設定](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>建立新專案

在範例標籤工具中，專案會儲存您的組態和設定。 建立新的專案，並在欄位中填入下列值：

* **顯示名稱** - 專案的顯示名稱
* **安全性權杖** - 某些專案設定可能會包含敏感值，例如 API 金鑰或其他共用秘密。 每個專案都會產生可用來加密/解密敏感專案設定的安全性權杖。 您可以按一下左側瀏覽列下方角落的齒輪圖示，在 [應用程式設定] 中找到安全性權杖。
* **來源連線** - 您在先前的步驟中建立而要在此專案中使用的 Azure Blob 儲存體連線。
* **資料夾路徑** - 選擇性 - 如果您的來源表單位於 Blob 容器的資料夾中，請在此處指定資料夾名稱
* **表單辨識器服務 URI** - 您的表單辨識器端點 URL。
* **API 金鑰** - 您的表單辨識器訂用帳戶金鑰。
* **描述** - 選擇性 - 專案描述

![範例標籤工具上的新增專案頁面](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>為表單加上標籤

當您建立或開啟專案時，主要的標籤編輯器視窗隨即開啟。 標籤編輯器由三個部分組成：

* 可調整大小的預覽窗格，其中包含來自來源連線的可捲動表單清單。
* 可讓您套用標籤的主要編輯器窗格。
* 可讓使用者修改、鎖定、重新排列和刪除標籤的標籤編輯器窗格。 

### <a name="identify-text-elements"></a>識別文字元素

按一下左窗格上的 [在所有檔案上執行 OCR]  ，以取得每份文件的文字版面配置資訊。 標籤工具會在每個文字元素周圍繪製週框方塊。

### <a name="apply-labels-to-text"></a>將標籤套用至文字

接下來，您將建立標籤，並將其套用至要讓模型辨識的文字元素。

1. 首先，使用標籤編輯器窗格建立您要識別的標記 (標籤)。
1. 在主要編輯器中，按一下並拖曳以從反白顯示的文字元素中選取一或多個單字。

    > [!NOTE]
    > 您目前無法選取跨多個頁面的文字。
1. 按一下您要套用的標籤，或按對應的鍵盤按鍵。 每個選取的文字元素只能套用一個標籤，且每個頁面只能將每個標籤套用一次。

    > [!TIP]
    > 數字鍵會指派為前十個標籤的快速鍵。 您可以使用標籤編輯器窗格中的向上和向下箭號圖示來重新排序標籤。

依照上述步驟為您的五個表單加上標籤，然後繼續進行下一個步驟。

![範例標籤工具的主要編輯器視窗](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>定型自訂模型

按一下左窗格上的「訓練」圖示 (火車)，以開啟 [訓練] 頁面。 然後，按一下 [訓練]  按鈕，開始對模型進行定型。 定型程序完成後，您會看到下列資訊：

* **模型識別碼** - 已建立並定型之模型的識別碼。 每個定型呼叫都會以本身的識別碼建立新的模型。 請將此字串複製到安全之處；如果您想要透過 REST API 進行預測呼叫，就會需要此字串。
* **平均精確度** - 模型的平均精確度。 您可以為其他表單加上標籤並再次定型以建立新的模型，進而改善模型的精確度。 建議您先為五個表單加上標籤，然後再視需要新增更多表單。
* 標籤的清單，以及每個標籤的預估精確度。

![訓練檢視](../media/label-tool/train-screen.png)

定型完成後，請查看 [平均精確度]  值。 如果該值偏低，您應新增更多輸入文件，並重複上述步驟。 您已加上標籤的文件會保留在專案索引中。

> [!TIP]
> 您也可以使用 REST API 呼叫來執行定型程序。 若要了解其執行方法，請參閱[使用 Python 以標籤定型](./python-labeled-data.md)。

## <a name="analyze-a-form"></a>分析表單

按一下左側的「預測 (矩形)」圖示，以測試您的模型。 上傳還未用於定型程序的表單文件。 然後按一下右側的 [預測]  按鈕，以取得表單的索引鍵/值預測。 此工具會將標籤套用到週框方塊中，且會報告每個標籤的信賴度。

> [!TIP]
> 您也可以使用 REST 呼叫來執行分析 API。 若要了解其執行方法，請參閱[使用 Python 以標籤定型](./python-labeled-data.md)。

## <a name="improve-results"></a>改善結果

根據報告的精確度，您可以進一步定型以改善模型。 完成預測後，請查看每個套用標籤的信賴值。 如果平均精確度的訓練值很高，但信賴分數很低 (或結果不正確)，您應將用於預測的檔案新增至定型集，並為其加上標籤，然後再次定型。

如果所分析的文件與用於定型的文件不同，則報告的平均精確度、信賴分數和實際精確度可能會不一致。 請注意，有些文件在常人看來會是類似的，但 AI 模型則會看出其不同之處。 例如，假設您使用具有兩種變化的表單類型來定型，其中，定型集由 20% 的變化 A 和 80% 的變化 B 所組成。在預測期間，變化 A 文件的信賴分數可能會較低。

## <a name="save-a-project-and-resume-later"></a>儲存專案並於稍後繼續執行

若要另擇時間或在另一個瀏覽器中繼續執行專案，您必須儲存專案的安全性權杖，並於稍後重新輸入。 

### <a name="get-project-credentials"></a>取得專案認證
移至您的專案設定頁面 (滑杆圖示)，並記下安全性權杖名稱。 然後，移至您的應用程式設定 (齒輪圖示)，此處會顯示目前瀏覽器執行個體中的所有安全性權杖。 找出專案的安全性權杖，並將其名稱和金鑰值複製到安全的位置。

### <a name="restore-project-credentials"></a>還原專案認證
想要繼續執行專案時，您必須先建立對相同 Blob 儲存體容器的連線。 請重複進行上述步驟來執行此動作。 然後，移至應用程式設定頁面 (齒輪圖示)，並確認您專案的安全性權杖位於該處。 如果不在該處，請新增安全性權杖，並以其覆寫先前步驟中的權杖名稱和金鑰。 然後，按一下 [儲存設定]。 

### <a name="resume-a-project"></a>繼續執行專案
最後，移至主頁面 (房屋圖示)，然後按一下 [開啟雲端專案]。 接著，選取 Blob 儲存體連線，再選取專案的 *.vott* 檔案。 應用程式會載入所有專案的設定，因為它具有安全性權杖。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用表單辨識器範例標籤工具，以手動加上標籤的資料為模型定型。 如果您想要將標籤工具整合到您自己的應用程式中，請使用可處理標示資料定型的 REST API。

> [!div class="nextstepaction"]
> [使用 Python 以標籤定型](./python-labeled-data.md)
