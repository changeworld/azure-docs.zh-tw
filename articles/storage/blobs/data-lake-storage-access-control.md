---
title: Azure Data Lake Storage Gen2 中的存取控制概觀 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 中的存取控制運作方式
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 93c21656a768ae458572e0b4917412c8103b2f2d
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992210"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的存取控制

Azure 資料儲存單元 2 實現了一個存取控制模型,該模型同時支援基於 Azure 角色的存取控制 (RBAC) 和類似 POSIX 的存取控制列表 (ACL)。 本文摘要說明 Data Lake Storage Gen2 存取控制模型的基本概念。

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>角色型存取控制

RBAC 使用角色配置有效地將權限集應用於*安全主體*。 *安全主體*是一個物件,表示在 Azure 活動目錄 (AD) 中定義的使用者、組、服務主體或託管標識,該標識請求對 Azure 資源的訪問。

通常,這些 Azure 資源受限制為頂級資源(例如:Azure 存儲帳戶)。 在 Azure 儲存以及 Azure 資料湖儲存 Gen2 的情況下,此機制已擴展到容器(檔案系統)資源。

要瞭解如何在儲存帳戶範圍內將角色分配給安全主體,請參閱[在 Azure 門戶中使用 RBAC 授予對 Azure Blob 和佇列資料的存取權限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 來賓用戶無法創建角色分配。

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>角色配置對檔案和目錄級存取控制列表的影響

雖然使用 RBAC 角色分配是控制存取許可權的一種強大機制,但相對於 ACL,它是一種非常粗粒度的機制。 RBAC 的最小粒度位於容器級別,這將以高於 ACL 的優先順序進行評估。 因此,如果將角色分配給容器範圍內的安全主體,則該安全主體具有與該容器中所有目錄和檔的角色關聯的授權級別,而不考慮 ACL 分配。

當安全主體通過[內置角色](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)或通過自定義角色被授予 RBAC 數據許可權時,這些許可權首先在請求的授權時進行評估。 如果請求的操作由安全主體的 RBAC 分配授權,則立即解決授權,並且不執行其他 ACL 檢查。 或者，如果安全性主體不具有 RBAC 指派，或要求作業不符合指派的權限，則會執行 ACL 檢查來判斷安全性主體是否已獲授權執行要求作業。

> [!NOTE]
> 如果安全主體已分配存儲 Blob 資料擁有者內建角色分配,則安全主體被視為*超級使用者*,並被授予對所有突變操作的完全存取許可權,包括為目錄或檔的所有者設定目錄或檔的所有者以及它們不是擁有者的目錄和檔的 ACL。 超級使用者存取權是唯一可變更資源擁有者的授權方式。

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>分享金鑰與共享存取簽署 (SAS) 認證

Azure 資料儲存第 2 代支援共享金鑰和 SAS 身份驗證方法。 這些身份驗證方法的一個特徵是沒有標識與調用方關聯,因此無法執行基於安全主體的許可權授權。

在共用密鑰的情況下,調用方有效地獲得了"超級使用者"訪問許可權,這意味著對所有資源的所有操作(包括設置擁有者和更改 ACL)的完全訪問許可權。

SAS 權杖會在其權杖中包含允許的權限。 SAS 權杖中包含的權限會有效地套用至所有授權決策，但不會再執行其他 ACL 檢查。

## <a name="access-control-lists-on-files-and-directories"></a>檔案和目錄的存取控制清單

您可以將安全主體與檔和目錄的訪問級別相關聯。 這些關聯在*存取控制清單 (ACL)* 中擷取。 儲存帳戶中的每個檔和目錄都有一個訪問控制清單。

> [!NOTE]
> ACL 僅適用於同一租戶中的安全主體。 

如果在存儲帳戶級別為安全主體分配了角色,則可以使用訪問控制列表授予該安全主體對特定檔和目錄的提升訪問許可權。

不能使用存取控制清單來提供低於角色分配授予的級別的訪問級別。 例如,如果將存儲 Blob[數據參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)角色分配給安全主體,則不能使用訪問控制清單來防止該安全主體寫入目錄。


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>使用存取控制清單設定檔和目錄等級權限

要設定檔和目錄等級權限,請參閱以下任一文章:

|||
|--------|-----------|
|Azure 儲存體總管 |[使用 Azure 儲存資源管理程式管理 Azure 資料湖儲存 Gen2 中的目錄、檔案和 ACL](data-lake-storage-explorer.md#managing-access)|
|.NET |[使用 .NET 管理 Azure 資料儲存單元 2 中的目錄、檔案和 ACL](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[使用 Java 管理 Azure 資料儲存庫第 2 代中的目錄、檔案和 ACL](data-lake-storage-directory-file-acl-java.md)|
|Python|[使用 Python 管理 Azure 資料系統儲存 Gen2 中的目錄、檔案與 ACL](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[使用 PowerShell 管理 Azure 資料儲存庫第 2 代中的目錄、檔案和 ACL](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[使用 Azure CLI 管理 Azure 資料儲存湖儲存 Gen2 中的目錄、檔案和 ACL](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[路徑 - 更新](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> 如果安全主體是*服務*主體,則使用服務主體的物件 ID 而不是相關應用註冊的對象 ID 非常重要。 要取得服務主體的物件代碼開啟 Azure CLI,然後使用此`az ad sp show --id <Your App ID> --query objectId`指令: 。 請確保將`<Your App ID>`占位符替換為應用註冊的應用 ID。

### <a name="types-of-access-control-lists"></a>存取控制列表的類型

有兩種類型的存取控制列表:存*取 ACL*和*預設 ACL。*

存取 ACL 可控制對物件的存取。 檔案和目錄均有存取 ACL。

預設 ACL 是與目錄關聯的 ACL 範本,該目錄確定在該目錄下創建的任何子項的訪問 ACL。 檔案沒有預設 ACL。

存取 ACL 和預設 ACL 有相同的結構。

> [!NOTE]
> 變更父代的預設 ACL 並不會影響現存子項目的存取 ACL 或預設 ACL。

### <a name="levels-of-permission"></a>權限等級

容器物件的權限為 **「讀取**」 與目錄,**並****寫入與目錄**,例如檔案與目錄,如下表所示:

|            |    檔案     |   目錄 |
|------------|-------------|----------|
| **讀取 (R)** | 可以讀取檔案的內容 | 需要 [讀取]**** 和 [執行]**** 才能列出目錄內容 |
| **寫入 (W)** | 可寫入或附加至檔案 | 需要 [寫入]**** 和 [執行]**** 才能在目錄中建立子項目 |
| **執行 (X)** | 不表示 Data Lake Storage Gen2 內容中的任何項目 | 需要周遊目錄的子項目 |

> [!NOTE]
> 如果僅使用 ACL (無 RBAC) 授予許可權,則要授予對檔案的安全主體讀取或寫入存取許可權,則需要向容器以及導致該檔的資料夾層次結構中的每個資料夾授予安全主體**執行**許可權。

#### <a name="short-forms-for-permissions"></a>權限的簡短形式

**RWX** 用來表示 [讀取 + 寫入 + 執行]****。 有更壓縮的數字形式存在，其中 [讀取 = 4]****、[寫入 = 2]**** 和 [執行 = 1]****，其總和代表各種權限。 以下有一些範例。

| 數值形式 | 簡短形式 |      意義     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 讀取 + 寫入 + 執行 |
| 5            | `R-X`        | 讀取 + 執行         |
| 4            | `R--`        | 讀取                   |
| 0            | `---`        | 沒有權限         |

#### <a name="permissions-inheritance"></a>權限繼承

在 Data Lake Storage Gen2 所使用的 POSIX 樣式模型中，項目的權限會儲存在項目本身。 換句話說，如果是在子項目建立後才設定權限，則無法從父項目繼承項目的權限。 必須是在子項目建立之前即已設定父項目的預設權限，才會繼承權限。

### <a name="common-scenarios-related-to-permissions"></a>權限相關的常見案例

下表列出了一些常見方案,以説明您瞭解在存儲帳戶上執行某些操作需要哪些許可權。

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

### <a name="users-and-identities"></a>使用者和身分識別

每個檔案和目錄都有這些身分識別的不同權限︰

- 擁有使用者
- 擁有群組
- 具名使用者
- 具名群組
- 具名服務主體
- 命名託管識別
- 所有其他使用者

使用者和群組的身分識別皆為 Azure Active Directory (Azure AD) 身分識別。 因此,除非另有說明,否則在數據存儲單元 Gen2 的上下文中,*用戶可以*引用 Azure AD 使用者、服務主體、託管標識或安全組。

#### <a name="the-owning-user"></a>擁有使用者

建立項目的使用者會自動成為項目的擁有使用者。 擁有使用者可以︰

* 變更所擁有檔案的權限。
* 只要擁有使用者也是目標群組的成員，請變更所擁有檔案的擁有群組。

> [!NOTE]
> 擁有使用者「無法」** 變更檔案或目錄的擁有使用者。 只有超級使用者可以變更檔案或目錄的擁有使用者。

#### <a name="the-owning-group"></a>擁有群組

在 POSIX ACL 中,每個使用者都與*主組*相關聯。 例如,使用者「Alice」可能屬於「財務」組。 Alice 也可能屬於多個組,但始終將一個組指定為其主組。 在 POSIX 中，當 Alice 會建立檔案時，該檔案的擁有群組會設定為她的主要群組，在此案例中為 "finance"。 除此之外，擁有群組的作用類似於指派給其他使用者/群組的權限。

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>指派新檔案或目錄的擁有群組

* **案例 1**: 根目錄 "/"。 此目錄是在創建資料儲存湖存儲Gen2容器時創建的。 在這種情況下,如果使用 OAuth 創建了容器,則將所屬組設置為創建容器的使用者。 如果使用分享金鑰、帳戶 SAS 或服務 SAS 建立容器,則擁有者和所屬群組將設定為 **$superuser**。
* **案例 2(** 所有其他情況):創建新項時,將從父目錄複製所屬組。

##### <a name="changing-the-owning-group"></a>變更擁有群組

可以變更擁有群組的對象︰
* 任何超級使用者。
* 擁有使用者，如果擁有使用者也是目標群組的成員。

> [!NOTE]
> 擁有群組無法變更檔案或目錄的 ACL。  雖然擁有組設置為在根目錄(上面為**案例1)** 的情況下創建帳戶的使用者,但單個使用者帳戶對於通過所屬組提供許可權無效。 您可以將此權限指派給有效的使用者群組 (如果適用的話)。

### <a name="access-check-algorithm"></a>存取檢查演算法

以下偽代碼表示存儲帳戶的訪問檢查演演演算法。

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
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>遮罩

如「存取檢查演算法」所說明，遮罩會限制具名使用者、擁有群組及具名群組的存取。  

> [!NOTE]
> 對於新的 Data Lake 儲存 Gen2 容器,根目錄的訪問 ACL ("/") 的遮罩預設為 750 目錄和 640 檔。 檔案不會接收 X 位元，因為它與僅限儲存的系統中包含的檔案無關。
>
> 遮罩可就個別的呼叫指定。 這可讓不同的取用系統 (例如叢集) 將不同的有效遮罩用於其檔案作業上。 指定於給定要求上的遮罩會完全覆寫預設遮罩。

#### <a name="the-sticky-bit"></a>黏性位元

粘性位是 POSIX 容器的更進階功能。 在 Data Lake Storage Gen2 的內容中，不太可能需要黏性位元。 總而言之，如果已在目錄上啟用黏性位元，子項目便只能由子項目的擁有使用者刪除或重新命名。

粘滯位未顯示在 Azure 門戶中。

### <a name="default-permissions-on-new-files-and-directories"></a>新檔案和目錄的預設權限

在現有目錄下建立新檔案或目錄時，父目錄的預設 ACL 可決定︰

- 子目錄的預設 ACL 和訪問 ACL。
- 子檔案的存取 ACL (檔案沒有預設 ACL)。

#### <a name="umask"></a>umask

在建立檔案或目錄時，可使用 umask 來修改子項目上的預設 ACL 設定方式。 umask 是父目錄上的一個 9 位元值，其中包含一個用於**擁有使用者**、**擁有群組**及**其他**的 RWX 值。

Azure Data Lake Storage Gen2 的 umask 是一個設定為 007 的常數值。 此值會轉譯成：

| umask 元件     | 數值形式 | 簡短形式 | 意義 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 針對擁有使用者，將父代的預設 ACL 複製到子系的存取 ACL | 
| umask.owning_group  |    0         |   `---`      | 針對擁有群組，將父代的預設 ACL 複製到子系的存取 ACL | 
| umask.other         |    7         |   `RWX`      | 針對其他，移除子系存取 ACL 上的所有權限 |

Azure Data Lake Storage Gen2 所使用的 umask 值實際上意謂著不論預設 ACL 的指示為何，依預設一律不會在新子系上傳輸用於**其他**的值。 

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 中 ACL 的相關常見問題

### <a name="do-i-have-to-enable-support-for-acls"></a>我必須啟用 ACL 的支援嗎？

否。 只要啟用分層命名空間 (HNS) 功能,儲存帳戶就為存儲帳戶啟用了透過 ACL 的存取控制。

如果 HNS 關閉，Azure RBAC 授權規則仍適用。

### <a name="what-is-the-best-way-to-apply-acls"></a>套用 ACL 的最佳方式為何？

一律使用 Azure AD 安全性群組作為 ACL 中指派的主體。 避免直接指派個別使用者或服務主體。 使用此結構，可讓您直接新增和移除使用者或服務主體，而不需要將 ACL 重新套用至整個目錄結構。 相對地，您只需要從適當的 Azure AD 安全性群組中加以新增或移除即可。 請記住，ACL 是不可繼承的，因此若要重新套用 ACL，必須更新每個檔案和子目錄的 ACL。 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>若要以遞迴方式刪除目錄及其內容，需要哪些權限？

- 調用方具有「超級用戶」許可權,

Or

- 父目錄必須具有 [寫入 + 執行] 權限。
- 要刪除的目錄及其中的每個目錄，都需要 [讀取 + 寫入 + 執行] 權限。

> [!NOTE]
> 您不需要寫入權限即可刪除目錄中的檔案。 此外，決不可刪除根目錄 "/"。

### <a name="who-is-the-owner-of-a-file-or-directory"></a>誰是檔案或目錄的擁有者？

檔案或目錄的建立者會成為擁有者。 對於根目錄,這是創建容器的用戶的標識。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>在建立檔案或目錄時，會將哪個群組設定為擁有群組？

擁有群組是從新檔案或目錄建立所在父目錄的擁有群組複製而來的。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>我是檔的使用者,但我沒有我需要的RWX許可權。 該怎麼辦？

擁有使用者可以變更檔案的權限，以取得本身所需的任何 RWX 權限。

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>為什麼我有時候會在 ACL 中看到 GUID？

如果項目代表使用者，而使用者已不存在於 Azure AD 中，則會顯示 GUID。 當使用者已離開公司，或已在 Azure AD 中刪除其帳戶時，通常會發生這種情形。 此外，服務主體和安全性群組並沒有使用者主體名稱 (UPN) 可資識別，因此會藉由其 OID 屬性 (GUID) 來顯示。

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>如何為服務主體正確設置 ACL?

為服務主體定義 ACL 時,請務必將*服務主體*的物件 ID (OID) 用於您創建的應用註冊。 請務必注意,已註冊的應用在特定 Azure AD 租戶中具有單獨的服務主體。 已註冊應用的 OID 在 Azure 門戶中可見,但*服務主體*具有另一個(不同的)OID。

要取得與應用程式的服務主體的 OID,可以使用指令`az ad sp show`。 指定應用程式識別為參數。 下面是一個示例,用於獲取與應用 ID 為應用 ID 的應用註冊相對應的服務主體的 OID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce。 在 Azure CLI 中執行下列命令：

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

將顯示 OID。

當服務主體具有正確的 OID 時,請轉到儲存資源管理員**管理訪問**頁以添加 OID 併為 OID 分配適當的許可權。 請確保選擇 **「保存**」。

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 是否支援 ACL 的繼承？

Azure RBAC 指派可以繼承。 分配從訂閱、資源組和存儲帳戶資源流到容器資源。

ACL 則不會繼承。 但預設 ACL 可以用來為父目錄下建立的子目錄和檔案設定 ACL。 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>何處可以進一步了解 POSIX 存取控制模型？

* [Linux 上的 POSIX 存取控制清單](https://www.linux.com/news/posix-acls-linux)
* [HDFS 權限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX 常見問題集](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu 上的 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [使用 Linux 上的存取控制列表的 ACL](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>另請參閱

* [Azure Data Lake Storage Gen2 概觀](../blobs/data-lake-storage-introduction.md)
