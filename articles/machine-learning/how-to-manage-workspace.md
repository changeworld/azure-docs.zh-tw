---
title: 在入口網站中建立工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 入口網站中建立、查看和刪除 Azure Machine Learning 的工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: dccf8b2e9608de4f22f9782eb9f3cdb489e18be3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319706"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理 Azure Machine Learning 工作區
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將會在[Azure Machine Learning](overview-what-is-azure-ml.md)的 Azure 入口網站中建立、查看和刪除[**Azure Machine Learning 的工作區**](concept-workspace.md)。  入口網站是開始使用工作區的最簡單方式，但隨著您的需求變更或自動化的需求增加，您也可以[使用 CLI](reference-azure-machine-learning-cli.md)、Python 程式[代碼](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或透過[VS Code 延伸](tutorial-setup-vscode-extension.md)模組來建立和刪除工作區。

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
   位置 | 選取最接近您的使用者與資料資源的位置，以建立工作區。
   工作區版本 | 選取 [**基本**] 或 [**企業**]。  此工作區版本會決定您可以存取和定價的功能。 深入瞭解[基本和企業版供應](overview-what-is-azure-ml.md#sku)專案。 

    ![設定您的工作區](./media/how-to-manage-workspace/select-edition.png)

1. 當您完成設定工作區時，請選取 [**審查 + 建立**]。
2. 檢查設定，並進行任何其他變更或更正。 當您對設定感到滿意時，請選取 [**建立**]。

   > [!Warning] 
   > 在雲端中建立工作區可能需要數分鐘的時間。

   程序完成後，會出現部署成功訊息。 
 
 1. 若要檢視新的工作區，選取 [前往資源]****。

### <a name="download-a-configuration-file"></a>下載設定檔

1. 如果您將建立[計算實例](tutorial-1st-experiment-sdk-setup.md#azure)，請略過此步驟。

1. 如果您打算在參考此工作區的本機環境上使用程式碼， 請從工作區的 [概觀]**** 區段中，選取 [下載 config.xml]****。  

   ![下載 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 指令碼或 Jupyter Notebook 將文件置於目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。 當您建立計算實例時，會為您將此檔案新增至 VM 上的正確目錄。

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>升級至 Enterprise edition

您可以將工作區從基本版本升級至 Enterprise edition，以利用增強功能，例如低程式碼體驗和增強的安全性功能。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. 選取您想要升級的工作區。

1. 選取頁面右上方的 [**深入瞭解**]。

   [![升級工作區 ](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. 在出現的視窗中選取 [**升級**]。


> [!IMPORTANT]
> 您無法將 Enterprise edition 工作區降級為基本版本工作區。 

## <a name="find-a-workspace"></a><a name="view"></a>尋找工作區

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在 [頂端搜尋] 欄位中，輸入**Machine Learning**。  

1. 選取 [Machine Learning]****。

   ![搜尋 Azure Machine Learning 工作區](./media/how-to-manage-workspace/find-workspaces.png)

1. 查看找到的工作區清單。 您可以根據訂用帳戶、資源群組和位置來篩選。  

1. 選取工作區以顯示其屬性。

## <a name="delete-a-workspace"></a>刪除工作區

在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您想要刪除的工作區頂端的 [**刪除**]。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="刪除工作區":::

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新租用戶。 這麼做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>刪除 Azure Container Registry

Azure Machine Learning 工作區會使用 Azure Container Registry (ACR) 進行某些作業。 它會在第一次需要 ACR 執行個體時自動建立一個。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>後續步驟

遵循完整的教學課程，瞭解如何使用工作區來建立、定型和部署具有 Azure Machine Learning 的模型。

> [!div class="nextstepaction"]
> [教學課程：訓練模型](tutorial-train-models-with-aml.md)
