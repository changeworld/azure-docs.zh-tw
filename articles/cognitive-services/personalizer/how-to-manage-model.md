---
title: 管理模型和學習設定-個人化工具
description: 您可以匯出機器學習的模型和學習設定，以便在您自己的原始檔控制系統中進行備份。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624328"
---
# <a name="how-to-manage-model-and-learning-settings"></a>如何管理模型和學習設定

您可以匯出機器學習的模型和學習設定，以便在您自己的原始檔控制系統中進行備份。

## <a name="export-the-personalizer-model"></a>匯出個人化工具模型

從 [**模型和學習設定**] 的 [資源管理] 區段中，查看 [模型建立] 和 [上次更新日期]，然後匯出目前的模型。 您可以使用 Azure 入口網站或個人化工具 API 來匯出要加以封存的模型檔案。

![匯出目前的個人化工具模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除學習迴圈的資料

1. 在 Azure 入口網站中，針對您的個人化工具資源，在 [**模型和學習設定**] 頁面上，選取 [**清除資料**]。
1. 若要清除所有資料，並將學習迴圈重設為原始狀態，請選取所有3個核取方塊。

    ![在 Azure 入口網站中，清除個人化工具資源中的資料。](./media/settings/clear-data-from-personalizer-resource.png)

    |值|目的|
    |--|--|
    |記錄的個人化和報酬資料。|此記錄資料會用於離線評估。 如果您要重設資源，請清除資料。|
    |重設個人化工具模型。|此模型會在每次重新定型時變更。 此定型頻率**會在 [設定] 頁面上**的 **[上傳模型頻率**] 中指定。 |
    |將 [學習原則] 設定為 [預設]。|如果您已將學習原則變更為離線評估的一部分，則會重設為原始學習原則。|

1. 選取 [**清除選取的資料**] 以開始清除進程。 狀態會在 Azure 通知的右上方導覽中回報。

## <a name="import-a-new-learning-policy"></a>匯入新的學習原則

[學習原則](concept-active-learning.md#understand-learning-policy-settings)設定會決定模型定型的_超參數_。 執行[離線評估](how-to-offline-evaluation.md)以尋找新的學習原則。

1. 開啟 [ [Azure 入口網站](https://portal.azure.com)]，然後選取您的個人化工具資源。
1. 在 [**資源管理**] 區段中選取 [**模型和學習設定**]。
1. 針對 [匯**入學習設定**]，選取您使用上面指定的 JSON 格式建立的檔案，然後選取 [**上傳**] 按鈕。

    等候學習原則上傳成功的通知。

## <a name="export-a-learning-policy"></a>匯出學習原則

1. 開啟 [ [Azure 入口網站](https://portal.azure.com)]，然後選取您的個人化工具資源。
1. 在 [**資源管理**] 區段中選取 [**模型和學習設定**]。
1. 針對 [匯**入學習設定**]，選取 [**匯出學習設定**] 按鈕。 這會將 `json` 檔案儲存到您的本機電腦。

## <a name="next-steps"></a>後續步驟

[瞭解如何管理學習原則](how-to-manage-model.md)
