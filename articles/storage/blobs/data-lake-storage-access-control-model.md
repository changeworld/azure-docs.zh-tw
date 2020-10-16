---
title: Azure Data Lake Storage Gen2 的存取控制模型 |Microsoft Docs
description: 瞭解如何在具有階層命名空間的帳戶中設定容器、目錄和檔案層級存取。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: normesta
ms.openlocfilehash: 099d79e63795a88a66ef1ec65aa1bfd97037191e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92134146"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的存取控制模型

Data Lake Storage Gen2 支援下列授權機制：

- 共用金鑰授權
- 共用存取簽章 (SAS) 授權
- 以角色為基礎的存取控制 (Azure RBAC) 
- 存取控制清單 (ACL)

[共用金鑰和 SAS 授權](#shared-key-and-shared-access-signature-sas-authorization) 可將存取權授與使用者 (或應用程式) ，而不需要在 Azure Active Directory (Azure AD) 中具有身分識別。 利用這兩種形式的驗證，Azure RBAC 和 Acl 沒有任何作用。

Azure RBAC 和 ACL 都需要使用者 (或應用程式) 具有 Azure AD 中的身分識別。  Azure RBAC 可讓您對儲存體帳戶資料授與「粗略」存取權，例如儲存體帳戶中 **所有** 資料的讀取或寫入存取權，而 acl 可讓您授與「更細緻」的存取權，例如特定目錄或檔案的寫入存取權。  

本文著重于 Azure RBAC 和 Acl，以及系統如何評估它們，以制定儲存體帳戶資源的授權決策。

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>以角色為基礎的存取控制 (Azure RBAC) 

Azure RBAC 會使用角色指派將許可權集套用至 [安全性主體](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal)。 安全性主體是一個物件，代表 Azure Active Directory (AD) 中定義的使用者、群組、服務主體或受控識別。 許可權集合可以為安全性主體提供「粗略」存取層級的存取權，例如儲存體帳戶中的 **所有** 資料或容器中的 **所有** 資料的讀取或寫入存取權。 

下列角色允許安全性主體存取儲存體帳戶中的資料。 

|角色|描述|
|--|--|
| [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) | Blob 儲存體容器和資料的完整存取權。 此存取權可讓安全性主體設定專案的擁有者，以及修改所有專案的 Acl。 |
| [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) | Blob 儲存體容器和 blob 的讀取、寫入和刪除存取權。 這項存取不允許安全性主體設定專案的擁有權，但可以修改安全性主體所擁有之專案的 ACL。 |
| [儲存體 Blob 資料讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) | 讀取及列出 Blob 儲存體容器和 blob。 |

[擁有](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)者、[參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)、[讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)和[儲存體帳戶參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)等角色允許安全性主體管理儲存體帳戶，但不提供該帳戶內資料的存取權。 不過，這些角色 (不包括 **讀取** 者) 可以取得儲存體金鑰的存取權，這些金鑰可用於各種用戶端工具以存取資料。

## <a name="access-control-lists-acls"></a>存取控制清單 (ACL)

Acl 可讓您將「更精細」的存取層級套用至目錄和檔案。 *Acl*是一種許可權結構，其中包含一系列的*ACL 專案*。 每個 ACL 專案都會將安全性主體與存取層級產生關聯。  若要深入瞭解，請參閱 [Azure Data Lake Storage Gen2 中)  (acl 的存取控制清單](data-lake-storage-access-control.md)。

## <a name="how-permissions-are-evaluated"></a>如何評估許可權

在以安全性主體為基礎的授權期間，許可權會以下列順序評估。

：一個： &nbsp; &nbsp; Azure RBAC 角色指派會先進行評估，並優先于任何 ACL 指派。

：2： &nbsp; &nbsp; 如果作業是根據 Azure RBAC 角色指派完整授權，則不會完全評估 acl。

：三：如果作業未 &nbsp; &nbsp; 經過完整授權，則會評估 acl。

> [!div class="mx-imgBorder"]
> ![data lake storage 許可權流程](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

由於系統會評估存取權限的方式，因此您 **無法** 使用 ACL 來 **限制** 角色指派已授與的存取權。 這是因為系統會先評估 Azure RBAC 角色指派，如果指派授與足夠的存取權限，則會忽略 Acl。 

下圖顯示三個常見作業的許可權流程：列出目錄內容、讀取檔案，以及寫入檔案。

> [!div class="mx-imgBorder"]
> ![data lake storage 許可權流程範例](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>許可權資料表：結合 Azure RBAC 和 ACL

下表說明如何結合 Azure RBAC 角色和 ACL 專案，讓安全性主體可以執行 [作業] **資料行中所列的** 作業。 下表顯示代表虛構目錄階層之每個層級的資料行。 容器的根目錄有一個資料行 (`/`) 、一個名為**俄勒岡**的子目錄、名為 [中] 的俄勒岡目錄子目錄，以及一個名為 [ **Data.txt**] 的 [中**名]** 目錄中的文字檔。 這些資料行中顯示的是授與許可權所需 ACL 專案的 [簡短形式](data-lake-storage-access-control.md#short-forms-for-permissions) 表示。 **N/A** (_不適用_ 的) 會出現在資料行中，如果沒有執行此作業所需的 ACL 專案。

|    作業             | 指派的 RBAC 角色               |    /        | Oregon/     | Portland/ | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Read Data.txt            |   儲存體 Blob 資料擁有者        | N/A      | N/A      | N/A       | N/A    |  
|                          |   儲存體 Blob 資料參與者  | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料讀者       | N/A      | N/A      | N/A       | N/A    |
|                          |   無                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Append to Data.txt       |   儲存體 Blob 資料擁有者        | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料參與者  | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料讀者       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   無                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Delete Data.txt          |   儲存體 Blob 資料擁有者        | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料參與者  | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料讀者       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   無                           | `--X`    | `--X`    | `-WX`     | N/A    |
| Create Data.txt          |   儲存體 Blob 資料擁有者        | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料參與者  | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料讀者       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   無                           | `--X`    | `--X`    | `-WX`     | N/A    |
| List /                   |   儲存體 Blob 資料擁有者        | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料參與者  | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料讀者       | N/A      | N/A      | N/A       | N/A    |
|                          |   無                           | `R-X`    | N/A      | N/A       | N/A    |
| List /Oregon/            |   儲存體 Blob 資料擁有者        | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料參與者  | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料讀者       | N/A      | N/A      | N/A       | N/A    |
|                          |   無                           | `--X`    | `R-X`    | N/A       | N/A    |
| List /Oregon/Portland/   |   儲存體 Blob 資料擁有者        | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料參與者  | N/A      | N/A      | N/A       | N/A    |
|                          |   儲存體 Blob 資料讀者       | N/A      | N/A      | N/A       | N/A    |
|                          |   無                           | `--X`    | `--X`    | `R-X`     | N/A    |


> [!NOTE] 
> 若要在 Azure 儲存體總管中查看容器的內容，安全性主體必須 [使用 Azure AD 登入儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#add-a-resource-via-azure-ad)，而且至少 () 具有讀取存取權 (R--)  (容器的根資料夾) `\` 。 此層級的許可權可讓他們能夠列出根資料夾的內容。 如果您不想要顯示根資料夾的內容，可以指派 [讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) 角色給他們。 使用該角色時，他們將能夠列出帳戶中的容器，但不能列出容器的內容。 然後，您可以使用 Acl 來授與特定目錄和檔案的存取權。   

## <a name="security-groups"></a>安全性群組

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-rbac-role-assignments-and-acl-entries"></a>Azure RBAC 角色指派和 ACL 專案的限制

使用群組時，您較不可能會超過每個訂用帳戶的角色指派數目上限，以及每個檔案或目錄的 ACl 專案數目上限。 下表描述了這些限制。

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>共用金鑰和共用存取簽章 (SAS) 授權

Azure Data Lake Storage Gen2 也支援 [共用金鑰](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) 和 [SAS](https://docs.microsoft.com/azure/storage/common/storage-sas-overview?toc=/azure/storage/blobs/toc.json) 方法以進行驗證。 這些驗證方法的特性是，沒有與呼叫端相關聯的身分識別，因此無法執行安全性主體許可權型授權。

在共用金鑰的情況下，呼叫端會有效地取得「超級使用者」存取權，這表示所有資源（包括資料、設定擁有者和變更 Acl）上所有作業的完整存取權。

SAS 權杖會在其權杖中包含允許的權限。 SAS 權杖中包含的權限會有效地套用至所有授權決策，但不會再執行其他 ACL 檢查。

## <a name="next-steps"></a>後續步驟

若要深入瞭解存取控制清單，請參閱  [Azure Data Lake Storage Gen2 中 (acl) 的存取控制清單](data-lake-storage-access-control.md)。

