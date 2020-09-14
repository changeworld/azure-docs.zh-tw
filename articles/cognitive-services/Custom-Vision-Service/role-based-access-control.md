---
title: 以角色為基礎的存取控制-自訂視覺
titleSuffix: Azure Cognitive Services
description: 本文將說明如何為您的自訂視覺專案設定角色型存取控制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fc536b3b0c7af113919f274fc4bdd5fad63cbd06
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057168"
---
# <a name="role-based-access-control"></a>角色型存取控制

自訂視覺支援 Azure RBAC) 的 Azure 角色型訪問 (控制，這是用來管理 Azure 資源個別存取的授權系統。 使用 RBAC，您可以為不同的小組成員指派自訂視覺專案的不同許可權層級。 如需 RBAC 的詳細資訊，請參閱 [AZURE rbac 檔](https://docs.microsoft.com/azure/role-based-access-control/)。

## <a name="add-role-assignment-to-custom-vision-resource"></a>將角色指派新增至自訂視覺資源

您可以將 Azure RBAC 指派給自訂視覺資源。 若要授與 Azure 資源的存取權，您可以新增角色指派。
1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，選取 [所有服務]。 
1. 然後選取 **認知服務**，並流覽至您特定的自訂視覺訓練資源。
   > [!NOTE]
   > 您也可以為整個資源群組、訂用帳戶或管理群組設定 RBAC。 若要這麼做，請選取所需的範圍層級，然後流覽至所需的專案 (例如，選取 **資源群組** ，然後按一下您想要的資源群組) 。
1. 選取左側流覽窗格中的 [ **存取控制] (IAM) ** 。
1. 選取 [ **角色指派** ] 索引標籤，以查看此領域的角色指派。
1. 選取 [**新增**  ->  **新增角色指派**]。
1. 在 [ **角色** ] 下拉式清單中，選取您要新增的角色。
1. 在 [選取]**** 清單中，選取使用者、群組、服務主體或受控識別。 如果您在清單中沒有看到安全性主體，您可以鍵入 [選取] 方塊，在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。
1. 選取 [儲存] 以指派角色。

在幾分鐘內，系統就會在選取的範圍中，將選取的角色指派給目標。

## <a name="custom-vision-role-types"></a>自訂視覺角色類型

使用下表來判斷自訂視覺資源的存取需求。

|角色  |權限  |
|---------|---------|
|`Cognitive Service Custom Vision Contributor`     | 專案的完整存取權，包括建立、編輯或刪除專案的能力。        |
|`Cognitive Service Custom Vision Trainer`     | 完整存取權，但建立或刪除專案的功能除外。 定型人員可以查看和編輯專案，以及定型、發行、取消發行或匯出模型。        |
|`Cognitive Service Custom Vision Labeler`     | 能夠上傳、編輯或刪除定型影像，以及建立、新增、移除或刪除標記。 標籤者可查看專案，但無法更新定型影像和標記以外的任何專案。         |
|`Cognitive Service Custom Vision Deployment`     | 發佈、取消發行或匯出模型的能力。 部署人員可查看專案，但無法更新專案、訓練影像或標記。        |
|`Cognitive Service Custom Vision Reader`     | 查看專案的能力。 讀者無法進行任何變更。        |
