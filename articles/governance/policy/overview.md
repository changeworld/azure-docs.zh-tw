---
title: Azure 原則的概觀
description: Azure 原則是 Azure 中的一個服務，您可以在 Azure 環境中用來建立、指派和管理原則定義。
ms.date: 10/05/2020
ms.topic: overview
ms.openlocfilehash: 96fd29b5e3d24bc3e678461a95005e004a8a3a80
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803947"
---
# <a name="what-is-azure-policy"></a>什麼是 Azure 原則？

Azure 原則有助於強制執行組織標準及大規模評估合規性。 其合規性儀表板會提供彙總檢視，以評估環境的整體狀態，並能夠向下切入至每個資源和每個原則的細微性。 也可透過對現有資源進行大規模補救，以及自動對新資源進行補救來協助您的資源達到合規性。

Azure 原則的常見使用案例包括針對資源一致性、法規合規性、安全性、成本和管理來進行治理。 這些常見使用案例的原則定義已內建在您的 Azure 環境中，可協助您開始進行作業。

所有 Azure 原則的資料和物件都會在待用時加密。 如需詳細資訊，請參閱 [Azure 待用資料加密](../../security/fundamentals/encryption-atrest.md)。

## <a name="overview"></a>概觀

Azure 原則會將這些資源屬性與商務規則做比較，藉此評估 Azure 中的資源。 這些以 [JSON 格式](./concepts/definition-structure.md)描述的商務規則稱為[原則定義](#policy-definition)。 為了簡化管理，您可以將數個商務規則結合在群組中，以形成[原則計畫](#initiative-definition) (有時稱為 policySet)。 當您的商務規則形成之後，原則定義或計畫就會[指派](#assignments)到 Azure 支援的任何資源範圍，例如[管理群組](../management-groups/overview.md)、訂用帳戶、[資源群組](../../azure-resource-manager/management/overview.md#resource-groups)或個別資源。 指派會套用至該指派 [Resource Manager 範圍](../../azure-resource-manager/management/overview.md#understand-scope)內的所有資源。 如有必要，您可以排除子範圍。 如需詳細資訊，請參閱 [Azure 原則中的範圍](./concepts/scope.md)。

Azure 原則會使用 [JSON 格式](./concepts/definition-structure.md)來形成評估作業用來判斷資源是否符合規範的邏輯。 定義包括中繼資料和原則規則。 定義的規則可使用函式、參數、邏輯運算子、條件和屬性[別名](./concepts/definition-structure.md#aliases)，來確切符合您想要的情況。 原則規則會決定指派範圍中的哪些資源要受到評估。

### <a name="understand-evaluation-outcomes"></a>了解評估結果

資源可在資源生命週期及原則指派生命週期中的特定時間點受到評估，也可以進行週期性的合規性評估。 以下是會導致資源受到評估的時間或事件：

- 在具有原則指派的範圍中建立、更新或刪除資源。
- 將新的原則或計畫指派給範圍。
- 更新已指派給範圍的原則或計畫。
- 標準合規性的評估週期期間 (每隔24小時發生一次)。

若要深入了解原則評估發生的時機和方式，請參閱[評估觸發程序](./how-to/get-compliance-data.md#evaluation-triggers)。

### <a name="control-the-response-to-an-evaluation"></a>控制評估的回應

各組織用來處理非合規資源的商務規則有很大的差異。 組織希望平台如何回應非合規資源的範例包括：

- 拒絕資源變更
- 記錄資源變更
- 變更之前改變資源
- 變更之後變更資源
- 部署相關的合規性資源

Azure 原則會透過套用[效果](./concepts/effects.md)，讓每個商務回應都是可行的。 效果會在 [原則定義](./concepts/definition-structure.md)的 **原則規則** 部分中設定。

### <a name="remediate-non-compliant-resources"></a>補救不相容的資源

雖然這些效果主要會在建立或更新資源時影響資源，但 Azure 原則也支援處理現有的非合規資源，而不需要改變該資源。 如需讓現有資源符合規範的詳細資訊，請參閱[補救資源](./how-to/remediate-resources.md)。

### <a name="video-overview"></a>影片概觀

以下 Azure 原則的概觀是從組建 2018 開始。 如需投影片或影片的下載，請瀏覽 Channel 9 上的[透過 Azure 原則控管您的 Azure 環境](https://channel9.msdn.com/events/Build/2018/THR2030)。

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>開始使用

### <a name="azure-policy-and-azure-rbac"></a>Azure 原則與 Azure RBAC

Azure 原則和 Azure 角色型存取控制 (Azure RBAC) 之間有幾個主要差異。 Azure 原則會藉由檢查 Resource Manager 中呈現的資源屬性及部分資源提供者的屬性，來評估狀態。 Azure 原則不會限制動作 (也稱為「作業」)。 Azure 原則可確保資源狀態遵循您的商務規則，而不需要擔心誰進行了變更或誰有權進行變更。

Azure RBAC 著重於管理不同範圍的使用者[動作](../../role-based-access-control/resource-provider-operations.md)。 如果需要控制動作，則 Azure RBAC 是您應使用的正確工具。 即使個人擁有執行動作的存取權，但結果若是不符合規範的資源，Azure 原則仍會封鎖建立或更新動作。

Azure RBAC 和 Azure 原則的結合，可在 Azure 中提供完整的範圍控制。

### <a name="azure-rbac-permissions-in-azure-policy"></a>Azure 原則中的 Azure RBAC 權限

Azure 原則在下列兩個資源提供者中有數個權限，一般稱之為作業：

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

許多內建角色都會授與 Azure 原則資源的權限。 [資源原則參與者] 角色包含大部分的 Azure 原則作業。 **擁有者** 則具有完整權限。 **參與者** 和 **讀者** 都有權「讀取」 Azure 原則作業。 **參與者** 可以觸發資源補救，但無法「建立」定義或指派。 **使用者存取管理員** 必須授與 **deployIfNotExists** 或 **modify** 指派的受控識別所需的權限。

沒有任何內建角色具有所需的權限，請建立[自訂角色](../../role-based-access-control/custom-roles.md)。

> [!NOTE]
> **deployIfNotExists** 或 **modify** 原則指派的受控識別需要足夠的權限，才可建立或更新目標資源。 如需詳細資訊，請參閱[設定用於補救的原則定義](./how-to/remediate-resources.md#configure-policy-definition)。

### <a name="resources-covered-by-azure-policy"></a>Azure 原則所涵蓋的資源

Azure 原則會評估 Azure 中的所有資源和已啟用 Arc 的資源。 某些資源提供者 (例如[來賓設定](./concepts/guest-configuration.md)、[Azure Kubernetes Service](../../aks/intro-kubernetes.md) 和 [Azure Key Vault](../../key-vault/general/overview.md)) 會有更深入的管理設定和物件整合。 若要深入了解，請參閱[資源提供者模式](./concepts/definition-structure.md)。

### <a name="recommendations-for-managing-policies"></a>管理原則的建議

以下是要牢記在心的幾個指標和秘訣：

- 從稽核效果開始，而不是從追蹤環境中原則定義對資源之影響的拒絕效果開始。 如果您已經有可自動調整應用程式的指令碼，設定拒絕效果可能會妨礙您既有的這類自動化工作。

- 在建立定義和指派時，請考慮組織階層。 建議您在較高的層級建立定義，例如管理群組或訂用帳戶層級。 接著，在下一個子層級建立指派。 如果您在管理群組建立定義，可以將指派的範圍縮小為該管理群組內的訂用帳戶或資源群組。

- 即使是針對單一原則定義，也建議您建立並指派計畫定義。
  例如，您有原則定義 _policyDefA_，並建立計畫定義 _initiativeDefC_ 下建立該定義。 如果您稍後針對 _policyDefB_ 建立另一個目標類似 _policyDefA_ 的原則定義，您可以在 _initiativeDefC_ 底下加入該定義，並一起進行追蹤。

- 一旦您建立了計畫指派之後，則加入至計畫的原則定義也會成為該計畫指派的一部分。

- 評估計畫指派時，也會評估該計畫內的所有原則。
  如果您需要個別評估原則，最好不要包含在計畫中。

## <a name="azure-policy-objects"></a>Azure 原則物件

### <a name="policy-definition"></a>原則定義

建立及實作 Azure 原則中的原則始於建立原則定義。 每個原則定義都有其強制執行的條件。 此外，還有已定義的效果，會在符合條件時發生。

在 Azure 原則中，我們預設提供數個可供使用的內建原則。 例如：

- **允許的儲存體帳戶 SKU** (拒絕)：確定要部署的儲存體帳戶是否在一組 SKU 大小內。 其效果是拒絕不符合已定義 SKU 大小集合的所有儲存體帳戶。
- **允許的資源類型** (拒絕)：定義您可以部署的資源類型。 其效果是拒絕不屬於此定義清單的所有資源。
- **允許的位置** (拒絕)：限制新資源的可用位置。 其效果可用來強制執行您的地理合規性需求。
- **允許的虛擬機器 SKU** (拒絕)：指定您可以部署的一組虛擬機器 SKU。
- **將標籤新增至資源** (修改)：部署要求未指定時，套用必要的標籤及其預設值。
- **不允許的資源類型** (拒絕)：防止部署資源類型清單。

若要實作這些原則定義 (內建和自訂定義)，您必須指派它們。 您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來指派任何這些原則。

原則評估會透過數個不同的動作進行，例如原則指派或原則更新。 如需完整清單，請參閱[原則評估觸發程序](./how-to/get-compliance-data.md#evaluation-triggers)。

若要深入了解原則定義的結構，請檢閱[原則定義結構](./concepts/definition-structure.md)。

原則參數減少您必須建立的原則定義數量，有助於簡化原則管理。 在建立更通用的原則定義時，您可以定義參數。 接著，您便可以針對不同的案例，重複使用該原則定義。 若要這麼做，只要在指派原則定義時傳入不同的值即可。 例如，為訂用帳戶指定一組位置。

參數是在建立原則定義時定義的。 定義參數時，需要指定名稱並選擇性地指定值。 例如，您可以為原則定義一個標題為 _location_ 的參數。 接著，您可以在指派原則時給予它不同的值，例如 _EastUS_ 或 _WestUS_。

如需有關原則參數的詳細資訊，請參閱[定義結構 - 參數](./concepts/definition-structure.md#parameters)。

### <a name="initiative-definition"></a>計畫定義

計畫定義是針對達到單一主要目標而量身打造的原則定義集合。 計畫定義可讓管理及指派原則定義更為簡單。 其簡化方式是將一組原則組成單一項目。 例如，您可以建立一個標題為 **在 Azure 資訊安全中心啟用監視** 的計畫，目標是要在您的 Azure 資訊安全中心監視所有可用的安全性建議。

> [!NOTE]
> SDK (例如 Azure CLI 和 Azure PowerShell) 會使用名為 **PolicySet** 的屬性和參數來參考計畫。

在這項計畫之下，您可能會有如下的原則定義：

- **在資訊安全中心監視未加密的 SQL Database** – 適用於監視未加密的 SQL 資料庫和伺服器。
- **在資訊安全中心監視作業系統弱點** – 適用於監視不符合設定基準的伺服器。
- **在資訊安全中心監視遺失的 Endpoint Protection** – 適用於監視沒有安裝 Endpoint Protection 代理程式的伺服器。

類似原則參數，計畫參數減少重複性，有助於簡化計畫管理。 計畫參數是計畫內原則定義所要使用的參數。

例如，以具有計畫定義的案例 - **initiativeC** 來說，其中包含原則定義 **policyA** 和 **policyB**，每個都必須有不同類型的參數：

| 原則 | 參數名稱 |參數類型  |附註 |
|---|---|---|---|
| policyA | allowedLocations | array  |因為參數類型已定義為陣列，所以此參數應該要有一個字串清單作為值 |
| policyB | allowedSingleLocation |字串 |因為參數類型已定義為字串，所以此參數應該要有一個字組作為值 |

在此案例中，定義 **initiativeC** 的計畫參數時，有三個選項：

- 使用此計畫內原則定義的參數：在此範例中，_allowedLocations_ 和 _allowedSingleLocation_ 會變成 **initiativeC** 的計畫參數。
- 提供值給此計畫定義內原則定義的參數。 在此範例中，您可以提供位置清單給 **policyA** 的參數 – **allowedLocations** 和 **policyB** 的參數 – **allowedSingleLocation**。 您也可以在指派此計畫時提供值。
- 提供指派此計畫時可以使用的 _值_ 選項清單。 當您指派此計畫時，從計畫內原則定義繼承的參數，只能有來自此提供清單中的值。

建立計畫定義中的值選項時，您無法在計畫指派期間輸入不同的值，因為它不是清單的一部分。

若要深入了解計畫定義的結構，請檢閱[計畫定義結構](./concepts/initiative-definition-structure.md)。

### <a name="assignments"></a>指派

指派是指已指派要在特定範圍內發生的原則定義或計畫。 此範圍可從[管理群組](../management-groups/overview.md)到個別資源。 「範圍」一詞係指作為定義指派對象的所有資源、資源群組、訂用帳戶或管理群組。 指派會由所有子資源繼承。 此設計的用意是，套用至某個資源群組的定義也會套用至該資源群組中的資源。 不過，您可以從指派中排除某個子範圍。

例如，在訂用帳戶範圍，您可以指派一個防止建立網路資源的定義。 您可以將該訂用帳戶內一個要用於網路基礎結構的資源群組排除。 接著，您要將此網路資源群組的存取權授與您所信任來建立網路資源的使用者。

在另一個範例中，建議您在管理群組層級指派資源類型允許清單定義。 然後，您可以在子管理群組或甚至直接在訂用帳戶上指派更寬鬆的原則 (允許更多資源類型)。 不過，因為 Azure 原則是明確的拒絕系統，此範例沒有作用。 相反地，您需要從管理群組層級的指派中排除子管理群組或訂用帳戶。 然後，對子管理群組或訂用帳戶層級指派更寬鬆的定義。 如果有任何指派導致系統拒絕資源，則唯一能允許資源的方法是修改拒絕指派。

如需透過入口網站設定指派的詳細資訊，請參閱[建立原則指派，以識別 Azure 環境中不符合規範的資源](./assign-policy-portal.md)。 此外，還會提供適用於 [PowerShell](./assign-policy-powershell.md) 和 [Azure CLI](./assign-policy-azurecli.md) 的步驟。 如需指派結構的詳細資訊，請參閱[指派結構](./concepts/assignment-structure.md)。

## <a name="maximum-count-of-azure-policy-objects"></a>Azure 原則物件的最大計數

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>後續步驟

現在您已大概了解 Azure 原則，以及一些重要概念，以下是建議的後續步驟：

- [檢閱原則定義結構](./concepts/definition-structure.md)。
- [使用入口網站指派原則定義](./assign-policy-portal.md)。
