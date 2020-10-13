---
title: 教學課程 - 使用 Azure 入口網站指派新原則
description: 在本教學課程中，您會使用 Azure 入口網站建立 Azure 原則指派，以識別不符合規範的資源。
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826597"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>教學課程：建立原則指派以識別不相容資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 Azure 原則支援使用「來賓設定」原則來稽核已啟用 Arc 的伺服器狀態。 「來賓設定」原則不會套用設定，這些原則只會稽核機器內的設定。 本教學課程會引導您完成建立和指派原則的程序，以識別已啟用 Arc 的伺服器中哪一個已安裝 Log Analytics 代理程式。

在此流程結束時，您會成功識別未安裝適用於 Windows 或 Linux 之 Log Analytics 代理程式的電腦。 它們「不符合」原則指派的規範。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-a-policy-assignment"></a>建立原則指派

在本教學課程中，您會建立原則指派，並且指派 _\[預覽]：_ 「Linux Azure Arc 電腦上應已安裝 Log Analytics 代理程式」原則定義。

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="搜尋所有服務中的原則" border="false":::

1. 選取 Azure 原則分頁左側的 [指派]****。 指派是已指派在特定範圍內發生的原則。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="搜尋所有服務中的原則" border="false":::

1. 從 [原則 - 指派]**** 分頁頂端選取 [指派原則]****。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="搜尋所有服務中的原則" border="false":::

1. 在 [指派原則]**** 頁面上，按一下省略符號並選取管理群組或訂用帳戶來選取 [範圍]****。 選擇性地選取資源群組。 範圍會決定在哪些資源或資源群組上強制執行原則指派。 然後，按一下位於 [範圍]**** 分頁底部的 [選取]****。

   這個範例會使用 **Parnell Aerospace** 訂用帳戶。 您的訂用帳戶不同。

1. 您可以根據**範圍**來排除資源。 **排除項目**會在低於**範圍**層級的層級上啟動。 **排除項目**是選擇性項目，所以目前將其保留為空白。

1. 選取 [原則定義]**** 省略符號以開啟可用定義的清單。 Azure 原則隨附您可以使用的內建原則定義。 有許多可供使用，例如：

   - 強制執行標籤和其值
   - 套用標籤和其值
   - 從資源群組繼承標籤 (若遺漏)

   如需可用內建原則的部分清單，請參閱 [Azure 原則範例](../../../governance/policy/samples/index.md)。

1. 搜尋原則定義清單以尋找 \[預覽]：「您的 Windows Azure Arc 機器上應已安裝 Log Analytics 代理程式」定義，如果您已在 Windows 機器上啟用已啟用 Arc 的伺服器代理程式。 針對以 Linux 電腦，尋找對應的 \[預覽]：「Linux Azure Arc 電腦上應已安裝 Log Analytics 代理程式」原則定義。 按一下該原則，然後按一下 [選取]****。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="搜尋所有服務中的原則" border="false":::

1. [指派名稱]**** 會自動填入您選取的原則名稱，但您可加以變更。 針對此範例，請將 \[預覽]：「Windows Azure Arc 電腦上應已安裝 Log Analytics 代理程式」或\[預覽]：「Linux Azure Arc 電腦上應已安裝 Log Analytics 代理程式」，依您選取的項目而。 您也可以新增選擇性的 [描述]****。 描述會提供有關此原則指派的詳細資料。
   **指派者**將根據登入者自動填入。 這是選擇性欄位，因此可以輸入自訂值。

1. 讓 [建立受控識別]**** 保持未選取狀態。 但是，當要原則或計畫包含具有 [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists) 效果的原則時，_必須_核取此方塊。 由於本快速入門所使用的原則並未包含該效果，因此請將其保留為空白。 如需詳細資訊，請參閱[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)和[補救安全性的運作方式](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)。

1. 按一下 [指派]。

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不符合規範的資源

選取頁面左側的 [合規性]。 然後尋找 \[預覽]：「Windows Azure Arc 電腦上應已安裝 Log Analytics 代理程式」或您建立的 \[預覽]：「Linux Azure Arc 電腦上應已安裝 Log Analytics 代理程式」原則指派。

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="搜尋所有服務中的原則" border="false":::

如果有任何現有資源不符合這個新指派的規範，它們會顯示在 [不符合規範的資源] 下。

根據現有資源評估條件，而且結果為 true 時，這些資源都會標示為不符合原則規範。 下表顯示不同的原則效果如何與結果合規性狀態的條件評估搭配使用。 雖然您在 Azure 入口網站中沒有看到評估邏輯，但是會顯示合規性狀態結果。 合規性狀態結果是符合規範和不符合規範其中之一。

| **資源狀態** | **效果** | **原則評估** | **合規性狀態** |
| --- | --- | --- | --- |
| Exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不符合規範 |
| Exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 相容 |
| 新增 | 稽核、AuditIfNotExist\* | True | 不符合規範 |
| 新增 | 稽核、AuditIfNotExist\* | False | 相容 |

\* Append、DeployIfNotExist 和 AuditIfNotExist 效果需要 IF 陳述式為 TRUE。
這些效果也需要存在條件為 FALSE，以呈現不符合規範。 若為 TRUE，IF 條件會觸發相關資源的存在條件評估。

## <a name="clean-up-resources"></a>清除資源

若要移除所建立的指派，請遵循下列步驟：

1. 選取 Azure 原則頁面左側的 [合規性] (或 [指派])，並找出 **\[預覽]：「Windows Azure Arc 電腦上應已安裝 Log Analytics 代理程式」** 或您建立的 **\[預覽]：「Linux Azure Arc 電腦上應已安裝 Log Analytics 代理程式」** 原則指派。

1. 以滑鼠右鍵按一下原則指派，然後選取 [刪除指派]。

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="搜尋所有服務中的原則" border="false":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您會將原則定義指派至範圍，並評估其更新狀態報告。 原則定義可驗證範圍中的所有資源都符合規範，並且識別哪些資源不符合。 現在您已準備好使用適用於 VM 的 Azure 監視器，監視已啟用 Azure Arc 的伺服器電腦。

若要了解如何從您的電腦監視和檢視效能、執行中程序及其相依性，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [啟用適用於 VM 的 Azure 監視器](tutorial-enable-vm-insights.md)