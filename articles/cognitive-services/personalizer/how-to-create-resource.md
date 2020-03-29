---
title: 建立個人化工具資源
description: 服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336051"
---
# <a name="create-a-personalizer-resource"></a>創建個人化資源

個人化器資源與個人學習迴圈相同。 為您擁有的每個主題域或內容區域創建單個資源或學習迴圈。 不要在同一迴圈中使用多個內容區域，因為這會混淆學習迴圈並提供較差的預測。

如果希望個人化程式為網頁的多個內容區域選擇最佳內容，請為每個內容使用不同的學習迴圈。


## <a name="create-a-resource-in-the-azure-portal"></a>在 Azure 入口網站中建立資源

請針對每個意見反應迴圈建立一個個人化工具資源。

1. 登錄到[Azure 門戶](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 先前的連結會帶您前往個人化工具服務的 [建立]**** 頁面。
1. 輸入服務名稱、選擇訂用帳戶、位置、定價層及資源群組。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure 門戶創建個人化工具資源，也稱為學習迴圈。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. 選擇 **"創建**"以創建資源。

1. 部署資源後，選擇"**轉到資源**"按鈕以轉到個人化程式資源。

1. 選擇資源的 **"快速入門頁**"，然後複製終結點和鍵的值。 您需要資源終結點和金鑰才能使用排名和獎勵 API。

1. 選擇新資源的 **"配置"** 頁以[配置學習迴圈](how-to-settings.md)。

## <a name="create-a-resource-with-the-azure-cli"></a>使用 Azure CLI 創建資源

1. 使用以下命令登錄到 Azure CLI：

    ```azurecli-interactive
    az login
    ```

1. 創建資源組，一個邏輯分組來管理要與個人化工具資源一起使用的所有 Azure 資源。


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 使用現有資源組的以下命令創建新的個人化資源 _（學習迴圈_）。

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    這將返回一個 JSON 物件，其中包括**您的資源終結點**。

1. 使用以下 Azure CLI 命令獲取**資源金鑰**。

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    您需要資源終結點和金鑰才能使用排名和獎勵 API。

## <a name="next-steps"></a>後續步驟

* [配置](how-to-settings.md)個人化學習迴圈