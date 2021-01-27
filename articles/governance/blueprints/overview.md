---
title: Azure 藍圖概觀
description: 了解 Azure 藍圖服務如何讓您在 Azure 環境中建立、定義及部署成品。
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919337"
---
# <a name="what-is-azure-blueprints"></a>什麼是 Azure 藍圖？

正如「藍圖」可讓工程師或架構設計人員勾勒出專案的設計參數一樣，Azure 藍圖可讓雲端架構人員和中央資訊技術人員定義一組可重複使用的 Azure 資源，其中實作並遵循組織的標準、模式和需求。 Azure 藍圖可讓開發小組在知道他們是以符合組織合規性進行建置的情況下，快速地建置及建立新環境，並包含一組內建元件 (例如網路) 以加速開發和交貨。

藍圖是以宣告方式來協調部署多種資源範本與其他成品，例如：

- 角色指派
- 原則指派
- Azure Resource Manager 範本 (ARM 範本)
- 資源群組

Azure 藍圖服務由散佈於世界各地的 [Azure Cosmos DB](../../cosmos-db/introduction.md) 所支援。 藍圖物件會複寫至多個 Azure 區域。 此複寫可針對您的藍圖物件提供低延遲、高可用性和一致的存取，無論 Azure 藍圖將您的資源部署至哪個區域。

## <a name="how-its-different-from-arm-templates"></a>與 ARM 範本有何不同

服務的設計目的是協助進行「環境設定」。 此設定通常包含一組資源群組、原則、角色指派和 ARM 範本部署。 藍圖是一種套件，可讓這些「成品」類型集合在一起，並可讓您撰寫及控制該套件的版本，包括透過持續整合和持續傳遞 (CI/CD) 管線。 最終，每個成品都可在單一可稽核且可追蹤的作業中指派給訂用帳戶。

您想要在 Azure 藍圖中為部署所納入的一切，幾乎都可使用 ARM 範本來完成。 不過，ARM 範本並非原生存在於 Azure 中的文件，而是分別儲存在本機或原始檔控制中。 範本可用來部署一或多個 Azure 資源，但是一旦部署那些資源之後，就會失去與範本間的有效聯繫和關聯性。

透過 Azure 藍圖，藍圖定義 (「應該加以」部署的) 和藍圖指派 (「已」部署的) 之間的關聯性就得以保留。 此聯繫有助於改善部署的追蹤和稽核。 Azure 藍圖也可同時升級由相同藍圖控管的數個訂用帳戶。

您不需要在 ARM 範本和藍圖之間做出取捨。 每個藍圖可以包含多個 ARM 範本「成品」，或是不包含。 此支援表示先前開發及維護 ARM 範本程式庫所投入的心力，也可用在 Azure 藍圖中。

## <a name="how-its-different-from-azure-policy"></a>它與 Azure 原則有何不同

藍圖是一種套件或容器，用來組成專注特定方面的標準、模式、以及與 Azure 雲端服務實作相關聯的要求組合，並且設計為可重複使用以維護一致性和合規性。

[原則](../policy/overview.md)是預設允許和明確拒絕的系統，在部署期間專注在資源屬性，並適用於既有資源。 它可確保訂用帳戶內的資源遵循需求和標準，進而支援雲端控管。

在藍圖中包含原則可讓您在指派藍圖期間建立正確的模式或設計。 納入原則可確保只能對環境進行經核准或符合預期的變更，以確保持續符合藍圖的目的。

原則可以作為許多「成品」之一，包含在藍圖定義中。 藍圖也支援使用參數搭配原則和方案。

## <a name="blueprint-definition"></a>藍圖定義

藍圖是由「成品」所組成。 Azure 藍圖目前支援下列資源作為成品：

|資源  | 階層選項| 描述  |
|---------|---------|---------|
|資源群組 | 訂用帳戶 | 建立新的資源群組以供藍圖內的其他成品使用。  這些預留位置資源群組可讓您確切地以自己想要的方式組織資源，並針對包含的原則和角色指派成品以及 ARM 範本提供範圍限制器。 |
|ARM 範本 | 訂用帳戶、資源群組 | 範本 (包括巢狀和連結的範本) 可用來撰寫複雜的環境。 範例環境：SharePoint 伺服器陣列、Azure Automation State Configuration 或 Log Analytics 工作區。 |
|原則指派 | 訂用帳戶、資源群組 | 可讓原則或方案指派到藍圖所指派到的訂用帳戶。 原則或方案必須在藍圖定義位置的範圍內。 如果原則或方案具有參數，您可以在藍圖建立或是藍圖指派期間指派這些參數。 |
|角色指派 | 訂用帳戶、資源群組 | 新增現有使用者或群組到內建角色，以確保適當人員一律擁有資源的正確存取權。 角色指派可針對整個訂用帳戶進行定義，或是以巢狀方式針對包含在藍圖內的特定資源群組定義。 |

### <a name="blueprint-definition-locations"></a>藍圖定義位置

建立藍圖定義時，您將定義藍圖的儲存位置。 藍圖可以儲存到您有其 **參與者** 權限的 [管理群組](../management-groups/overview.md)或訂用帳戶。 如果該位置是管理群組，則可將藍圖指派給該管理群組的任何子訂用帳戶。

### <a name="blueprint-parameters"></a>藍圖參數

藍圖可以傳遞參數給原則/方案，或是 ARM 範本。 當任一「成品」新增到藍圖時，作者可決定要為每個藍圖指派已定義的值，或是允許每個藍圖指派在指派時間提供值。 此彈性可提供選擇是要為藍圖的所有使用者定義預先決定的值，或是在指派時再做出決定。

> [!NOTE]
> 藍圖可以有自己專屬的參數，但目前只有在藍圖是從 REST API 而非透過入口網站建立時才能建立這些參數。

如需詳細資訊，請參閱[藍圖參數](./concepts/parameters.md)。

### <a name="blueprint-publishing"></a>藍圖發佈

藍圖最初建立時，它被視為處於 **草稿** 模式。 當它準備好指派時，必須是 **已發佈**。 發佈需要定義 **版本** 字串 (字母、數字和連字號，長度上限為 20 個字元)，以及選擇性的 **變更附註**。 **版本** 可區別相同藍圖日後的變更，並允許個別指派每個版本。 此版本控制也表示相同藍圖的不同 **版本** 可指派給相同的訂用帳戶。 對藍圖進行其他變更時，**已發佈的**
**版本** 仍會存在 (**未發佈的變更也是如此**)。 一旦變更完成，更新過的藍圖會是 **已發佈**、具有新的唯一 **版本**，並且可立即指派。

## <a name="blueprint-assignment"></a>藍圖指派

藍圖的每個 **已發佈** **版本** 都可指派 (具有最長 90 個字元的名稱) 給現有的管理群組或訂用帳戶。 在入口網站中，藍圖會將 **版本** 預設為最近 **已發佈** 的版本。 如果有成品參數或藍圖參數，則參數將會在指派過程中定義。

> [!NOTE]
> 將藍圖定義指派給管理群組，表示指派物件存在於管理群組中。 成品的部署仍以訂用帳戶為目標。 若要執行管理群組指派，必須使用[建立或更新 REST API](/rest/api/blueprints/assignments/createorupdate) ，而且要求本文必須包含 `properties.scope` 的值，才能定義目標訂用帳戶。

## <a name="permissions-in-azure-blueprints"></a>Azure 藍圖中的權限

若要使用藍圖，您必須透過 [Azure 角色型存取控制](../../role-based-access-control/overview.md) (Azure RBAC) 獲得授權。 若要讀取或檢視 Azure 入口網站中的藍圖，您的帳戶必須對藍圖定義所在的範圍具有讀取權。

若要建立藍圖，您的帳戶需要下列權限：

- `Microsoft.Blueprint/blueprints/write` - 建立藍圖定義
- `Microsoft.Blueprint/blueprints/artifacts/write` - 在藍圖定義上建立成品
- `Microsoft.Blueprint/blueprints/versions/write` - 發佈藍圖

若要刪除藍圖，您的帳戶需要下列權限：

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> 必須獲得或繼承了藍圖儲存所在管理群組或訂用帳戶範圍上的藍圖定義權限。

若要指派或取消指派藍圖，您的帳戶需要下列權限：

- `Microsoft.Blueprint/blueprintAssignments/write` - 指派藍圖
- `Microsoft.Blueprint/blueprintAssignments/delete` - 取消指派藍圖

> [!NOTE]
> 因為藍圖指派是在訂用帳戶上建立，所以藍圖指派和取消指派權限必須在訂用帳戶範圍上授與，或是在訂用帳戶範圍上繼承。

可用的內建角色如下：

|Azure 角色 | 描述 |
|-|-|
|[擁有者](../../role-based-access-control/built-in-roles.md#owner) | 除了其他權限之外，還包含所有與 Azure 藍圖相關的權限。 |
|[參與者](../../role-based-access-control/built-in-roles.md#contributor) | 除了其他權限之外，還可以建立和刪除藍圖定義，但不具有藍圖指派權限。 |
|[藍圖參與者](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | 可以管理藍圖定義，但不能加以指派。 |
|[藍圖操作者](../../role-based-access-control/built-in-roles.md#blueprint-operator) | 可以指派現有的已發行藍圖，但無法建立新的藍圖定義。 只有透過使用者指派的受控識別來完成藍圖指派時，該指派才有效。 |

如果這些內建角色不符合您的安全性需求，請考慮建立[自訂角色](../../role-based-access-control/custom-roles.md)。

> [!NOTE]
> 如果使用系統指派的受控識別，Azure 藍圖的服務主體在指派的訂用帳戶上需要 **擁有者** 角色才能進行部署。 如果使用入口網站，會針對部署自動授與此角色並撤銷。 如果使用 REST API，必須手動授與此角色，但在部署完成之後仍會自動撤銷。 如果使用的是使用者指派的受控識別，只有建立藍圖指派的使用者需要 `Microsoft.Blueprint/blueprintAssignments/write` 授權，這會包含在 **擁有者** 和 **藍圖操作員** 內建角色中。

## <a name="naming-limits"></a>命名限制

某些欄位存在下列限制：

|Object|欄位|允許的字元|最大 長度|
|-|-|-|-|
|藍圖|名稱|字母、數字、連字號和句點|48|
|藍圖|版本|字母、數字、連字號和句點|20|
|藍圖指派|名稱|字母、數字、連字號和句點|90|
|藍圖成品|名稱|字母、數字、連字號和句點|48|

## <a name="video-overview"></a>影片概觀

以下 Azure 藍圖的概觀是來自 Azure Fridays。 如需下載影片，請瀏覽 Channel 9 上的 [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) \(英文\)。

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>後續步驟

- [建立藍圖 - 入口網站](./create-blueprint-portal.md)。
- [建立藍圖 - PowerShell](./create-blueprint-powershell.md)。
- [建立藍圖 - REST API](./create-blueprint-rest-api.md)。