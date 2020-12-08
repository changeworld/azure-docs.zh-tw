---
title: 教學課程：Azure Synapse 中認知服務的必要條件
description: 如何在 Azure Synapse 中設定使用認知服務必要條件的教學課程
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: cf76a3d09da83693791873feb9e6ab03c432e47f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463622"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>教學課程：在 Azure Synapse 中使用認知服務的必要條件

在本教學課程中，您將了解如何設定必要元件，以安全地運用 Azure Synapse 中的認知服務。

本教學課程涵蓋下列項目：
> [!div class="checklist"]
> - 建立認知服務資源。 例如文字分析或異常偵測器。
> - 將認知服務資源的驗證金鑰儲存為 Azure Key Vault 中的祕密，並設定 Azure Synapse 工作區的存取權。
> - 在 Azure Synapse Analytics 工作區中建立 Azure Key Vault 連結服務。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md)，並將 ADLS Gen2 儲存體帳戶設定為預設儲存體。 您必須是所要使用 ADLS Gen2 檔案系統的 **儲存體 Blob 資料參與者**。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

[Azure 認知服務](https://go.microsoft.com/fwlink/?linkid=2147492)包含許多不同類型的服務。 以下是 Synapse 教學課程中所使用的一些範例。

### <a name="create-an-anomaly-detector-resource"></a>建立 Anomaly Detector 資源
在 Azure 入口網站中建立[異常偵測器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。

![建立異常偵測器](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>建立文字分析資源
在 Azure 入口網站中建立[文字分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)資源。

![建立文字分析](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>建立 Key Vault 並設定祕密和存取

1. 在 Azure 入口網站中建立 [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault)。
2. 移至 **Key Vault -> 存取原則**，並授與 [Azure Synapse 工作區 MSI](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-identity) 權限以讀取 Azure Key Vault 中的祕密。

>請確定已儲存原則變更。 此步驟很容易遺漏。

![新增存取原則](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. 移至您的認知服務資源，例如 **異常偵測器 -> 金鑰和端點**，將兩個金鑰的其中一個複製到剪貼簿。

4. 移至 **Key Vault -> 祕密** 以建立新的祕密。 指定祕密的名稱，然後將上一個步驟中的金鑰貼到 [值] 欄位中。 最後，按一下 [建立]。

![建立祕密](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> 請務必記住或記下此祕密名稱！ 稍後從 Azure Synapse Studio 連線到認知服務時，將會用到此名稱。

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>在 Azure Synapse 中建立 Azure Key Vault 連結服務

1. 在 Azure Synapse Studio 中開啟您的工作區。 瀏覽至 **管理 -> 連結服務**。 建立 Azure Key Vault 連結服務，指向我們剛才建立的 Key Vault。 然後按一下 [測試連線] 按鈕並檢查是否顯示為綠色，以確認連線。 如果一切正常運作，請先按一下 [建立]，然後按一下 [全部發佈] 以儲存變更。
![連結服務](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

您現在已準備好繼續進行在 Azure Synapse Studio 中使用 Azure 認知服務體驗的其中一個教學課程。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用認知服務進行情感分析](tutorial-cognitive-services-sentiment.md)
- [教學課程：使用認知服務的異常偵測](tutorial-cognitive-services-sentiment.md)
- [教學課程：Azure Synapse 專用 SQL 集區的機器學習模型評分](tutorial-sql-pool-model-scoring-wizard.md)。
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)