---
title: Azure Resource Manager 試用產品 |Azure Marketplace
description: 使用 Azure Resource Manager 建置 Markeplace 試用產品
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275929"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 試用產品

此文章適用於在 Azure Marketplace 上提供供應項目或在 AppSource 上提供供應項目但只想要使用 Azure 資源建置其試用產品的發行者。

Azure Resource Manager （Resource Manager）範本是一種 Azure 資源的程式碼容器，您可以將其設計成最適合您的解決方案。 如果您不熟悉 Resource Manager 範本為何，請閱讀[瞭解 Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)和[撰寫 Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)，以確保您知道如何建立和測試您自己的範本。

試用產品會利用提供的 Resource Manager 範本並將所有必要資源從該 Resource Manager 範本部署到資源群組。

若您選擇建置 Azure Resource Manager 試用產品，需求是：

- 建置、測試並上傳您的試用產品 Resource Manager 範本。
- 設定所有必要中繼資料與設定以啟用您的試用產品。
- 在已啟用試用產品的情況下重新發佈您的供應項目。

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>如何建置 Azure Resource Manager 試用產品

以下是建立 Azure Resource Manager 試用產品的流程：

1. 設計您希望客戶在流程圖中執行的工作。
1. 定義您希望客戶建立的體驗。
1. 根據上述定義，決定客戶要完成這類體驗所需的部分和資源：例如，D365 移轉實例或具有資料庫的網站。
1. 在本機建立設計，並測試體驗。
1. 在 ARM 範本部署中封裝體驗，然後從這裡：
    1. 定義資源的哪些部分是輸入參數;
    1. 哪些變數是;
    1. 提供給客戶經驗的輸出。
1. 發行、測試及上線。

有關如何建置 Azure Resource Manager 試用產品最重要的部分是定義您想讓您的客戶體驗的案例。 您提供防火牆產品並想要示範如何妥善處理指令碼插入式攻擊？ 您提供儲存體產品並想要示範您的解決方案快速並輕鬆地壓縮檔案？

請務必花相當長的時間來評估您的產品的最佳顯示方式。 特別針對您需要的所有必要資源，因為它可讓封裝 Resource Manager 範本的作業變得更容易。

繼續討論上面所講的防火牆範例，架構可能是您需要為您的服務使用公用 IP URL，並為您防火牆所保護的網站使用另一個公用 IP URL。 每個 IP 都是部署在虛擬機器上並使用網路安全性群組 + 網路介面彼此連線。

一旦您設計了所需的資源套件，現在就可以開始撰寫和建立試用產品 Resource Manager 範本。

## <a name="writing-test-drive-resource-manager-templates"></a>撰寫試用產品 Resource Manager 範本

試用產品是在完全自動化模式中執行部署，而且因為這樣，試用產品範本有一些如下限制。

### <a name="parameters"></a>參數

大部分的範本都有一組參數。 參數定義資源名稱、資源大小 (例如，儲存體帳戶類型或虛擬機器大小)、使用者名稱與密碼，以及 DNS 名稱等。 當您使用 Azure 入口網站部署解決方案時，您可以手動填寫所有這些參數，挑選可用的 DNS 名稱或儲存體帳戶名稱等。

![Azure Resource Manager 中的參數清單](./media/azure-resource-manager-test-drive/param1.png)

不過，試用產品在完全自動化模式中運作，不需人工互動，因此只支援一組有限的參數類別。 若試用產品 Resource Manager 範本中的參數不屬於下列其中一個支援的類別，您必須**將此參數取代為變數或常數值**。

您可以為您的參數使用任何有效名稱，試用產品會透過使用中繼資料類型值來識別參數類別。 您**必須為每個範本參數指定中繼資料類型**，否則您的範本將無法通過驗證：

```json
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

也請務必注意，**所有參數都是選擇性**的，因此如果您\'不想要使用任何參數，就\'不需要這麼做。

### <a name="accepted-parameter-metadata-types"></a>接受的參數中繼資料類型

| 中繼資料類型   | 參數類型  | 描述     | 範例值    |
|---|---|---|---|
| **baseuri**     | 字串          | 您部署套件的基底 URI| HTTPs：\//\<。\.\>. blob.core.windows.net/\<\.。\> |
| **username**    | 字串          | 新的隨機使用者名稱。| admin68876      |
| **password**    | 安全字串    | 新的隨機使用者密碼 | Lp!ACS\^2kh     |
| **會話識別碼**   | 字串          | 唯一試用產品工作階段識別碼 (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

試用產品會使用您部署套件的**基底 Uri** 初始化此參數，因此您可以使用此參數將任何檔案的建構 Uri 包含到您的套件中。

```json
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

在您的範本內，您可以使用此參數從您試用產品部署套件建構任何檔案的 Uri。 下面的範例說明如何建構連結範本的 Uri：

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

試用產品會使用新的隨機使用者名稱來初始化此參數：

```json
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

範例值：

    admin68876

您可以為您的解決方案使用隨機或常數使用者名稱。

#### <a name="password"></a>password

試用產品會使用新的隨機密碼初始化此參數：

```json
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

範例值：

    Lp!ACS^2kh

您可以為您的解決方案使用隨機或常數密碼。

#### <a name="session-id"></a>session ID

試用產品會使用代表試用產品工作階段識別碼的唯一 GUID 初始化此參數：

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

範例值：

    b8c8693e-5673-449c-badd-257a405a6dee

如果有需要，您可以使用此參數來唯一識別試用產品工作階段。

### <a name="unique-names"></a>唯一名稱

某些 Azure 資源 (例如儲存體帳戶或 DNS 名稱) 需要全域唯一名稱。

這表示每次試用產品部署 Resource Manager 範本時，都會為其\'所有資源建立**具有唯一名稱的新資源群組**。 因此，必須使用 [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) 函式結合您資源群組識別碼上的變數名稱，來產生隨一唯一值：

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

確定您將您的參數/變數字串 (\'contosovm\') 與唯一字串輸出 (\'resourceGroup().id\') 結合，因此這樣可保證每個變數的唯一性與可靠性。

例如，大部分的資源名稱開頭都不能是數字，但唯一字串函式可以傳回開頭為數字的字串。 因此，若您使用原始唯一字串輸出，您的部署將會失敗。 

您可以在[此文章](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)獲得資源命名規則與限制的額外資訊。

### <a name="deployment-location"></a>部署位置

您可以在不同的 Azure 區域中提供您的試用產品。 其概念是讓使用者挑選最近的區域，以提供最佳使用者體驗。

當試用產品建立實驗的執行個體時，它一律會在使用者選擇的區域中建立資源群組，然後在此群組內容中執行您的部署範本。 因此，您的範本應該從資源群組挑選部署位置：

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

接著為特定實驗執行個體的每個資原始用此位置：

```json
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

您必須確認您的訂用帳戶允許您在您選擇的每個區域部署您要部署的所有資源。 此外，您也必須確認您的虛擬機器映像在您要啟用的的所有區域中都可供使用，否則您的部署範本在某些區域將無法運作。

### <a name="outputs"></a>輸出

一般而言，使用 Resource Manager 範本時，您可以在不產生任何輸出的情況下進行部署。 這是因為您知道用來填寫範本參數的所有值，而且您一律可以手動檢查任何資源的屬性。

不過，針對試用產品 Resource Manager 範本，請務必將所有資訊傳回給試用產品，才能存取實驗 (網站 URI、虛擬機器主機名稱、使用者名稱與密碼)。 確定您的所有輸出名稱都可讀，因為這些變數會呈現給客戶。

範本輸出沒有任何相關限制。 只要記住，試用產品會將所有輸出值轉換為**字串**，因此若您將某個物件傳送到輸出，使用者將會看到 JSON 字串。

範例：

```json
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

您應該考慮的另一件事是訂用帳戶與服務限制。 例如，若您想要部署最多十部 4 核心虛擬機器，您必須確認您為實驗使用的訂用帳戶允許您使用 40 個核心。

您可以在[此文章](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)中找到有關 Azure 訂用帳戶與服務限制的詳細資訊。 可以同時進行多個試用產品評估，因此請確認您的訂用帳戶可以處理核心數目乘以可以同時進行的並行試用產品總數。

### <a name="what-to-upload"></a>要上傳的項目

試用產品 Resource Manager 範本會以 zip 檔案形式上傳，其中可包括各種部署成品，但其名稱必須是 **main-template.json**。 此檔案是 Azure Resource Manager 部署範本，而且試用產品會使用它來具現化實驗。

若除了此檔案之外還有其他額外資源，您能以範本內之外部資源的方式參考它，或將該資源包括在 zip 檔案中。

在發佈認證期間，試用產品會將您的部署套件解壓縮，並將其內容放到內部適用產品 Blob 容器中。 容器結構會反映您部署套件的結構：

| package.zip                       | 試用產品 Blob 容器         |
|---|---|
| main-template.json                | HTTPs：\//\<...\.\>. blob.core.windows.net/\<\..。\>/main-template.json  |
| templates/solution.json           | HTTPs：\//\<...\.\>. blob.core.windows.net/\<\..。\>/templates/solution.json |
| scripts/warmup.ps1                | HTTPs：\//\<...\.\>. blob.core.windows.net/\<\..。\>/scripts/warmup.ps1  |


我們將此 Blob 容器的 Uri 稱為基底 Uri。 您實驗的每個版次都有自己的 Blob 容器，因此您實驗的每個版次都有自己的基底 Uri。 試用產品可以透過範本參數，將您已解壓縮之部署套件的基底 Uri 傳遞到您的範本。

## <a name="transforming-template-examples-for-test-drive"></a>轉換試用產品的範本範例

將資源架構轉換為試用產品 Resource Manager 範本的程序可能很困難。 為協助您更輕鬆地完成此程序，我們已建立範例，說明如何以最佳方式[轉換目前的部署範本](./transforming-examples-for-test-drive.md)。

## <a name="how-to-publish-a-test-drive"></a>如何發佈試用產品

既然您已經建置您的試用產品，此節逐步解說成功發佈試用產品所需的每個欄位。

![在使用者介面中啟用試用產品](./media/azure-resource-manager-test-drive/howtopub1.png)

第一個且最重要的欄位是切換是否要為您的供應項目啟用試用產品。 當您選取 **[是] 時，** 會顯示包含所有必要欄位的表單的其餘部分，以供您填寫。當您選取 [否] 時 **，** 表單會變成停用狀態，如果您在已停用試用產品的情況下重新發佈，就會從生產環境中移除

注意：若任何試用產品仍由使用者使用中，那些試用產品將會繼續執行，直到其工作階段過期。

### <a name="details"></a>詳細資料

要填寫的下一個區段是有關您試用產品供應項目的詳細資料。

![試用產品詳細資訊](./media/azure-resource-manager-test-drive/howtopub2.png)

**描述 -** *必要* 這是撰寫有關您試用產品所提供主要功能的位置。 客戶將到這裡查看您的試用產品涵蓋哪些產品的案例。 

**使用者手動 -** *必要* 這是詳細說明您試用產品體驗的位置。 客戶將開啟此部分，而且可以逐步查看您在其整個試用產品評估過程中要他們做的事。 此內容必須容易理解並遵循！ (必須是 .pdf 檔案)

**試用產品示範影片 -** *建議* 類似使用者手冊，最好能包括有關您試用產品體驗的影片教學課程。 客戶將在進行試用產品評估前或進行試用產品評估期間觀看影片，而且可以 逐步查看您在其整個試用產品評估過程中要他們做的事。 此內容必須容易理解並遵循！

- **名稱** - 您的影片標題
- **連結** - 必須是來自您的 Tube 或影片的內嵌 URL。 有關如何取得內嵌 URL 的範例如下：
- **縮圖** - 必須是高品質影像 (533x324) 像素。 建議您在這裡擷取您試用產品體驗部分的一些螢幕擷取畫面。

下面顯示這些欄位如何在客戶的試用產品體驗期間顯示。

![Marketplace 供應項目中試用產品欄位的位置](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>技術設定

要填寫的下一節是您上傳試用產品 Resource Manager 範本的位置，並特別定義您的試用產品執行個體如何運作。

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**執行個體 -** *必要* 您可以在此位置設定所需的執行個體數目、要在哪個區域設定，以及您的客戶多快就可以取得試用產品。

- **執行個體** - [選取區域] 是您挑選試用產品 Resource Manager 範本部署所在的位置。 建置只挑選一個您最希望您的客戶所在的區域。
- **熱** - 每個所選區域已部署並等候存取的試用產品執行個體數目。 客戶可以立即存取此試用產品，而不需要等候部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 非常建議您擁有**至少一個待命執行個體**，因為您的大部分客戶都不會想要等候完整部署完成，因此客戶使用方面會減少經常性儲存層。
- **暖** - 每個區域中已部署且 VM 已停止並存放在 Azure 儲存體中的試用產品執行個體數目。 暖執行個體的等候時間比熱執行個體還久，但是儲存體運作成本也相對較低。
- **冷** - 每個區域中可能可以部署的試用產品執行個體數目。 在客戶要求時試用產品時，冷執行個體需要整個試用產品 Resource Manager 範本執行部署程序，因此比熱或暖執行個體慢。 但是，優點是只需要針對實際執行試用產品期間付費。

目前，請計算您將提供的潛在並行試用產品總數，並確認您訂用帳戶的配額限制可以處理該並行量：

**(選取的區域數目 x 熱執行個體數目) + (選取的區域數目 x 暖執行個體數目) + (選取的區域數目 x 冷執行個體數目)**

**試用產品持續時間（小時）-** 在數小時內，試用產品將維持作用中\#狀態的*所需*時間長度。 在此持續時間過後，試用產品將會自動終止。

**試用產品 Resource Manager 範本 -** *必要* 在這裡上傳您的 Resource Manager 範本。 這是您在上一節中建立的檔案。 主範本檔案名稱："main-template.json" 並確認您的 Resource Manager 範本包含所需的主要變數輸出參數。 (必須是 .zip 檔案)

**存取資訊 -** *必要* 客戶取得其試用產品之後，會呈現存取資訊給他們。 這些指示旨在共用來自您試用產品 Resource Manager 範本的實用輸出參數。 若要包括輸出參數，請使用雙大括弧 (例如 **{{outputname}}**)，而且它們將會被正確插入該位置。 (這裡建議 HTML 字串格式，以便在前端轉譯)。

### <a name="test-drive-deployment-subscription-details"></a>試用產品部署訂用帳戶詳細資料

要填寫的最後一個區段是要能夠透過連結您的 Azure 訂用帳戶與 Azure Active Directory (AD) 以自動部署試用產品。

![試用產品部署訂用帳戶詳細資料](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure 訂用帳戶識別碼 -** *必要* 這會授與對 Azure 服務與 Azure 入口網站的存取權。 訂用帳戶是回報資源使用狀況並針對所使用服務計費的位置。 若您還沒有將只用於試用產品的**獨立** Azure 訂用帳戶，請建立一個獨立訂用帳戶。 您可以透過登入 Azure 入口網站並瀏覽到左側功能表中的 [訂用帳戶] 以尋找 Azure 訂用帳戶識別碼。 (範例："a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure 訂用帳戶](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD 租用戶識別碼 -** *必要* 若您已經有可用的租用戶識別碼，您可以在下面的 [屬性 -\> 目錄識別碼] 中找到它。

![Azure Active Directory 屬性](./media/azure-resource-manager-test-drive/subdetails3.png)

否則，請在 Azure Active Directory 中建立新的租用戶。

![Azure Active Directory 租用戶清單](./media/azure-resource-manager-test-drive/subdetails4.png)

![定義 Azure AD 租使用者的組織、網域和國家/地區](./media/azure-resource-manager-test-drive/subdetails5.png)

![確認選取範圍](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App 識別碼 -** *必要* 下一個步驟是建立並註冊新的應用程式。 我們將使用此應用程式在您的試用產品執行個體上執行作業。

1. 瀏覽到新建立的目錄或現有的目錄，並在篩選窗格中選取 Azure Active Directory。
2. 搜尋「應用程式註冊」並按一下 [新增]
3. 提供應用程式名稱。
4. 在 [類型] 中選取 [Web 應用程式/API]
5. 在 [登入 URL] 中提供任意值，我們將不會使用該欄位。
6. 按一下 [建立]。
7. 建立應用程式之後，移至 [屬性 -\> 將應用程式設定為多租用戶]，然後按一下 [儲存]。

按一下 [儲存]。 最後一個步驟是擷取這個已註冊之應用程式的應用程式識別碼，並將它貼入到這裡的 [試用產品] 欄位。

![Azure AD 應用程式識別碼詳細資料](./media/azure-resource-manager-test-drive/subdetails7.png)

假設我們使用應用程式部署到訂用帳戶，我們必須在訂用帳戶上將應用程式新增為參與者。 有關這些動作的指示如下：

1. 瀏覽到 [訂用帳戶] 刀鋒視窗，並選取您僅用於試用產品的適當訂用帳戶。
1. 按一下 **[存取控制（IAM）**]。
1. 按一下 [**角色指派**] 索引標籤。 ![加入新的存取控制主體](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. 按一下 [**新增角色指派**]。
1. 將角色設定為 [**參與者**]。
1. 輸入 Azure AD 應用程式的名稱，並選取應用程式以指派角色。
    ![新增權限](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. 按一下 **[儲存]** 。

**Azure AD App 金鑰 -** *必要* 最後一個欄位是要產生驗證金鑰。 在 [金鑰] 下，新增 [金鑰描述] 並將期間設定為永不到期，然後選取 [儲存]。 **務必**避免擁有已到期的金鑰，這將會使得生產環境中的試用產品中斷。 複製此值並將它貼到您的必填 [試用產品] 欄位中。

![顯示 Azure AD 應用程式的金鑰](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>後續步驟

既然您已經填寫所有試用產品欄位，請瀏覽一遍，然後**重新發佈**您的供應項目。 一旦您的試用產品通過認證，您應該在您供應項目的**預覽**中全面測試客戶體驗。 在 UI 中啟動試用產品，然後在 Azure 入口網站中開啟您的 Azure 訂用帳戶，並確認您的試用產品已完全正確部署。

![Azure 入口網站](./media/azure-resource-manager-test-drive/subdetails9.png)

請務必記住，您不需要刪除任何試用產品執行個體，因為它們是針對您的客戶所佈建，因此在客戶完成評估之後，試用產品服務將會自動清除這些資源群組。

一旦您對預覽供應專案感到滿意之後，就可以開始**上線**！ 供應項目發佈之後，必須由 Microsoft 進行審核程序，以便再次檢查整個端對端體驗。 如果供應項目因某個原因遭拒，我們將會傳送通知給工程連絡人，說明您必須如何修正您的供應項目。

如果您有其他問題、在尋找疑難排解建議，或想讓試用產品更成功，請移至[常見問題集、疑難排解與最佳做法](./marketing-and-best-practices.md)。
