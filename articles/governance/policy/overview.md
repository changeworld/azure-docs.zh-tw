---
title: Azure 原則的概觀
description: Azure 原則是 Azure 中的一個服務，您可以在 Azure 環境中用來建立、指派和管理原則定義。
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937824"
---
# <a name="what-is-azure-policy"></a>什麼是 Azure 原則？

控管可驗證貴組織是否能夠透過富有效率及效益的 IT 使用方式來達成其目標。 透過在商務目標與 IT 專案之間建立明確見地符合此需求。

您公司是否遇到似乎永遠無法解決的大量 IT 問題？ 良好的 IT 控管包括規劃您的方案，以及在策略層級上設定優先順序，以協助管理及避免問題。 此策略性需求是 Azure 原則存在的理由。

Azure 原則是 Azure 中的一個服務，您可以用來建立、指派和管理原則。 這些原則會對您的資源強制執行不同的規則和效果，讓這些資源能符合公司標準和服務等級協定的規範。 Azure 原則會透過評估您的資源是否符合指派的策略來滿足此需求。 透過 Azure 原則儲存的所有資料都會進行待用加密。

例如，您可以設定原則，在環境中只允許特定 SKU 大小的虛擬機器。 實作此原則之後，會評估新資源和現有資源的合規性。 使用適當類型的原則，現有的資源就可以合規。 稍後在本文件中，我們將介紹更多關於如何使用 Azure 原則來建立及實作原則的詳細資料。

> [!IMPORTANT]
> 無論定價層為何，所有指派現在都會提供 Azure 原則的合規性評估。 如果指派未顯示合規性資料，請確定已向 Microsoft.PolicyInsights 資源提供者註冊訂用帳戶。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>它和 RBAC 有什麼不同？

Azure 原則和角色型存取控制 (RBAC) 之間有幾個主要差異。 RBAC 著重於不同範圍的使用者動作。 您可能會被加入資源群組的參與者角色中，讓您對該資源群組進行變更。 Azure 原則著重於部署期間及既有資源的資源屬性。 Azure 原則會控制屬性，例如資源的類型或位置。 不同於 RBAC，Azure 原則是預設允許且明確拒絕的系統。

### <a name="rbac-permissions-in-azure-policy"></a>Azure 原則中的 RBAC 權限

Azure 原則在下列兩個資源提供者中有數個權限，一般稱之為作業：

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

許多內建角色都會授與 Azure 原則資源的權限。 [資源原則參與者]  角色包含大部分的 Azure 原則作業。 **擁有者**則具有完整權限。 **參與者**和**讀取者**都可以使用所有讀取的 Azure 原則作業，但**參與者**也可以觸發修復。

沒有任何內建角色具有所需的權限，請建立[自訂角色](../../role-based-access-control/custom-roles.md)。

## <a name="policy-definition"></a>原則定義

建立及實作 Azure 原則中的原則始於建立原則定義。 每個原則定義都有其強制執行的條件。 此外，還有已定義的效果，會在符合條件時發生。

在 Azure 原則中，我們預設提供數個可供使用的內建原則。 例如：

- **允許的儲存體帳戶 SKU**：確定要部署的儲存體帳戶是否在一組 SKU 大小內。 其效果是拒絕不符合已定義 SKU 大小集合的所有儲存體帳戶。
- **允許的資源類型**：定義您可以部署的資源類型。 其效果是拒絕不屬於此定義清單的所有資源。
- **允許的位置**：限制新資源的可用位置。 其效果可用來強制執行您的地理合規性需求。
- **允許的虛擬機器 SKU**：指定您可以部署的一組虛擬機器 SKU。
- **將標籤新增至資源**：部署要求未指定時，套用必要的標籤及其預設值。
- **強制執行標籤及其值**：對資源強制執行必要的標籤及其值。
- **不允許的資源類型**：防止部署資源類型清單。

若要實作這些原則定義 (內建和自訂定義)，您必須指派它們。 您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來指派任何這些原則。

原則評估會透過數個不同的動作進行，例如原則指派或原則更新。 如需完整清單，請參閱[原則評估觸發程序](./how-to/get-compliance-data.md#evaluation-triggers)。

若要深入了解原則定義的結構，請檢閱[原則定義結構](./concepts/definition-structure.md)。

## <a name="policy-assignment"></a>原則指派

原則指派是已指派在特定範圍內發生的原則定義。 此範圍可從[管理群組](../management-groups/overview.md)到個別資源。 「範圍」  一詞係指作為原則定義指派對象的所有資源、資源群組、訂用帳戶或管理群組。 原則指派會由所有子資源繼承。 此設計的用意是，套用至某個資源群組的原則也會套用至該資源群組中的資源。 不過，您可以從原則指派中排除某個子範圍。

例如，在訂用帳戶範圍，您可以指派一個防止建立網路資源的原則。 您可以將該訂用帳戶內一個要用於網路基礎結構的資源群組排除。 接著，您要將此網路資源群組的存取權授與您所信任來建立網路資源的使用者。

在另一個範例中，建議您在管理群組層級指派資源類型允許清單原則。 然後，在子管理群組或甚至直接在訂用帳戶上指派更寬鬆的原則 (允許更多資源類型)。 不過，因為原則是明確的拒絕系統，此範例沒有作用。 相反地，您需要從管理群組層級的原則指派中排除子管理群組或訂用帳戶。 然後，對子管理群組或訂用帳戶層級指派更寬鬆的原則。 如果有任何原則導致系統拒絕資源，則唯一能允許資源的方法是修改拒絕原則。

如需透過入口網站設定原則定義和指派的詳細資訊，請參閱[建立原則指派，以識別 Azure 環境中不符合規範的資源](assign-policy-portal.md)。 此外，還會提供適用於 [PowerShell](assign-policy-powershell.md) 和 [Azure CLI](assign-policy-azurecli.md) 的步驟。

## <a name="policy-parameters"></a>原則參數

原則參數減少您必須建立的原則定義數量，有助於簡化原則管理。 在建立更通用的原則定義時，您可以定義參數。 接著，您便可以針對不同的案例，重複使用該原則定義。 若要這麼做，只要在指派原則定義時傳入不同的值即可。 例如，為訂用帳戶指定一組位置。

參數是在建立原則定義時定義的。 定義參數時，需要指定名稱並選擇性地指定值。 例如，您可以為原則定義一個標題為 *location* 的參數。 接著，您可以在指派原則時給予它不同的值，例如 *EastUS* 或 *WestUS*。

如需有關原則參數的詳細資訊，請參閱[定義結構 - 參數](./concepts/definition-structure.md#parameters)。

## <a name="initiative-definition"></a>計畫定義

計畫定義是針對達到單一主要目標而量身打造的原則定義集合。 計畫定義可讓管理及指派原則定義更為簡單。 其簡化方式是將一組原則組成單一項目。 例如，您可以建立一個標題為**在 Azure 資訊安全中心啟用監視**的計畫，目標是要在您的 Azure 資訊安全中心監視所有可用的安全性建議。

> [!NOTE]
> SDK (例如 Azure CLI 和 Azure PowerShell) 會使用名為 **PolicySet** 的屬性和參數來參考計畫。

在這項計畫之下，您可能會有如下的原則定義：

- **在資訊安全中心監視未加密的 SQL Database** – 適用於監視未加密的 SQL 資料庫和伺服器。
- **在資訊安全中心監視作業系統弱點** – 適用於監視不符合設定基準的伺服器。
- **在資訊安全中心監視遺失的 Endpoint Protection** – 適用於監視沒有安裝 Endpoint Protection 代理程式的伺服器。

## <a name="initiative-assignment"></a>計畫指派

類似原則指派，計畫指派是指派至特定範圍的計畫定義。 計畫指派會減少為每個範圍進行多個計畫定義的需求。 此範圍也可從管理群組到個別資源。

每個計畫都會獲指派不同的範圍。 一個計畫可以同時指派給 **subscriptionA** 和 **subscriptionB**。

## <a name="initiative-parameters"></a>計畫參數

類似原則參數，計畫參數減少重複性，有助於簡化計畫管理。 計畫參數是計畫內原則定義所要使用的參數。

例如，以具有計畫定義的案例 - **initiativeC** 來說，其中包含原則定義 **policyA** 和 **policyB**，每個都必須有不同類型的參數：

| 原則 | 參數名稱 |參數類型  |附註 |
|---|---|---|---|
| policyA | allowedLocations | array  |因為參數類型已定義為陣列，所以此參數應該要有一個字串清單作為值 |
| policyB | allowedSingleLocation |字串 |因為參數類型已定義為字串，所以此參數應該要有一個字組作為值 |

在此案例中，定義 **initiativeC** 的計畫參數時，有三個選項：

- 使用此計畫內原則定義的參數：在此範例中，*allowedLocations* 和 *allowedSingleLocation* 會變成 **initiativeC** 的計畫參數。
- 提供值給此計畫定義內原則定義的參數。 在此範例中，您可以提供位置清單給 **policyA 的參數 – allowedLocations** 和 **policyB 的參數 – allowedSingleLocation**。 您也可以在指派此計畫時提供值。
- 提供指派此計畫時可以使用的*值*選項清單。 當您指派此計畫時，從計畫內原則定義繼承的參數，只能有來自此提供清單中的值。

建立計畫定義中的值選項時，您無法在計畫指派期間輸入不同的值，因為它不是清單的一部分。

## <a name="maximum-count-of-azure-policy-objects"></a>Azure 原則物件的最大計數

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>管理原則的建議

以下是要牢記在心的幾個指標和秘訣：

- 從稽核效果開始，而不是從追蹤環境中原則定義對資源之影響的拒絕效果開始。 如果您已經有可自動調整應用程式的指令碼，設定拒絕效果可能會妨礙您既有的這類自動化工作。

- 在建立定義和指派時，請考慮組織階層。 建議您在較高的層級建立定義，例如管理群組或訂用帳戶層級。 接著，在下一個子層級建立指派。 如果您在管理群組建立定義，可以將指派的範圍縮小為該管理群組內的訂用帳戶或資源群組。

- 即使是針對單一原則定義，也建議您建立並指派計畫定義。
  例如，您有原則定義 *policyDefA*，並建立計畫定義 *initiativeDefC* 下建立該定義。 如果您稍後針對 *policyDefB* 建立另一個目標類似 *policyDefA* 的原則定義，您可以在 *initiativeDefC* 底下加入該定義，並一起進行追蹤。

- 一旦您建立了計畫指派之後，則加入至計畫的原則定義也會成為該計畫指派的一部分。

- 評估計畫指派時，也會評估該計畫內的所有原則。
  如果您需要個別評估原則，最好不要包含在計畫中。

## <a name="video-overview"></a>影片概觀

以下 Azure 原則的概觀是從組建 2018 開始。 如需投影片或影片的下載，請瀏覽 Channel 9 上的[透過 Azure 原則控管您的 Azure 環境](https://channel9.msdn.com/events/Build/2018/THR2030)。

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>後續步驟

現在您已大概了解 Azure 原則，以及一些重要概念，以下是建議的後續步驟：

- [使用入口網站指派原則定義](./assign-policy-portal.md)。
- [使用 Azure CLI 指派原則定義](./assign-policy-azurecli.md)。
- [使用 PowerShell 指派原則定義](./assign-policy-powershell.md)。
