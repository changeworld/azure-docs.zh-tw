---
title: 封鎖特定 API 連接器的連線
description: 限制在 Azure Logic Apps 中建立和使用 API 連接
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 06/19/2020
ms.openlocfilehash: 0ba95969d8bb6987d2e3685f937170f97e1af68f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078698"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>封鎖 Azure Logic Apps 中連接器所建立的連接

如果您的組織不允許使用其在 Azure Logic Apps 中的連接器連線到受限或未核准的資源，您可以封鎖在邏輯應用程式工作流程中建立和使用這些連線的功能。 使用[Azure 原則](../governance/policy/overview.md)，您可以定義和強制執行[原則](../governance/policy/overview.md#policy-definition)，以防止建立或使用您想要封鎖之連接器的連接。 例如，基於安全性理由，您可能會想要封鎖特定社交媒體平臺或其他服務和系統的連接。

本主題說明如何使用 Azure 入口網站來設定封鎖特定連線的原則，但是您可以透過 Azure REST API、Azure PowerShell、Azure CLI 和 Azure Resource Manager 範本，以其他方式建立原則定義。 如需詳細資訊，請參閱[教學課程：建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請在開始前[建立免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要封鎖之連接器的參考識別碼。 如需詳細資訊，請參閱[尋找連接器參考識別碼](#connector-reference-ID)。

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>尋找連接器參考識別碼

如果您已經有邏輯應用程式與您要封鎖的連線，請依照 Azure 入口網站的[步驟進行](#connector-ID-portal)。 否則，請遵循下列步驟進行︰

1. 請造訪[Logic Apps 連接器清單](/connectors/connector-reference/connector-reference-logicapps-connectors)。

1. 尋找您要封鎖之連接器的參考頁面。

   例如，如果您想要封鎖 Instagram 連接器，請移至此頁面： 
   
   `https://docs.microsoft.com/connectors/instagram/`

1. 從頁面的 URL 複製並儲存結尾處的連接器參考識別碼，而不含正斜線（ `/` ），例如 `instagram` 。

   之後，當您建立原則定義時，您會在定義的條件陳述式中使用此識別碼，例如：

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure 入口網站

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的邏輯應用程式。

1. 在 [邏輯應用程式] 功能表上，選取 [**邏輯應用程式代碼視圖**]，讓您可以查看邏輯應用程式的 JSON 定義。

   ![開啟 [邏輯應用程式代碼視圖] 以尋找連接器識別碼](./media/block-connections-connectors/code-view-connector-id.png)

1. 尋找 `parameters` 包含物件的物件 `$connections` ，其中包含 `{connection-name}` 邏輯應用程式中每個連接的物件，並指定該連接的相關資訊：

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   例如，針對 Instagram 連接器，尋找 `instagram` 識別 Instagram 連接的物件：

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. 針對您想要封鎖的連線，尋找 `id` 屬性和值，其格式如下： 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   例如，以下是 `id` Instagram 連接的屬性和值：

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. 在 `id` 屬性值中，複製並儲存結尾的連接器參考識別碼，例如 `instagram` 。

   之後，當您建立原則定義時，您會在定義的條件陳述式中使用此識別碼，例如：

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>建立原則以封鎖建立連接

若要封鎖邏輯應用程式中的完全建立連線，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在入口網站的 [搜尋] 方塊中，輸入 `policy` ，然後選取 [**原則**]。

   ![在 Azure 入口網站中，尋找並選取 [原則]](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在 [**原則**] 功能表的 [**撰寫**] 底下，選取 [**定義**] [  >  **+ 原則定義**]。

   ![選取 [定義] > [+ 原則定義]](./media/block-connections-connectors/add-new-policy-definition.png)

1. 在 [**原則定義**] 底下，根據範例底下所述的屬性，提供原則定義的資訊：

   ![原則定義屬性](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | 屬性 | 必要 | 值 | 說明 |
   |----------|----------|-------|-------------|
   | **定義位置** | 是 | <*Azure-subscription-name*> | 要用於原則定義的 Azure 訂用帳戶 <p><p>1. 若要尋找您的訂用帳戶，請選取省略號（**...**）按鈕。 <br>2. 從 [**訂**用帳戶] 清單中，尋找並選取您的訂用帳戶。 <br>3. 當您完成時，選取 [**選取**]。 |
   | **名稱** | 是 | <*原則-定義-名稱*> | 要用於原則定義的名稱 |
   | **描述** | 否 | <*原則-定義-名稱*> | 原則定義的描述 |
   | **類別** | 是 | **邏輯應用程式** | 原則定義的現有分類或新類別的名稱 |
   | **原則強制執行** | 是 | **已啟用** | 此設定指定當您儲存工作時，是否要啟用或停用原則定義。 |
   ||||

1. 在 [**原則規則**] 底下，JSON 編輯方塊會預先填入原則定義範本。 請根據下表中所述的屬性，以及遵循此語法，將此範本取代為您的[原則定義](../governance/policy/concepts/definition-structure.md)：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 屬性 | 值 | 說明 |
   |----------|-------|-------------|
   | `mode` | `All` | 決定原則所評估之資源類型的模式。 <p><p>此案例會將設定 `mode` 為 `All` ，這會將原則套用至 Azure 資源群組、訂用帳戶和所有資源類型。 <p><p>如需詳細資訊，請參閱[原則定義結構模式](../governance/policy/concepts/definition-structure.md#mode)。 |
   | `if` | `{condition-to-evaluate}` | 判斷何時強制執行原則規則的條件 <p><p>在此案例中，會 `{condition-to-evaluate}` 判斷 `api.id` 中的值是否 `Microsoft.Web/connections/api.id` 符合 `*managedApis/{connector-name}` （指定萬用字元（*）值）。 <p><p>如需詳細資訊，請參閱[原則定義結構-原則規則](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `field` | `Microsoft.Web/connections/api.id` | `field`要與條件比較的值 <p><p>在此案例中，會 `field` 使用[*別名*](../governance/policy/concepts/definition-structure.md#aliases)（ `Microsoft.Web/connections/api.id` ）來存取 connector 屬性中的值 `api.id` 。 |
   | `like` | `*managedApis/{connector-name}` | 要用來比較值的邏輯運算子和值 `field` <p><p>在此案例中， `like` 運算子和萬用字元（*）字元都會確保規則的運作方式（不論是哪個區域），而字串則是要比對的 `*managedApis/{connector-name}` 值，其中 `{connector-name}` 是您要封鎖之連接器的識別碼。 <p><p>例如，假設您想要封鎖建立社交媒體平臺或資料庫的連接： <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server 或 Azure SQL：`sql` <p><p>若要尋找這些連接器識別碼，請參閱本主題稍早的[尋找連接器參考識別碼](#connector-reference-ID)。 |
   | `then` | `{effect-to-apply}` | 符合條件時要套用的效果 `if` <p><p>在此案例中， `{effect-to-apply}` 會封鎖並使不符合原則的要求或作業失敗。 <p><p>如需詳細資訊，請參閱[原則定義結構-原則規則](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `effect` | `deny` | `effect`是用來封鎖要求，也就是建立指定的連接。 <p><p>如需詳細資訊，請參閱[瞭解 Azure 原則效果-拒絕](../governance/policy/concepts/effects.md#deny)。 |
   ||||

   例如，假設您想要封鎖使用 Instagram 連接器建立連接。 以下是您可以使用的原則定義：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   以下是 [**原則規則**] 方塊的顯示方式：

   ![原則定義的規則](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   針對多個連接器，您可以新增更多條件，例如：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. 完成時，選取 [儲存]。 儲存原則定義之後，Azure 原則會產生更多屬性值，並將其新增至原則定義。

1. 接下來，若要指派原則定義讓您想要強制執行原則，請[建立原則指派](#create-policy-assignment)。

如需 Azure 原則定義的詳細資訊，請參閱下列主題：

* [原則結構定義](../governance/policy/concepts/definition-structure.md)
* [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)
* [適用於 Azure Logic Apps 的 Azure 原則內建原則定義](../logic-apps/policy-samples.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>建立原則以封鎖使用連接

當您在邏輯應用程式內建立連線時，該連線會以個別 Azure 資源的形式存在。 如果您只刪除邏輯應用程式，則連線不會自動刪除，而且會持續存在，直到刪除為止。 您可能會有一個案例，也就是連線已經存在，或是您必須建立連線以供邏輯應用程式外部使用。 您仍然可以藉由建立原則來防止儲存具有受限制或未核准連線的邏輯應用程式，以封鎖在邏輯應用程式中使用現有連接的功能。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在入口網站的 [搜尋] 方塊中，輸入 `policy` ，然後選取 [**原則**]。

   ![在 Azure 入口網站中，尋找並選取 [原則]](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在 [**原則**] 功能表的 [**撰寫**] 底下，選取 [**定義**] [  >  **+ 原則定義**]。

   ![選取 [定義] > [+ 原則定義]](./media/block-connections-connectors/add-new-policy-definition.png)

1. 在 [**原則定義**] 下，根據範例底下所述的屬性，提供原則定義的資訊，並繼續使用 Instagram 作為範例：

   ![原則定義屬性](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | 屬性 | 必要 | 值 | 說明 |
   |----------|----------|-------|-------------|
   | **定義位置** | 是 | <*Azure-subscription-name*> | 要用於原則定義的 Azure 訂用帳戶 <p><p>1. 若要尋找您的訂用帳戶，請選取省略號（**...**）按鈕。 <br>2. 從 [**訂**用帳戶] 清單中，尋找並選取您的訂用帳戶。 <br>3. 當您完成時，選取 [**選取**]。 |
   | **名稱** | 是 | <*原則-定義-名稱*> | 要用於原則定義的名稱 |
   | **描述** | 否 | <*原則-定義-名稱*> | 原則定義的描述 |
   | **類別** | 是 | **邏輯應用程式** | 原則定義的現有分類或新類別的名稱 |
   | **原則強制執行** | 是 | **已啟用** | 此設定指定當您儲存工作時，是否要啟用或停用原則定義。 |
   ||||

1. 在 [**原則規則**] 底下，JSON 編輯方塊會預先填入原則定義範本。 請根據下表中所述的屬性，以及遵循此語法，將此範本取代為您的[原則定義](../governance/policy/concepts/definition-structure.md)：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 屬性 | 值 | 說明 |
   |----------|-------|-------------|
   | `mode` | `All` | 決定原則所評估之資源類型的模式。 <p><p>此案例會將設定 `mode` 為 `All` ，這會將原則套用至 Azure 資源群組、訂用帳戶和所有資源類型。 <p><p>如需詳細資訊，請參閱[原則定義結構模式](../governance/policy/concepts/definition-structure.md#mode)。 |
   | `if` | `{condition-to-evaluate}` | 判斷何時強制執行原則規則的條件 <p><p>在此案例中，會 `{condition-to-evaluate}` 判斷的字串輸出是否 `[string(field('Microsoft.Logic/workflows/parameters'))]` 包含字串 `{connector-name}` 。 <p><p>如需詳細資訊，請參閱[原則定義結構-原則規則](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | 要與條件比較的值 <p><p>在此案例中， `value` 是的字串輸出 `[string(field('Microsoft.Logic/workflows/parameters'))]` ，它會將 `$connectors` 物件內的物件轉換 `Microsoft.Logic/workflows/parameters` 成字串。 |
   | `contains` | `{connector-name}` | 要用來與屬性進行比較的邏輯運算子和值 `value` <p><p>在此案例中， `contains` 運算子會確保規則運作，而不論出現在何處 `{connector-name}` ，其中字串 `{connector-name}` 是您想要限制或封鎖之連接器的識別碼。 <p><p>例如，假設您想要封鎖使用社交媒體平臺或資料庫的連接： <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server 或 Azure SQL：`sql` <p><p>若要尋找這些連接器識別碼，請參閱本主題稍早的[尋找連接器參考識別碼](#connector-reference-ID)。 |
   | `then` | `{effect-to-apply}` | 符合條件時要套用的效果 `if` <p><p>在此案例中， `{effect-to-apply}` 會封鎖並使要求或作業失敗，而不符合原則。 <p><p>如需詳細資訊，請參閱[原則定義結構-原則規則](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `effect` | `deny` | `effect`是 `deny` 或封鎖要求，以儲存使用指定連接的邏輯應用程式 <p><p>如需詳細資訊，請參閱[瞭解 Azure 原則效果-拒絕](../governance/policy/concepts/effects.md#deny)。 |
   ||||

   例如，假設您想要封鎖儲存使用 Instagram 連接的邏輯應用程式。 以下是您可以使用的原則定義：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   以下是 [**原則規則**] 方塊的顯示方式：

   ![原則定義的規則](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. 完成時，選取 [儲存]。 儲存原則定義之後，Azure 原則會產生更多屬性值，並將其新增至原則定義。

1. 接下來，若要指派原則定義讓您想要強制執行原則，請[建立原則指派](#create-policy-assignment)。

如需 Azure 原則定義的詳細資訊，請參閱下列主題：

* [原則結構定義](../governance/policy/concepts/definition-structure.md)
* [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)
* [適用於 Azure Logic Apps 的 Azure 原則內建原則定義](../logic-apps/policy-samples.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>建立原則指派

接下來，您必須將原則定義指派至您要強制執行原則的位置，例如，套用至單一資源群組、多個資源群組、Azure Active Directory （Azure AD）租使用者或 Azure 訂用帳戶。 針對這項工作，請遵循下列步驟來建立原則指派：

1. 如果您已登出，請重新登入[Azure 入口網站](https://portal.azure.com)。 在入口網站的 [搜尋] 方塊中，輸入 `policy` ，然後選取 [**原則**]。

   ![在 Azure 入口網站中，尋找並選取 [原則]](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在 [**原則**] 功能表的 [**撰寫**] 底下 **，選取 [** 指派] [  >  **指派原則**]。

   ![選取 [指派] > [指派]](./media/block-connections-connectors/add-new-policy-assignment.png)

1. 在 [**基本**] 底下，為原則指派提供下列資訊：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **範圍** | 是 | 您想要強制執行原則指派的資源。 <p><p>1. 在 [**範圍**] 方塊旁，選取省略號（**...**）按鈕。 <br>2. 從 [**訂**用帳戶] 清單中，選取 Azure 訂用帳戶。 <br>3. （選擇性）從 [**資源群組**] 清單中，選取 [資源群組]。 <br>4. 當您完成時，選取 [**選取**]。 |
   | **排除** | 否 | 要從原則指派中排除的任何 Azure 資源。 <p><p>1. 在 [**排除**專案] 方塊旁，選取省略號（**...**）按鈕。 <br>2. 從**資源清單**中，選取資源 >**新增至選取的範圍**。 <br>3. 當您完成時，請選取 [**儲存**]。 |
   | **原則定義** | 是 | 您想要指派和強制執行的原則定義名稱。 此範例會繼續進行範例 Instagram 原則「封鎖 Instagram 連接」。 <p><p>1. 在 [**原則定義**] 方塊旁，選取省略號（**...**）按鈕。 <br>2. 使用 [**類型**篩選] 或 [**搜尋**] 方塊，尋找並選取原則定義。 <br>3. 當您完成時，選取 [**選取**]。 |
   | **指派名稱** | 是 | 原則指派所要使用的名稱（如果與原則定義不同） |
   | **指派識別碼** | 是 | 自動產生的原則指派識別碼 |
   | **描述** | 否 | 原則指派的描述 |
   | **原則強制執行** | 是 | 啟用或停用原則指派的設定 |
   | **指派者** | 否 | 建立和套用原則指派的人員名稱 |
   ||||

   例如，若要使用 Instagram 範例將原則指派給 Azure 資源群組：

   ![原則指派屬性](./media/block-connections-connectors/policy-assignment-basics.png)

1. 當您完成時，選取 [檢閱 + 建立]。

   建立原則之後，您可能需要等待15分鐘，原則才會生效。 變更可能也會有類似的延遲效果。

1. 原則生效之後，您就可以[測試您的原則](#test-policy)。

如需詳細資訊，請參閱[快速入門：建立原則指派以識別不符合規範的資源](../governance/policy/assign-policy-portal.md)。

<a name="test-policy"></a>

## <a name="test-the-policy"></a>測試原則

若要嘗試您的原則，請使用邏輯應用程式設計工具中的 [立即限制的連接器] 開始建立連接。 繼續進行 Instagram 範例，當您登入 Instagram 時，您會收到此錯誤，表示您的邏輯應用程式無法建立連線：

![連接失敗，因為套用原則](./media/block-connections-connectors/connection-failure-message.png)

此訊息包含下列資訊：

| 描述 | 內容 |
|---|---|
| 失敗的原因 | `"Resource 'instagram' was disallowed by policy."` |
| 指派名稱 | `"Block Instagram connections"` |
| 指派識別碼 | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| 原則定義識別碼 | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 原則](../governance/policy/overview.md)
