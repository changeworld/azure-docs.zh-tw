---
title: ML Studio (傳統) ：適用于 web 服務的 Excel 增益集-Azure
description: 如何在 Excel 中直接使用 Azure Machine Learning Web 服務，而不需要撰寫任何程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: fd406535b00ed0e25f44d875ee9b5fe716971bab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341786"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>適用于 Azure Machine Learning Studio (傳統) web 服務的 Excel 增益集

**適用於：** ![是](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)![否](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)


Excel 可以讓您直接輕鬆呼叫 Web 服務，而不需要撰寫任何程式碼。

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>使用活頁簿中現有 Web 服務的步驟

1. 開啟 [範例 Excel 檔案](https://aka.ms/amlexcel-sample-2)，其中包含 Excel 增益集和鐵達尼號乘客的相關資料。 
 
    > [!NOTE]
    > - 您會看到和檔案相關聯的 Web 服務清單，以及在底部的 [Auto-predict] \(自動預測\) 核取方塊。 如果您啟用自動預測，每當輸入變更時，您**所有**服務的預測都會更新。 如果取消選取，您將需要按一下 [Predict All] \(全部預測\) 來重新整理。 若要啟用服務層級的自動預測，請移至步驟 6。
    > - Azure Machine Learning Excel 增益集將會呼叫 Office 增益集存放區以載入。 如果您的組織禁止存取 Office 增益集存放區，您將會在載入增益集時看到錯誤。 在此情況下，請從 Microsoft 365 系統管理中心部署 Azure Machine Learning Excel 增益集。 然後，藉由貼上 URL 和 API 金鑰，以手動方式叫用增益集並手動新增 web 服務。

 

2. 按一下 Web 服務加以選擇，在此範例中為「鐵達尼號存活者預測工具 (Excel 增益集範例) [分數]」。
   
    ![選取 Web 服務](./media/excel-add-in-for-web-services/image1.png)
3. 這會帶領您到 [預測]**** 區段。  此活頁簿已經包含範例資料，但若為空白的活頁簿，您可在 Excel 中選取一個儲存格並按一下 [使用範例資料] ****。
4. 選取含有標頭的資料並按一下輸入資料範圍圖示。  請確定已核取 [我的資料有標頭] 方塊。
5. 在 [輸出]**** 之下，輸入您想要輸出所在的儲存格編號，例如此處的 "H1"。
6. 按一下 [預測] 。 如果選取 [Auto-predict] \(自動預測\) 核取方塊，所選區域 (指定為輸入的區域) 上的任何變更都會觸發輸出儲存格的要求和更新，而不需要按一下預測按鈕。
   
    ![預測區段](./media/excel-add-in-for-web-services/image1.png)

部署 Web 服務或使用現有的 Web 服務。 如需部署 web 服務的詳細資訊，請參閱 [教學課程3：部署信用風險模型](tutorial-part3-credit-risk-deploy.md)。

取得 Web 服務的 API 金鑰。 執行此動作的位置，取決於您發佈的是傳統 Machine Learning Web 服務或新的 Machine Learning Web 服務。

**使用傳統 web 服務** 

1. 在 Machine Learning Studio (傳統) 中，按一下左窗格中的 [ **WEB 服務** ] 區段，然後選取 web 服務。
   
    ![Studio 選取 Web 服務](./media/excel-add-in-for-web-services/image4.png)
2. 複製 web 服務的 API 金鑰。
   
    ![Studio API 金鑰](./media/excel-add-in-for-web-services/image5.png)
3. 在 web 服務的 [ **儀表板** ] 索引標籤上，按一下 [ **要求/回應** ] 連結。
4. 尋找 [要求 URI] **** 區段。  複製並儲存 URL。

> [!NOTE]
> 您現在可以登入 [Azure Machine Learning Web 服務](https://services.azureml.net) 入口網站，以取得傳統 Machine Learning Web 服務的 API 金鑰。
> 
> 

**使用新的 web 服務**

1. 在 [Azure Machine Learning Web 服務](https://services.azureml.net) 入口網站中，按一下 [ **web 服務**]，然後選取您的 web 服務。 
2. 按一下 [取用] ****。
3. 尋找 [基本使用資訊] **** 區段。 複製並儲存 [主要金鑰]**** 和 [要求-回應]**** URL。

## <a name="steps-to-add-a-new-web-service"></a>新增 Web 服務的步驟

1. 部署 Web 服務或使用現有的 Web 服務。 如需部署 web 服務的詳細資訊，請參閱 [教學課程3：部署信用風險模型](tutorial-part3-credit-risk-deploy.md)。
2. 按一下 [取用] ****。
3. 尋找 [基本使用資訊] **** 區段。 複製並儲存 [主要金鑰]**** 和 [要求-回應]**** URL。
4. 在 Excel 中，移至 [ **Web 服務** ] 區段 (如果您在 [ **預測** ] 區段中，請按一下 [上一步] 箭號以移至 [web 服務] 清單) 。
   
    ![前往 Web 服務](./media/excel-add-in-for-web-services/image3.png)
5. 按一下 [新增 Web 服務] ****。
6. 將 URL 貼到標示為 [URL] **** 的 Excel 增益集文字方塊中。
7. 將 API/主要金鑰貼到標示為 [API 金鑰] **** 的文字方塊中。
8. 按一下 [新增] 。
   
    ![傳統 Web 服務的 URL 和 API 金鑰。](./media/excel-add-in-for-web-services/image6.png)
9. 若要使用 Web 服務，請遵循前述「使用現有 Web 服務的步驟」的指示。

## <a name="sharing-your-workbook"></a>共用活頁簿
如果您儲存您的活頁簿，則您為 Web 服務加入的 API/主要金鑰也會一併儲存。 這表示您應該只與您信任的人共用活頁簿。

如果有任何問題，請在下方的註解區或在我們 [論壇](https://docs.microsoft.com/answers/topics/azure-machine-learning.html)中提出。
