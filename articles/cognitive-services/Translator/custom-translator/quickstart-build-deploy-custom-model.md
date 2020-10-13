---
title: 快速入門：建置、部署和使用自訂模型 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您會進行「使用自訂翻譯工具來建置翻譯系統」的逐步程序。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: f24c9c372ff91db5836a62ac2d08b569434ff253
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761574"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>快速入門：建置、部署和使用自訂模型來進行翻譯

本文會逐步說明如何使用自訂翻譯工具來建置翻譯系統。

## <a name="prerequisites"></a>必要條件

1. 若要使用[自訂翻譯工具](https://portal.customtranslator.azure.ai)入口網站，您必須擁有 [Microsoft 帳戶](https://signup.live.com)或 [Azure AD 帳戶](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (裝載於 Azure 上的組織帳戶) 以便進行登入。

2. 可透過 Azure 入口網站來使用的翻譯工具文字 API 訂用帳戶。 您必須有翻譯工具文字 API 訂用帳戶金鑰，才能與工作區相關聯。 請參閱[如何註冊翻譯工具文字 API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup)。

3. 當您擁有上述兩帳戶時，請登入[自訂翻譯](https://portal.customtranslator.azure.ai)入口網站來建立工作區、專案、上傳檔案，以及建立/部署模型。

>[!Note]
>自訂翻譯不支援針對在[已啟用 VNET](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)內建立的翻譯工具文字 API 資源建立工作區。

## <a name="create-a-workspace"></a>建立工作區

如果您是第一次登入的使用者，系統會要求您先同意服務條款，才能建立工作區，並將您的工作區與 Microsoft 翻譯工具文字 API 訂用帳戶建立關聯。

![建立工作區](media/quickstart/terms-of-service.png)
![建立工作區映像 1](media/quickstart/create-workspace-1.png)
![建立工作區映像 2](media/quickstart/create-workspace-2.png)
![建立工作區映像 3](media/quickstart/create-workspace-3.png)
![建立工作區映像 4](media/quickstart/create-workspace-4.png)
![建立工作區映像 5](media/quickstart/create-workspace-5.png)
![建立工作區映像 6](media/quickstart/create-workspace-6.png)

之後造訪自訂翻譯入口網站時，您可以瀏覽至可管理工作區的 [設定] 頁面來建立更多工作區、將 Microsoft 翻譯工具文字 API 訂用帳戶金鑰與您的工作區建立關聯、新增共同擁有者，以及變更訂用帳戶金鑰。

## <a name="create-a-project"></a>建立專案

在自訂翻譯工具入口網站的登陸頁面上，按一下 [新增專案]。 在對話方塊中，您可以輸入想要的專案名稱、語言組和類別，以及其他相關欄位。 然後，儲存您的專案。 如需詳細資訊，請瀏覽[建立專案](how-to-create-project.md)。

![建立專案](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>上傳文件

接著，上傳[訓練](training-and-model.md#training-document-type-for-custom-translator)、[微調](training-and-model.md#tuning-document-type-for-custom-translator)和[測試](training-and-model.md#testing-dataset-for-custom-translator)文件集。 您可以上傳[平行處理](what-are-parallel-documents.md)和組合文件。 您也可以上傳[字典](what-is-dictionary.md)。

您可以從 [文件] 索引標籤或從特定專案的頁面上傳文件。

![上傳文件](media/quickstart/ct-how-to-upload.png)

在上傳文件時，請選擇文件類型 (訓練、微調或測試) 和語言組。 在上傳平行處理的文件時，您另外還需要指定文件名稱。 如需詳細資訊，請瀏覽[上傳文件](how-to-upload-document.md)。

## <a name="create-a-model"></a>建立模型

所有必要文件皆已上傳時，下一步是建置模型。

選取您所建立的專案。 您會看到所有已上傳、且與這個專案共用語言組的文件。 選取您想要包含在模型中的文件。 您可以選取[訓練](training-and-model.md#training-document-type-for-custom-translator)、[微調](training-and-model.md#tuning-document-type-for-custom-translator)和[測試](training-and-model.md#testing-dataset-for-custom-translator)資料，也可以只選取訓練資料，然後讓自訂翻譯工具自動建置模型的微調和測試集。

![建立模型](media/quickstart/ct-how-to-train.png)

選好所需文件時，按一下 [建立模型] 按鈕，以建立模型並開始訓練。 您可以在 [模型] 索引標籤中看到訓練的狀態，以及所有受訓練模型的詳細資料。

如需詳細資訊，請瀏覽[建立模型](how-to-train-model.md)。

## <a name="analyze-your-model"></a>分析模型

訓練順利完成後，請檢查結果。 BLEU 分數便是其中一個可指出翻譯品質的計量。 您也可以手動比較自訂模型所進行的翻譯與測試集所提供的翻譯，方法是瀏覽至 [測試] 索引標籤，然後按一下 [系統結果]。 手動檢查其中的一些翻譯，會讓您清楚了解系統所產生的翻譯品質。 如需詳細資訊，請瀏覽[系統測試結果](how-to-view-system-test-results.md)。

## <a name="deploy-a-trained-model"></a>部署已完成訓練的模型

當您準備好部署已完成訓練的模型時，請按一下 [部署] 按鈕。 每個專案可以有一個已部署的模型，而且您可以在 [狀態] 資料行中檢視部署的狀態。 如需詳細資訊，請瀏覽[模型部署](how-to-view-system-test-results.md#deploy-a-model)

![部署已完成訓練的模型](media/quickstart/ct-how-to-deploy.png)

## <a name="swap-deployed-model"></a>交換已部署的模型

若要在專案內交換已部署的模型，請按一下目標模型旁顯示的 [交換] 按鈕。 在交換過程中，已部署的模型會繼續提供給轉譯要求使用。 

![交換已部署的模型](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>使用已部署的模型

已部署的模型可透過 Microsoft 翻譯工具[文字 API V3 (藉由指定 CategoryID)](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 來存取。 如需翻譯工具文字 API 的詳細資訊，請參閱 [API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)網頁。

## <a name="next-steps"></a>後續步驟

- 了解如何瀏覽[自訂翻譯工具工作區和管理專案](workspace-and-project.md)。
