---
title: 教學課程 -Web 應用程式使用受控識別存取儲存體 |Azure
description: 在本教學課程中，您將了解如何使用受控識別來存取應用程式的 Azure 儲存體。
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 72b1d4fe864c23c0ac065e47d96ab0c78866defa
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435836"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>教學課程：從 Web 應用程式存取 Azure 儲存體

了解如何使用受控識別，來針對在 Azure App Service 上執行的 Web 應用程式 (非登入的使用者) 存取其 Azure 儲存體。

:::image type="content" alt-text="說明如何存取儲存體的圖表。" source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

您想從 Web 應用程式新增對 Azure 資料平面 (Azure 儲存體、Azure SQL Database、Azure Key Vault 或其他服務) 的存取權。 您可以使用共用金鑰，但必須擔心哪些人可以建立、部署和管理祕密的操作安全性。 金鑰也會簽入 GitHub，此舉可能引來駭客掃描金鑰。 讓 Web 應用程式存取資料的安全方式，是使用[受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

來自 Azure Active Directory (Azure AD) 的受控識別可讓應用程式服務透過角色型存取控制 (RBAC) 存取資源，而不需要應用程式認證。 將受控識別指派給您的 Web 應用程式後，Azure 會負責建立和散發憑證。 不再需要擔心管理祕密或應用程式認證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 在 Web 應用程式上建立系統指派的受控識別。
> * 建立儲存體帳戶和 Azure Blob 儲存體容器。
> * 使用受控識別從 Web 應用程式存取儲存體。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 在 Azure App Service 上執行且已啟用 [App Service 驗證/授權模組](scenario-secure-app-authentication-app-service.md)的 Web 應用程式。

## <a name="enable-managed-identity-on-an-app"></a>啟用應用程式上的受控識別

如果您透過 Visual Studio 建立並發佈 Web 應用程式，則系統會為您的應用程式啟用受控識別。 在應用程式服務中，選取左側窗格中的 [身分識別]，然後選取 [系統指派]。 確定 **狀態** 設為 **開啟**。 若未設定為開啟，請依序選取 [儲存] 和 [是] 以啟用系統指派的受控識別。 啟用受控識別時，狀態會設定為「開啟」，且物件識別碼可供使用。

:::image type="content" alt-text="顯示 [系統指派的身分識別] 選項的螢幕擷取畫面。" source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

此步驟會建立新的物件識別碼，不同於在 [驗證/授權] 窗格中建立的應用程式識別碼。 複製系統指派的受控識別物件識別碼。 稍後您將會用到此資訊。

## <a name="create-a-storage-account-and-blob-storage-container"></a>建立儲存體帳戶和 Blob 儲存體容器

現在您已準備好建立儲存體帳戶和 Blob 儲存體容器。

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 本文說明如何建立新資源群組。

一般用途 v2 儲存體帳戶提供所有 Azure 儲存體服務的存取權：Blob、檔案、佇列、資料表和磁碟。 此處說明的步驟會建立一般用途 v2 儲存體帳戶，但建立任何類型儲存體帳戶的步驟都很類似。

Azure 儲存體中的 Blob 會組織成容器。 您必須先建立容器，稍後才能在本教學課程中上傳 Blob。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要在 Azure 入口網站中建立一般用途 v2 儲存體帳戶，請遵循下列步驟。

1. 在 Azure 入口網站功能表上，選取 [所有服務]  。 在資源清單中，輸入 **儲存體帳戶**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [儲存體帳戶]  。

1. 在出現的 [儲存體帳戶] 視窗中，選取 [新增]。

1. 選取要在其中建立儲存體帳戶的訂用帳戶。

1. 在 [資源群組] 欄位下，從下拉式功能表中選取包含您 Web 應用程式的資源群組。

1. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。

1. 選取儲存體帳戶的位置，或使用預設位置。

1. 讓這些欄位設定為其預設值：

    |欄位|值|
    |--|--|
    |部署模型|Resource Manager|
    |效能|標準|
    |帳戶種類|StorageV2 (一般用途 v2)|
    |複寫|讀取權限異地備援儲存體 (RA-GRS)|
    |存取層|經常性存取層|

1. 選取 [檢閱 + 建立]  ，以檢閱您的儲存體帳戶設定並建立帳戶。

1. 選取 [建立]。

若要在 Azure 儲存體中建立 Blob 儲存體容器，請遵循下列步驟。

1. 在 Azure 入口網站中移至新的儲存體帳戶。

1. 在儲存體帳戶的左窗格中，捲動到 [Blob 服務] 區段，然後選取 [容器]。

1. 選取 [+ 容器]  按鈕。

1. 輸入新容器的名稱。 容器名稱必須是小寫，以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。

1. 設定容器的公用存取層級。 預設層級是 [私用 (沒有匿名存取權)]  。

1. 選取 [確定]  以建立容器。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要建立一般用途的 V2 儲存體帳戶和儲存體容器，請執行下列指令碼。 指定包含 Web 應用程式的資源群組名稱。 輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。

指定儲存體帳戶的位置。 若要查看您訂用帳戶有效的位置清單，請執行 ```Get-AzLocation | select Location```。 容器名稱必須是小寫，以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。

請記得以您自己的值取代角括號中的預留位置值。

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要建立一般用途的 V2 儲存體帳戶和儲存體容器，請執行下列指令碼。 指定包含 Web 應用程式的資源群組名稱。 輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。 

指定儲存體帳戶的位置。 容器名稱必須是小寫，以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。

下列範例會使用您的 Azure AD 帳戶來授權作業，以便建立容器。 建立容器之前，請將 儲存體 Blob 資料參與者 角色指派給自己。 即使您是帳戶擁有者，您還是需要明確的權限，才能對儲存體帳戶執行資料作業。

請記得以您自己的值取代角括號中的預留位置值。

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>授與儲存體帳戶的存取權

您必須先將 Web 應用程式存取權授與儲存體帳戶，才能建立、讀取或刪除 Blob。 在上一個步驟中，您已使用受控識別，設定在 App Service 上執行的 Web 應用程式。 一旦建立了使用者指派的 MSI 後，就可以將 MSI 存取權提供給另一個資源，如同任何安全性主體。 「儲存體 Blob 資料參與者」角色會提供 Web 應用程式 (以系統指派的受控識別表示) 讀取、寫入和刪除 Blob 容器和資料的存取權。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶以授與 Web 應用程式存取權。 選取左側窗格中的 [存取控制 (IAM)]，然後選取 [角色指派]。 您會看到可存取儲存體帳戶的人員清單。 現在您要將角色指派新增至機器人，這是需要存取儲存體帳戶的應用程式服務。 選取 [新增] > [新增角色指派]。

在 **角色** 中，選取 [儲存體 Blob 資料參與者] ，讓您的 Web 應用程式能夠存取讀取儲存體 Blob。 在 **指派存取權** 中，選取 [App Service]。 在 **訂用帳戶** 中選取您的訂用帳戶。 然後選取您想要為其提供存取權的 App Service。 選取 [儲存]。

:::image type="content" alt-text="顯示 [新增角色指派] 畫面的螢幕擷取畫面。" source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

您的 Web 應用程式現在可以存取儲存體帳戶。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

執行下列指令碼，將您的 Web 應用程式 (以系統指派的受控識別表示) 指派給儲存體帳戶上的「儲存體 Blob 資料參與者」角色。

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

執行下列指令碼，將您的 Web 應用程式 (以系統指派的受控識別表示) 指派給儲存體帳戶上的「儲存體 Blob 資料參與者」角色。

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>存取 Blob 儲存體 (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 類別是用來取得程式碼的權杖認證，以授權 Azure 儲存體的要求。 建立 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 類別的執行個體，此執行個體會使用受控識別擷取權杖，並將其附加至服務用戶端。 下列程式碼範例會取得已驗證的權杖認證，並用來建立服務用戶端物件，以上傳新的 Blob。

若要在範例應用程式中查看此程式碼，請參閱 [GitHub 上的範例](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity)。

### <a name="install-client-library-packages"></a>安裝用戶端程式庫套件

安裝 [Blob 儲存體 NuGet 套件](https://www.nuget.org/packages/Azure.Storage.Blobs/)，以使用 Blob 儲存體和[適用於 .NET NuGet 套件的 Azure 身分識別用戶端程式庫](https://www.nuget.org/packages/Azure.Identity/)驗證 Azure AD 認證。 使用 .NET Core 命令列介面或 Visual Studio 中的套件管理員主控台，安裝用戶端程式庫。

# <a name="command-line"></a>[命令列](#tab/command-line)

開啟命令列，並切換至包含專案檔的目錄。

執行安裝命令。

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[套件管理員](#tab/package-manager)

在 Visual Studio 中開啟專案或方案，然後使用 **工具** > **NuGet 套件管理員** > **套件管理員主控台** 命令開啟主控台。

執行安裝命令。
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>範例

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>清除資源

如果您已完成本教學課程，且不再需要 Web 應用程式或相關聯的資源，請[清除您建立的資源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 建立系統指派的受控識別。
> * 建立儲存體帳戶和 Blob 儲存體容器。
> * 使用受控識別從 Web 應用程式存取儲存體。

> [!div class="nextstepaction"]
> [App Service 以使用者身分存取 Microsoft Graph](scenario-secure-app-access-microsoft-graph-as-user.md)