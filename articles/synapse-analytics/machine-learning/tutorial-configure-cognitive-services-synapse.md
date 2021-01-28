---
title: 教學課程： Azure Synapse Analytics 中的認知服務必要條件
description: 瞭解如何設定在 Azure Synapse 中使用認知服務的必要條件。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936688"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>教學課程：在 Azure Synapse Analytics 中使用認知服務的必要條件

在本教學課程中，您將瞭解如何設定在 Azure Synapse Analytics 中安全使用 Azure 認知服務的必要條件。

本教學課程涵蓋下列項目：
> [!div class="checklist"]
> - 建立認知服務資源，例如文字分析或異常偵測器。
> - 將驗證金鑰儲存為認知服務資源，以做為 Azure Key Vault 中的秘密，並設定 Azure Synapse Analytics 工作區的存取權。
> - 在您的 Azure Synapse Analytics 工作區中建立 Azure Key Vault 連結服務。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md) ，具有設定為預設儲存體的 Azure Data Lake Storage Gen2 儲存體帳戶。 您必須是您所使用 Azure Data Lake Storage Gen2 檔案系統的 *儲存體 Blob 資料參與者* 。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

[Azure 認知服務](../../cognitive-services/index.yml) 包含許多類型的服務。 文字分析和異常偵測器是 Azure Synapse 教學課程中的兩個範例。

您可以在 Azure 入口網站中建立 [文字分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 資源：

![在入口網站中使用 [建立] 按鈕顯示文字分析的螢幕擷取畫面。](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

您可以在 Azure 入口網站中建立 [異常](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 偵測器資源：

![在入口網站中使用 [建立] 按鈕顯示異常偵測器的螢幕擷取畫面。](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>建立金鑰保存庫，並設定秘密和存取權

1. 在 Azure 入口網站中建立 [金鑰保存庫](https://ms.portal.azure.com/#create/Microsoft.KeyVault) 。
2. 移至 **Key Vault**  >  **存取原則**，並授與 [Azure Synapse 工作區 MSI](../security/synapse-workspace-managed-identity.md)許可權，以從 Azure Key Vault 讀取秘密。

   > [!NOTE]
   > 請確定已儲存原則變更。 此步驟很容易遺漏。

   ![顯示用於新增存取原則之選項的螢幕擷取畫面。](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. 移至您的認知服務資源。 例如，移至 **異常** 偵測器  >  **金鑰和端點**。 然後將兩個金鑰的其中一個複製到剪貼簿。

4. 移至 **Key Vault**  >  **secret** 以建立新的密碼。 指定密碼的名稱，然後在 [ **值** ] 欄位中貼上上一個步驟中的金鑰。 最後，選取 [建立]。

   ![顯示用於建立秘密之選項的螢幕擷取畫面。](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > 請確定您記得或記下此秘密名稱。 稍後當您從 Azure Synapse Studio 連線到認知服務時，將會用到它。

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>在 Azure Synapse 中建立 Azure Key Vault 連結服務

1. 在 Azure Synapse Studio 中開啟您的工作區。 
2. 移至 [**管理**  >  **連結的服務**]。 指向您剛才建立的金鑰保存庫，以建立 **Azure Key Vault** 連結的服務。 
3. 選取 [ **測試連接** ] 按鈕來驗證連接。 如果連接為綠色，請選取 [ **建立** ]，然後選取 [ **全部發佈** ] 以儲存您的變更。

![以新的連結服務顯示 Azure Key Vault 的螢幕擷取畫面。](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

您現在已準備好繼續使用 Azure Synapse Studio 中的 Azure 認知服務體驗的其中一個教學課程。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用認知服務進行情感分析](tutorial-cognitive-services-sentiment.md)
- [教學課程：使用認知服務的異常偵測](tutorial-cognitive-services-sentiment.md)
- [教學課程：Azure Synapse 專用 SQL 集區的機器學習模型評分](tutorial-sql-pool-model-scoring-wizard.md)。
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)