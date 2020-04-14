---
title: 在門戶中建立 Azure 機器學習工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 門戶中創建、查看和刪除 Azure 機器學習工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 4b4b64bcca57e1dc98cdba10626597532ae1461c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269718"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>在 Azure 門戶建立與管理 Azure 機器學習工作區
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將在 Azure 門戶中建立、查看和刪除[Azure](overview-what-is-azure-ml.md)[**機器學習工作區**](concept-workspace.md)。  門戶是開始使用工作區的最簡單方法,但隨著您的需求變化或自動化要求的提高,您也可以[使用 CLI、Python](reference-azure-machine-learning-cli.md)[代碼](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或透過[VS 代碼擴展](tutorial-setup-vscode-extension.md)創建和刪除工作區。

## <a name="create-a-workspace"></a>建立工作區

若要建立工作區，您將需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

1. 使用您 Azure 訂閱的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 

1. 在 Azure 入口網站的左上角，選取 [+建立資源]****。

      ![建立新的資源](./media/how-to-manage-workspace/create-workspace.gif)

1. 使用搜尋列尋找 **Machine Learning**。

1. 選取 [Machine Learning]****。

1. 在 [Machine Learning]**** 窗格中選取 [建立]**** 來開始操作。

1. 提供下列資訊來設定新的工作區：

   欄位|描述 
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。 工作區名稱不區分大小寫。
   訂用帳戶 |選取您要使用的 Azure 訂用帳戶。
   資源群組 | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **docs-aml**。 
   Location | 選取最接近您的使用者與資料資源的位置，以建立工作區。
   工作區版本 | 選擇**基本**「**或「企業**」。  此工作區版本確定您將有權訪問和定價的功能。 瞭解有關[基本版和企業版產品的更多。](overview-what-is-azure-ml.md#sku) 

    ![設定您的工作區](./media/how-to-manage-workspace/select-edition.png)

1. 完成配置工作區後,選擇 **「審閱 + 創建**」。。
2. 查看設置並進行任何其他更改或更正。 當您對設置滿意時,選擇 **"創建**"。

   > [!Warning] 
   > 在雲端中建立工作區可能需要數分鐘的時間。

   程序完成後，會出現部署成功訊息。 
 
 1. 若要檢視新的工作區，選取 [前往資源]****。

### <a name="download-a-configuration-file"></a>下載設定檔

1. 如果要創建[計算實例](tutorial-1st-experiment-sdk-setup.md#azure),請跳過此步驟。

1. 如果您打算在參考此工作區的本機環境上使用程式碼， 請從工作區的 [概觀]**** 區段中，選取 [下載 config.xml]****。  

   ![下載 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 指令碼或 Jupyter Notebook 將文件置於目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。 建立計算實例時,此檔將添加到 VM 上的正確目錄中。

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>升級到企業版

您可以將工作區從基本版升級到企業版,以利用增強的功能(如低代碼體驗和增強的安全功能)。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. 選擇要升級的工作區。

1. 在頁面右上角選擇 **「瞭解更多**」。

   [![升級工作區](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. 在顯示的視窗中選擇 **「升級**」 。


> [!IMPORTANT]
> 不能將企業版工作區降級為基本版工作區。 

## <a name="find-a-workspace"></a><a name="view"></a>尋找工作區

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在頂部搜尋欄位中,鍵入**機器學習**。  

1. 選取 [Machine Learning]****。

   ![搜尋 Azure 機器學習工作區](./media/how-to-manage-workspace/find-workspaces.png)

1. 查看找到的工作區清單。 您可以根據訂用帳戶、資源群組和位置來篩選。  

1. 選擇工作區以顯示其屬性。

## <a name="delete-a-workspace"></a>刪除工作區

在[Azure 門戶](https://portal.azure.com/)中,選擇要刪除的工作區頂部的 **「刪除**」。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="刪除工作區":::

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供程式錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure 機器學習工作區移動到其他訂閱,或將所屬訂閱移動到新租戶。 這樣做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>移除 Azure 容器註冊表

Azure 機器學習工作區使用 Azure 容器註冊表 (ACR) 執行某些操作。 當 ACR 實體首次需要時,它將自動建立 ACR 實例。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>後續步驟

請按照完整教程瞭解如何使用 Azure 機器學習構建、訓練和部署模型。

> [!div class="nextstepaction"]
> [教學課程：訓練模型](tutorial-train-models-with-aml.md)
