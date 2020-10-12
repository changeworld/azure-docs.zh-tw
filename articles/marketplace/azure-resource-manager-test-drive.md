---
title: 試用產品的類型，Microsoft 商業市場
description: 商業 marketplace 中的試用產品類型
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: keferna
author: keferna
ms.openlocfilehash: 92fd4d629585ed465e2891be2dce1c1bdc8c88e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287933"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品

如果您有 Azure Marketplace 或 AppSource 的供應專案，但只想要建立只有 Azure 資源的試用產品，請使用此類型。 Azure Resource Manager (ARM) 範本是一種 Azure 資源的編碼容器，您可以將其設計為最能代表您的解決方案。 試用產品會採用提供的 ARM 範本，並將它所需的所有資源部署到資源群組。 這是虛擬機器或 Azure 應用程式供應專案的唯一試用產品選項。

如果您不熟悉什麼是 ARM 範本，請閱讀 [Azure Resource Manager 的內容](../azure-resource-manager/resource-group-overview.md) ，並 [瞭解 arm 範本的結構和語法](../azure-resource-manager/resource-group-authoring-templates.md) ，以進一步瞭解如何建立及測試您自己的範本。

如需 **託管** 或 **邏輯應用程式** 試用產品的相關資訊，請參閱 [什麼是試用產品？](what-is-test-drive.md)

## <a name="technical-configuration"></a>技術設定

部署範本包含所有組成解決方案的 Azure 資源。 適用於此案例的產品為僅使用 Azure 資源的產品。 在合作夥伴中心中設定下列屬性：

- **區域** (必要) - 目前有 26 個 Azure 支援的區域可提供試用產品。 一般來說，建議您在預期客戶數最多的區域提供試用產品，以便客戶可以選取最接近的區域以獲得最佳效能。 請先確認您的訂閱允許在每個所選區域部署所有需要的資源。

- **執行個體** – 選取類型 (經常性存取層或非經常性存取層) 和可用執行個體的數目，其會乘以供應項目可用的區域數目。

  - **經常性存取層** – 此類型的執行個體為已部署，並會在每個所選區域等候存取。 客戶可以立即存取試用產品的「經常性存取層」執行個體，而不需要等候部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議您擁有至少一個「經常性存取層」執行個體，因為您的大部分客戶都不會想要等候完整部署，因此若沒有可用的「經常性存取層」執行個體，客戶會減少使用量。

  - **非經常性存取層** – 此類型的執行個體代表每個區域可能部署的執行個體總數。 在客戶要求時試用產品時，「非經常性存取層」執行個體需要整個試用產品 Resource Manager 範本執行部署程序，因此較「經常性存取層」執行個體緩慢許多。 這個類型的好處在於您只需要支付試用產品持續時間的費用，而不像「經常性存取層」執行個體一樣，須一律在 Azure 訂閱上執行。

- **試用產品 Azure Resource Manager 範本** – 上傳包含您的 Azure Resource Manager 範本的 .zip 檔案。 在快速入門文章中，深入了解如何建立 Azure Resource Manager 範本，[使用 Azure 入口網站建立及部署 ARM 範本](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)。

- **試用產品持續時間** (必要) - 輸入試用產品會保持作用的時數。 在此持續時間過後，試用產品將會自動終止。 僅使用整數 (例如，「2」小時為有效時間，「1.5」則無效)。

## <a name="write-the-test-drive-template"></a>撰寫試用產品範本

當您設計所需的資源套件之後，請撰寫並建立試用產品 ARM 範本。 因為試用產品會在完全自動化模式中執行部署，所以試用產品範本有一些限制：

### <a name="parameters"></a>參數

大部分的範本都有一組參數，可定義資源名稱、資源大小 (例如儲存體帳戶的類型或虛擬機器大小) 、使用者名稱和密碼、DNS 名稱等等。 當您使用 Azure 入口網站部署解決方案時，您可以手動填寫所有這些參數，挑選可用的 DNS 名稱或儲存體帳戶名稱等。

![Azure Resource Manager 中的參數清單](media/test-drive/resource-manager-parameters.png)

不過，試用產品會自動運作，不需要人為互動，因此只支援一組有限的參數類別。 如果試用產品 ARM 範本中的參數不屬於其中一個支援的類別，您必須使用變數或常數值來取代此參數。

您可以使用任何有效的參數名稱;試用產品會使用元資料類型值來辨識參數類別目錄。 指定每個範本參數的元資料類型，否則您的範本將不會通過驗證：

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> 所有參數都是選擇性的，因此，如果您不想使用任何參數，則不需要這麼做。

### <a name="accepted-parameter-metadata-types"></a>接受的參數中繼資料類型

| 中繼資料類型   | 參數類型  | 說明     | 範例值    |
|---|---|---|---|
| **baseuri**     | 字串          | 您部署套件的基底 URI| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **username**    | 字串          | 新的隨機使用者名稱。| admin68876      |
| **password**    | 安全字串    | 新的隨機使用者密碼 | Lp!ACS\^2kh     |
| **會話識別碼**   | 字串          | 唯一的試用產品會話識別碼 (GUID)     | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

試用產品會使用您部署套件的 **基底 Uri** 來初始化此參數，因此您可以使用此參數來建立套件中所含任何檔案的 Uri。

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

請在您的範本內使用此參數，以從您的試用產品部署套件中建立任何檔案的 Uri。 下列範例顯示如何建立連結範本的 Uri：

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

試用產品會使用新的隨機使用者名稱來初始化此參數：

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

範例值： `admin68876`

您可以為您的解決方案使用隨機或常數使用者名稱。

#### <a name="password"></a>密碼

試用產品會使用新的隨機密碼來初始化此參數：

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

範例值：  `Lp!ACS^2kh`

您可以為您的解決方案使用隨機或常數密碼。

#### <a name="session-id"></a>session ID

試用產品會使用代表試用產品會話識別碼的唯一 GUID 來初始化此參數：

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

範例值： `b8c8693e-5673-449c-badd-257a405a6dee`

您可以使用此參數來唯一識別試用產品會話（如有必要）。

### <a name="unique-names"></a>唯一名稱

某些 Azure 資源 (例如儲存體帳戶或 DNS 名稱) 需要全域唯一名稱。 這表示每次試用產品部署 ARM 範本時，它會建立一個新的資源群組，其所有資源都有唯一的名稱。 因此，您必須使用 [uniquestring](../azure-resource-manager/templates/template-functions.md) 函式與資源群組識別碼上的變數名稱串連，以產生隨機的唯一值：

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

請確定您將參數/變數字串 (`contosovm`) 與唯一的字串輸出 (`resourceGroup().id`) ）串連，因為這可保證每個變數的唯一性與可靠性。

例如，大部分的資源名稱開頭都不能是數字，但唯一字串函式可以傳回開頭為數字的字串。 因此，若您使用原始唯一字串輸出，您的部署將會失敗。

您可以在[此文章](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)獲得資源命名規則與限制的額外資訊。

### <a name="deployment-location"></a>部署位置

您可以讓您在不同的 Azure 區域中使用測試磁片磁碟機。 其概念是讓使用者挑選最近的區域，以提供最佳使用者體驗。

當試用產品建立實驗室的實例時，一律會在使用者選擇的區域中建立資源群組，然後在此群組內容中執行您的部署範本。 因此，您的範本應該從資源群組挑選部署位置：

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

接著為特定實驗執行個體的每個資原始用此位置：

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

確定您的訂用帳戶可以在您選取的每個區域中部署您想要的所有資源。 此外，請確定您的虛擬機器映射可在您將啟用的所有區域中使用，否則您的部署範本將無法在某些區域中運作。

### <a name="outputs"></a>輸出

一般來說，您可以在不產生任何輸出的情況下部署 Resource Manager 範本。 這是因為您知道用來填寫範本參數的所有值，而且您一律可以手動檢查任何資源的屬性。

不過，若為試用產品 Resource Manager 範本，請務必返回測試磁片，以取得實驗室 (網站 Uri、虛擬機器主機名稱、使用者名稱和密碼) 所需的所有資訊。 確定您的所有輸出名稱都是可讀取的，因為這些變數會呈現給客戶。

範本輸出沒有任何相關限制。 試用產品會將所有輸出值轉換成字串，因此，如果您將物件傳送到輸出，使用者會看到 JSON 字串。

範例：

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>訂用帳戶限制

別忘了訂用帳戶和服務限制。 例如，如果您想要部署多達 10 4 核心的虛擬機器，您必須確定您用於實驗室的訂用帳戶可讓您使用40核心。 如需 Azure 訂用帳戶和服務限制的詳細資訊，請參閱 [azure 訂用帳戶和服務限制、配額和條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。 當您可以同時執行多個試用產品時，請確認您的訂用帳戶可以處理核心數目乘以可進行的並行試用產品總數。

### <a name="what-to-upload"></a>要上傳的項目

試用產品 ARM 範本會以 zip 檔案的形式上傳，其中可包含各種不同的部署成品，但必須有一個名為的檔案 `main-template.json` 。 這是試用產品用來具現化實驗室的 ARM 部署範本。 如果您在此檔案之外有其他資源，您可以參考這些資源作為範本內的外部資源，或將它們包含在 zip 檔案中。

在發佈認證期間，試用產品會會解壓縮您的部署套件，並將其內容放入內部的試用產品 blob 容器中。 容器結構會反映您部署套件的結構：

| package.zip                       | 測試磁片磁碟機 blob 容器         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

我們將此 Blob 容器的 Uri 稱為基底 Uri。 因為實驗室的每個修訂都有自己的 blob 容器，所以實驗室的每個修訂都有自己的基底 Uri。 試用產品可以透過範本參數，將您已解壓縮之部署套件的基底 Uri 傳遞到您的範本。

### <a name="transform-template-examples-for-test-drive"></a>試用產品的轉換範本範例

將資源架構轉換為試用產品 Resource Manager 範本的程式可能很困難。 如需其他說明，請參閱下列範例：如何以最佳方式轉換目前 [的部署範本？什麼是試用產品？](what-is-test-drive.md#transforming-examples)。

## <a name="test-drive-deployment-subscription-details"></a>試用產品部署訂用帳戶詳細資料

最後一個要完成的部分，就是將您的 Azure 訂用帳戶和 Azure Active Directory (AD) ，以自動部署試用產品。

![試用產品部署訂用帳戶詳細資料](media/test-drive/deployment-subscription-details.png)

1. 取得 **Azure 訂**用帳戶識別碼。 這會授與對 Azure 服務與 Azure 入口網站的存取權。 訂用帳戶是回報資源使用狀況並針對所使用服務計費的位置。 如果您還沒有個別的 Azure 訂用帳戶來試用磁片磁碟機，請建立一個。 您可以透過登 `1a83645ac-1234-5ab6-6789-1h234g764ghty1` 入 Azure 入口網站並從左側導覽功能表中選取 [訂用帳戶**Subscriptions** ]，來尋找 Azure 訂用帳戶識別碼 (例如) 。

   ![Azure 訂用帳戶](media/test-drive/azure-subscriptions.png)

2. 取得 **Azure AD 租使用者識別碼**。 如果您已經有可用的租使用者識別碼，您可以在**Azure Active Directory**  >  **屬性**  >  **目錄識別碼**：

   ![Azure Active Directory 屬性](media/test-drive/azure-active-directory-properties.png)

   如果您沒有租使用者識別碼，請在 Azure Active Directory 中建立一個新的識別碼。 如需設定租使用者的協助，請參閱 [快速入門：設定租](../active-directory/develop/quickstart-create-new-tenant.md)使用者。

3. **AZURE AD APP 識別碼** -建立並註冊新的應用程式。 我們將使用此應用程式在您的試用產品實例上執行作業。

   1. 流覽至新建立的目錄或現有的目錄，然後在 [篩選] 窗格中選取 [Azure Active Directory]。
   2. 搜尋 **應用程式註冊** 然後選取 [ **新增**]。
   3. 提供應用程式名稱。
   4. 選取**Web 應用程式/API**的**類型**。
   5. 在 [登入 URL] 中提供任何值，就不會使用此欄位。
   6. 選取 [建立]****。
   7. 建立應用程式之後，選取 [**屬性**]  >  **將應用程式設定為多租**使用者，然後**儲存**。

4. 選取 [儲存]****。

5. 複製此已註冊應用程式的應用程式識別碼，並將它貼到 [試用產品] 欄位中。

   ![Azure AD 應用程式識別碼詳細資料](media/test-drive/azure-ad-application-id-detail.png)

6. 由於我們使用應用程式部署至訂用帳戶，因此我們必須將應用程式新增為訂用帳戶的參與者：

   1. 選取您要用於試用產品的 **訂** 用帳戶類型。
   1. 選取 [存取控制 (IAM)]  。
   1. 選取 [ **角色指派** ] 索引標籤，然後 **新增角色指派**。

      ![新增新的存取控制原則](media/test-drive/access-control-principal.jpg)

   1. 設定 **角色** 並 **將存取權指派給** ，如下所示。 在 [ **選取** ] 欄位中，輸入 Azure AD 應用程式的名稱。 選取您要指派 **參與者** 角色的應用程式。

      ![新增權限](media/test-drive/access-control-permissions.jpg)

   1. 選取 [儲存]****。

7. 產生 **Azure AD App** 的驗證金鑰。 在 [ **金鑰**] 下，新增 **金鑰描述**、將持續時間設為 [ **永不過期** ] (到期的金鑰會將您的試用產品分成生產) ，然後選取 [ **儲存**]。 將此值複製並貼到 [需要的試用產品] 欄位中。

![顯示 Azure AD 應用程式的金鑰](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>重新發佈

現在所有的試用產品欄位都已完成，請重新 **發佈** 您的供應專案。 一旦您的試用產品通過認證，請在您的供應專案預覽版中測試客戶體驗：

1. 在 UI 中啟動試用產品。
1. 在 Azure 入口網站中開啟您的 Azure 訂用帳戶。
1. 確認您的試用產品已正確部署。

   ![Azure 入口網站](media/test-drive/azure-portal.png)

請勿刪除為客戶布建的任何試用產品實例;在客戶完成後，試用產品服務將會自動清除這些資源群組。

一旦您熟悉您的預覽供應專案，就可以 **上線**！ 有最後的評論流程可以重複檢查整個端對端體驗。 如果我們拒絕該供應專案，我們會以電子郵件傳送您供應專案的工程連絡人，說明需要修正的專案。

## <a name="next-steps"></a>接下來的步驟

- 如果您遵循在合作夥伴中心中建立供應專案的指示，請使用 [上一步] 箭號返回該主題。
- 深入瞭解 [什麼是試用產品](what-is-test-drive.md)的其他類型的試用產品？。
