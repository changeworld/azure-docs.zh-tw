---
title: 管理模型和學習設定-個人化工具
description: 您可以在自己的原始檔控制系統中匯出機器學習模型和學習設定，以進行備份。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: f82adad5273f1c5559cbeb1924f59e57c863f6b6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303449"
---
# <a name="how-to-manage-model-and-learning-settings"></a>如何管理模型和學習設定

您可以在自己的原始檔控制系統中匯出機器學習模型和學習設定，以進行備份。

## <a name="export-the-personalizer-model"></a>匯出個人化工具模型

從資源管理的 **模型和學習設定**區段中，檢查模型建立和上次更新日期，然後匯出目前的模型。 您可以使用 Azure 入口網站或個人化工具 API 來匯出要加以封存的模型檔案。

![匯出目前的個人化工具模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除學習迴圈的資料

1. 在 Azure 入口網站中，針對您的個人化工具資源，請在 [ **模型和學習設定** ] 頁面上選取 [ **清除資料**]。
1. 若要清除所有資料，並將學習迴圈重設為原始狀態，請選取全部3個核取方塊。

    ![在 Azure 入口網站中，清除個人化工具資源中的資料。](./media/settings/clear-data-from-personalizer-resource.png)

    |值|目的|
    |--|--|
    |記錄個人化和獎勵資料。|這項記錄資料會用於離線評估。 如果您要重設資源，請清除資料。|
    |重設個人化工具模型。|此模型會在每次重新定型時變更。 這種定型頻率是在 [設定 **] 頁面上的** **[上傳模型頻率**] 中指定。 |
    |將學習原則設定為預設值。|如果您已將學習原則變更為離線評估的一部分，這會重設為原始的學習原則。|

1. 選取 [ **清除選取的資料** ] 以開始清除處理常式。 狀態會在 Azure 通知的右上方導覽中回報。

## <a name="import-a-new-learning-policy"></a>匯入新的學習原則

[學習原則](concept-active-learning.md#understand-learning-policy-settings)設定會決定模型定型的_超參數_。 執行 [離線評估](how-to-offline-evaluation.md) 以找出新的學習原則。

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後選取您的個人化工具資源。
1. 選取 [**資源管理**] 區段中的 [**模型和學習設定**]。
1. 在 [匯 **入學習設定** ] 中，選取您使用上述指定的 JSON 格式建立的檔案，然後選取 [ **上傳** ] 按鈕。

    等候已成功上傳學習原則的通知。

## <a name="export-a-learning-policy"></a>匯出學習原則

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後選取您的個人化工具資源。
1. 選取 [**資源管理**] 區段中的 [**模型和學習設定**]。
1. 針對 [匯 **入學習設定** ]，選取 [ **匯出學習設定** ] 按鈕。 這會將檔案儲存 `json` 到您的本機電腦。

## <a name="next-steps"></a>後續步驟

[瞭解如何管理學習原則](how-to-manage-model.md)
