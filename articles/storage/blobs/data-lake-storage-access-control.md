---
title: Azure Data Lake Storage Gen2 中的存取控制清單 |Microsoft Docs
description: 瞭解類似 POSIX 的 Acl 存取控制清單在 Azure Data Lake Storage Gen2 中的運作方式。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 03117b9f0c3cbaea22f36703f689264549b851e8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959130"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中)  (Acl 的存取控制清單

Azure Data Lake Storage Gen2 會實作為存取控制模型，以支援 Azure 角色型存取控制 (Azure RBAC) 和類似 POSIX 的存取控制清單 (Acl) 。 本文說明 Data Lake Storage Gen2 中的存取控制清單。 若要瞭解如何將 Azure RBAC 與 Acl 結合，以及系統如何評估它們以進行授權決策，請參閱 [Azure Data Lake Storage Gen2 中的存取控制模型](data-lake-storage-access-control-model.md)。

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>關於 Acl

您可以將 [安全性主體](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) 與檔案和目錄的存取層級產生關聯。 這些關聯會在 *(ACL) 的存取控制清單* 中捕捉。 您儲存體帳戶中的每個檔案和目錄都具有存取控制清單。 當安全性主體嘗試在檔案或目錄上執行作業時，ACL 檢查會判斷該安全性主體 (使用者、群組、服務主體或受控識別) 是否有正確的許可權等級可以執行此作業。

> [!NOTE]
> Acl 只適用于相同租使用者中的安全性主體，而且不會套用至使用共用金鑰或共用存取簽章 (SAS) 權杖驗證的使用者。 這是因為沒有與呼叫端相關聯的身分識別，因此無法執行安全性主體許可權型授權。  

## <a name="how-to-set-acls"></a>如何設定 Acl

若要設定檔案和目錄層級許可權，請參閱下列任何文章：

| 環境 | 發行項 |
|--------|-----------|
|Azure 儲存體總管 |[使用 Azure 儲存體總管來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 ACL](data-lake-storage-explorer.md#managing-access) (機器翻譯)|
|.NET |[使用 .NET 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl](data-lake-storage-directory-file-acl-dotnet.md#manage-access-control-lists-acls)|
|Java|[使用 JAVA 管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl](data-lake-storage-directory-file-acl-java.md#manage-access-control-lists-acls)|
|Python|[使用 Python 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl](data-lake-storage-directory-file-acl-python.md#manage-access-control-lists-acls)|
|PowerShell|[使用 PowerShell 管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl](data-lake-storage-directory-file-acl-powershell.md#manage-access-control-lists-acls)|
|Azure CLI|[使用 Azure CLI 來管理 Azure Data Lake Storage Gen2 中的目錄、檔案，以及 ACL](data-lake-storage-directory-file-acl-cli.md#manage-access-control-lists-acls)|
|REST API |[路徑-更新](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> 如果安全性主體是 *服務* 主體，請務必使用服務主體的物件識別碼，而不是相關應用程式註冊的物件識別碼。 若要取得服務主體的物件識別碼，請開啟 Azure CLI，然後使用此命令： `az ad sp show --id <Your App ID> --query objectId` 。 請務必將預留位置取代為 `<Your App ID>` 應用程式註冊的應用程式識別碼。

## <a name="types-of-acls"></a>Acl 的類型

存取控制清單有兩種： *存取 acl* 和 *預設 acl*。

存取 ACL 可控制對物件的存取。 檔案和目錄均有存取 ACL。

預設 Acl 是與目錄相關聯之 Acl 的範本，可判斷在該目錄下建立之任何子專案的存取 Acl。 檔案沒有預設 ACL。

存取 ACL 和預設 ACL 有相同的結構。

> [!NOTE]
> 變更父代的預設 ACL 並不會影響現存子項目的存取 ACL 或預設 ACL。

## <a name="levels-of-permission"></a>許可權層級

容器物件的許可權為 [ **讀取**]、[ **寫入**] 和 [ **執行**]，並且可用於下表所示的檔案和目錄：

|            |    檔案     |   目錄 |
|------------|-------------|----------|
| **讀取 (R)** | 可以讀取檔案的內容 | 需要 [讀取] 和 [執行] 才能列出目錄內容 |
| **寫入 (W)** | 可寫入或附加至檔案 | 需要 [寫入] 和 [執行] 才能在目錄中建立子項目 |
| **執行 (X)** | 不表示 Data Lake Storage Gen2 內容中的任何項目 | 需要周遊目錄的子項目 |

> [!NOTE]
> 如果您只使用 Acl (沒有 Azure RBAC) 來授與許可權，然後將檔案的讀取或寫入存取權授與安全性主體，則必須將該容器的 **執行** 許可權授與安全性主體，以及導致該檔案之資料夾階層中的每個資料夾。

### <a name="short-forms-for-permissions"></a>權限的簡短形式

**RWX** 用來表示 [讀取 + 寫入 + 執行]。 有更壓縮的數字形式存在，其中 [讀取 = 4]、[寫入 = 2] 和 [執行 = 1]，其總和代表各種權限。 以下有一些範例。

| 數值形式 | 簡短形式 |      代表的意義     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 讀取 + 寫入 + 執行 |
| 5            | `R-X`        | 讀取 + 執行         |
| 4            | `R--`        | 讀取                   |
| 0            | `---`        | 沒有權限         |

### <a name="permissions-inheritance"></a>權限繼承

在 Data Lake Storage Gen2 所使用的 POSIX 樣式模型中，項目的權限會儲存在項目本身。 換句話說，如果是在子項目建立後才設定權限，則無法從父項目繼承項目的權限。 必須是在子項目建立之前即已設定父項目的預設權限，才會繼承權限。

## <a name="common-scenarios-related-to-acl-permissions"></a>與 ACL 許可權相關的常見案例

下表顯示啟用安全性主體以執行 [作業] **資料行中所列之** 作業所需的 ACL 專案。 

下表顯示代表虛構目錄階層之每個層級的資料行。 容器的根目錄有一個資料行 (`\`) 、一個名為 **俄勒岡** 的子目錄、名為 [中] 的俄勒岡目錄子目錄，以及一個名為 [ **Data.txt**] 的 [中 **名]** 目錄中的文字檔。 

> [!IMPORANT] 下表假設您 **只** 使用 acl，而不使用任何 Azure 角色指派。 若要查看結合 Azure RBAC 與 Acl 的類似表格，請參閱 [許可權表：結合 AZURE rbac 和 acl](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl)。

|    作業             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> 只要前面的兩個條件成立，刪除檔案時就不需要其寫入權限。

## <a name="users-and-identities"></a>使用者和身分識別

每個檔案和目錄都有這些身分識別的不同權限︰

- 擁有使用者
- 擁有群組
- 具名使用者
- 具名群組
- 具名服務主體
- 命名受控識別
- 所有其他使用者

使用者和群組的身分識別皆為 Azure Active Directory (Azure AD) 身分識別。 因此除非另有說明，否則 Data Lake Storage Gen2 的內容中， *使用者* 可以參考 Azure AD 使用者、服務主體、受控識別或安全性群組。

### <a name="the-owning-user"></a>擁有使用者

建立項目的使用者會自動成為項目的擁有使用者。 擁有使用者可以︰

* 變更所擁有檔案的權限。
* 只要擁有使用者也是目標群組的成員，請變更所擁有檔案的擁有群組。

> [!NOTE]
> 擁有使用者「無法」變更檔案或目錄的擁有使用者。 只有超級使用者可以變更檔案或目錄的擁有使用者。

### <a name="the-owning-group"></a>擁有群組

在 POSIX Acl 中，每個使用者都與一個 *主要群組* 相關聯。 例如，使用者 "Alice" 可能屬於「財務」群組。 Alice 也可能屬於多個群組，但一律將一個群組指定為其主要群組。 在 POSIX 中，當 Alice 會建立檔案時，該檔案的擁有群組會設定為她的主要群組，在此案例中為 "finance"。 除此之外，擁有群組的作用類似於指派給其他使用者/群組的權限。

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>指派新檔案或目錄的擁有群組

* **案例 1**：根目錄 "/"。 建立 Data Lake Storage Gen2 容器時，就會建立此目錄。 在此情況下，擁有群組會設定為建立容器的使用者（如果是使用 OAuth 完成）。 如果容器是使用共用金鑰、帳戶 SAS 或服務 SAS 所建立，則擁有者和擁有群組會設定為 **$superuser**。
* **案例 2** (其他所有案例) ：建立新專案時，會從父目錄複寫擁有群組。

#### <a name="changing-the-owning-group"></a>變更擁有群組

可以變更擁有群組的對象︰
* 任何超級使用者。
* 擁有使用者，如果擁有使用者也是目標群組的成員。

> [!NOTE]
> 擁有群組無法變更檔案或目錄的 ACL。  當擁有群組設定為建立帳戶的使用者時，如果是在根目錄的案例中， **第1案例** ，則單一使用者帳戶無效，無法透過擁有群組提供許可權。 您可以將此權限指派給有效的使用者群組 (如果適用的話)。

## <a name="access-check-algorithm"></a>存取檢查演算法

下列虛擬程式碼代表儲存體帳戶的存取檢查演算法。

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
mask = get_mask( path )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    if ((desired_perms & entry.permissions & mask) == desired_perms)
        return True 
        
# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>遮罩

如「存取檢查演算法」所說明，遮罩會限制具名使用者、擁有群組及具名群組的存取。  

針對新的 Data Lake Storage Gen2 容器，根目錄的存取 ACL ( "/" ) 的遮罩預設為 **750** （適用于目錄）和 **640** （檔案）。 下表顯示這些許可權等級的符號標記法。

|實體|目錄|檔案儲存體|
|--|--|--|
|擁有使用者|`rwx`|`r-w`|
|擁有群組|`r-x`|`r--`|
|其他|`---`|`---`|

檔案不會接收 X 位元，因為它與僅限儲存的系統中包含的檔案無關。 

遮罩可就個別的呼叫指定。 這可讓不同的取用系統 (例如叢集) 將不同的有效遮罩用於其檔案作業上。 指定於給定要求上的遮罩會完全覆寫預設遮罩。

### <a name="the-sticky-bit"></a>黏性位元

「粘滯位」（advanced bit）是 POSIX 容器的更先進功能。 在 Data Lake Storage Gen2 的內容中，不太可能需要黏性位元。 總而言之，如果已在目錄上啟用黏性位元，子項目便只能由子項目的擁有使用者刪除或重新命名。

在 Azure 入口網站中不會顯示該上位。

## <a name="default-permissions-on-new-files-and-directories"></a>新檔案和目錄的預設權限

在現有目錄下建立新檔案或目錄時，父目錄的預設 ACL 可決定︰

- 子目錄的預設 ACL 和存取 ACL。
- 子檔案的存取 ACL (檔案沒有預設 ACL)。

### <a name="umask"></a>umask

在建立檔案或目錄時，可使用 umask 來修改子項目上的預設 ACL 設定方式。 umask 是父目錄上的一個 9 位元值，其中包含一個用於 **擁有使用者**、**擁有群組** 及 **其他** 的 RWX 值。

Azure Data Lake Storage Gen2 的 umask 是一個設定為 007 的常數值。 此值會轉譯成：

| umask 元件     | 數值形式 | 簡短形式 | 意義 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 針對擁有使用者，將父代的預設 ACL 複製到子系的存取 ACL | 
| umask.owning_group  |    0         |   `---`      | 針對擁有群組，將父代的預設 ACL 複製到子系的存取 ACL | 
| umask.other         |    7         |   `RWX`      | 針對其他，移除子系存取 ACL 上的所有權限 |

Azure Data Lake Storage Gen2 所使用的 umask 值實際上表示除非在父目錄上定義了預設 ACL，否則 **其他** 的值永遠不會在新的子系上預設傳輸。 在這種情況下，umask 會被忽略，而且預設 ACL 所定義的許可權會套用至子專案。 

下列虛擬程式碼說明為子項目建立 ACL 時套用 umask 的方式。

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="faq"></a>常見問題集

### <a name="do-i-have-to-enable-support-for-acls"></a>我必須啟用 ACL 的支援嗎？

否。 只要已開啟階層命名空間 (HNS) 功能，就會為儲存體帳戶啟用透過 Acl 的存取控制。

如果已關閉 HNS，則 Azure Azure RBAC 授權規則仍然適用。

### <a name="what-is-the-best-way-to-apply-acls"></a>套用 ACL 的最佳方式為何？

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Azure RBAC 和 ACL 許可權的評估方式為何？

若要瞭解系統如何一起評估 Azure RBAC 和 Acl，以進行儲存體帳戶資源的授權決策，請參閱 [如何評估許可權](data-lake-storage-access-control-model.md#how-permissions-are-evaluated)。

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Azure 角色指派和 ACL 專案有哪些限制？

下表提供使用 Azure RBAC 來管理「粗略」許可權 (套用至儲存體帳戶或容器之許可權的摘要查看，) 和使用 Acl 來管理套用至檔案和目錄) 之「更細緻」許可權 (的許可權。 使用安全性群組進行 ACL 指派。 使用群組時，您較不可能會超過每個訂用帳戶的角色指派數目上限，以及每個檔案或目錄的 ACl 專案數目上限。 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Data Lake Storage Gen2 是否支援 Azure RBAC 的繼承？

Azure 角色指派會進行繼承。 指派會從訂用帳戶、資源群組和儲存體帳戶資源往下流向容器資源。

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 是否支援 ACL 的繼承？

預設 Acl 可以用來為父目錄下建立的新子子目錄和檔案設定 Acl。 若要更新現有子專案的 Acl，您必須針對所需的目錄階層，以遞迴方式加入、更新或移除 Acl。 如需詳細資訊，請參閱 [設定 (acl 的存取控制清單) 以遞迴方式進行 Azure Data Lake Storage Gen2](recursive-access-control-lists.md)。 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>若要以遞迴方式刪除目錄及其內容，需要哪些權限？

- 呼叫端具有「超級使用者」許可權，

或

- 父目錄必須具有 [寫入 + 執行] 權限。
- 要刪除的目錄及其中的每個目錄，都需要 [讀取 + 寫入 + 執行] 權限。

> [!NOTE]
> 您不需要寫入權限即可刪除目錄中的檔案。 此外，決不可刪除根目錄 "/"。

### <a name="who-is-the-owner-of-a-file-or-directory"></a>誰是檔案或目錄的擁有者？

檔案或目錄的建立者會成為擁有者。 在根目錄的案例中，這是建立容器的使用者身分識別。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>在建立檔案或目錄時，會將哪個群組設定為擁有群組？

擁有群組是從新檔案或目錄建立所在父目錄的擁有群組複製而來的。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>我是檔案的擁有使用者，但我沒有所需的 RWX 許可權。 該怎麼辦？

擁有使用者可以變更檔案的權限，以取得本身所需的任何 RWX 權限。

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>為什麼我有時候會在 ACL 中看到 GUID？

如果項目代表使用者，而使用者已不存在於 Azure AD 中，則會顯示 GUID。 當使用者已離開公司，或已在 Azure AD 中刪除其帳戶時，通常會發生這種情形。 此外，服務主體和安全性群組並沒有使用者主體名稱 (UPN) 可資識別，因此會藉由其 OID 屬性 (GUID) 來顯示。

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>如何? 為服務主體正確設定 Acl？

當您為服務主體定義 Acl 時，請務必針對您所建立的應用程式註冊使用 *服務主體* 的物件識別碼 (OID) 。 請務必注意，已註冊的應用程式在特定的 Azure AD 租使用者中具有個別的服務主體。 註冊的應用程式具有 Azure 入口網站中可見的 OID，但 *服務主體* 具有另一個 (不同的) oid。

若要取得對應至應用程式註冊的服務主體 OID，您可以使用 `az ad sp show` 命令。 將應用程式識別碼指定為參數。 以下範例說明如何取得對應至應用程式識別碼 = 18218b12-1895-43e9-ad80-6e8fc1ea88ce 之應用程式註冊的服務主體 OID。 在 Azure CLI 中執行下列命令：

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

將會顯示 OID。

當您有服務主體的正確 OID 時，請移至儲存體總管 **管理存取權** ] 頁面，以新增 oid 並為 oid 指派適當的許可權。 請確定選取了 [儲存]。

### <a name="can-i-set-the-acl-of-a-container"></a>是否可以設定容器的 ACL？

否。 容器沒有 ACL。 不過，您可以設定容器根目錄的 ACL。 每個容器都有一個根目錄，且共用與容器相同的名稱。 例如，如果容器名為，則 `my-container` 根目錄的名稱為 `myContainer/` 。 

Azure 儲存體 REST API 包含名為 [ [設定容器 ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)] 的作業，但該作業不能用來設定容器的 ACL 或容器的根目錄。 相反地，該作業會用來指出是否可以 [公開存取](anonymous-read-access-configure.md)容器中的 blob。 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>何處可以進一步了解 POSIX 存取控制模型？

* [Linux 上的 POSIX 存取控制清單](https://www.linux.com/news/posix-acls-linux)
* [HDFS 權限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX 常見問題集](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu 上的 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [在 Linux 上使用存取控制清單的 ACL](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 中的存取控制模型](data-lake-storage-access-control-model.md)