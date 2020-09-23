---
title: 建立個人化工具資源
description: 在本文中，您將瞭解如何在 Azure 入口網站中建立每個意見反應迴圈的個人化工具資源。
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7965fb1eaa3efbe1f47db4eedd88e4444b0e610b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985819"
---
# <a name="create-a-personalizer-resource"></a>建立個人化工具資源

個人化工具資源與個人化工具學習迴圈一樣。 系統會為您擁有的每個主旨網域或內容區域建立單一資源或學習迴圈。 請勿在同一個迴圈中使用多個內容區域，因為這會使學習迴圈混淆，並提供不佳的預測。

如果您想要讓個人化工具為網頁的多個內容區域選取最佳內容，請針對每個頁面使用不同的學習迴圈。


## <a name="create-a-resource-in-the-azure-portal"></a>在 Azure 入口網站中建立資源

請針對每個意見反應迴圈建立一個個人化工具資源。

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 先前的連結會帶您前往個人化工具服務的 [建立]**** 頁面。
1. 輸入服務名稱、選擇訂用帳戶、位置、定價層及資源群組。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure 入口網站來建立個人化工具資源，也稱為學習迴圈。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. 選取 [ **建立** ] 以建立資源。

1. 部署資源之後，請選取 [ **移至資源** ] 按鈕以移至您的個人化工具資源。

1. 選取資源的 [ **快速入門** ] 頁面，然後複製您的端點和金鑰的值。 您需要資源端點和金鑰，才能使用排名和獎勵 Api。

1. 選取新資源的 [設定 **] 頁面，** 以 [設定學習迴圈](how-to-settings.md)。

## <a name="create-a-resource-with-the-azure-cli"></a>使用 Azure CLI 建立資源

1. 使用下列命令登入 Azure CLI：

    ```azurecli-interactive
    az login
    ```

1. 建立資源群組，這是一個邏輯群組，用來管理您想要搭配個人化工具資源使用的所有 Azure 資源。


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 使用適用于現有資源群組的下列命令，建立新的個人化工具資源、 _學習迴圈_。

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    這會傳回 JSON 物件，其中包含您的 **資源端點**。

1. 使用下列 Azure CLI 命令來取得您的 **資源金鑰**。

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    您需要資源端點和金鑰，才能使用排名和獎勵 Api。

## <a name="next-steps"></a>下一步

* [設定](how-to-settings.md) 個人化工具學習迴圈
