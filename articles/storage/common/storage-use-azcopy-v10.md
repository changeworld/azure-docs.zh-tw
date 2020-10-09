---
title: 使用 AzCopy v10 將資料複製或移動到 Azure 儲存體 |Microsoft Docs
description: AzCopy 是一種命令列公用程式，可讓您用來在儲存體帳戶之間複製資料。 本文可協助您下載 AzCopy、連線到您的儲存體帳戶，然後傳輸檔案。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 2f5b82f07ecff36ba95917a1f1b347c42ae7e074
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856770"
---
# <a name="get-started-with-azcopy"></a>開始使用 AzCopy

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 Blob 或檔案。 本文可協助您下載 AzCopy、連線到您的儲存體帳戶，然後傳輸檔案。

> [!NOTE]
> AzCopy **V10** 是目前支援的 AzCopy 版本。
>
> 如果您需要使用舊版的 AzCopy，請參閱本文的「 [使用舊版的 AzCopy](#previous-version) 」一節。

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>下載 AzCopy

首先，將 AzCopy V10 可執行檔下載至您電腦上的任何目錄。 AzCopy V10 只是一個可執行檔，因此不需要安裝任何專案。

- [Windows 64](https://aka.ms/downloadazcopy-v10-windows) 位 (zip) 
- [Windows 32](https://aka.ms/downloadazcopy-v10-windows-32bit) 位 (zip) 
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar) 
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip) 

這些檔案會壓縮成 zip 檔案 (Windows 和 Mac) 或 (Linux) 的 tar 檔案。 若要下載並解壓縮 Linux 上的 tar 檔案，請參閱您 Linux 發行版本的檔。

> [!NOTE]
> 如果您想要將資料複製到您的 [Azure 表格儲存體](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) 服務或從中複製資料，請安裝 [AzCopy 7.3 版](https://aka.ms/downloadazcopynet)。


## <a name="run-azcopy"></a>執行 AzCopy

為了方便起見，請考慮將 AzCopy 可執行檔的目錄位置新增至您的系統路徑，以方便使用。 如此一來，您就可以 `azcopy` 從系統上的任何目錄進行輸入。

如果您選擇不要將 AzCopy 目錄新增至您的路徑，您必須將目錄變更為 AzCopy 可執行檔的位置，並 `azcopy` `.\azcopy` 在 Windows PowerShell 命令提示字元中輸入或。

若要查看命令的清單，請輸入， `azcopy -h` 然後按下 enter 鍵。

若要瞭解特定命令，請只包含命令的名稱 (例如： `azcopy list -h`) 。

> [!div class="mx-imgBorder"]
> ![內嵌說明](media/storage-use-azcopy-v10/azcopy-inline-help.png)


若要尋找每個命令和命令參數的詳細參考檔，請參閱 [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> 作為 Azure 儲存體帳戶的擁有者，您不會自動獲指派存取資料的許可權。 您必須先決定要如何為儲存體服務提供授權認證，才能使用 AzCopy 來進行任何有意義的動作。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇您要如何提供授權認證

您可以使用 Azure Active Directory (AD) 或使用共用存取簽章 (SAS) 權杖來提供授權認證。

使用此資料表作為指南：

| 儲存體類型 | 目前支援的授權方法 |
|--|--|
|**Blob 儲存體** | Azure AD 和 SAS |
|**Blob 儲存體 (階層命名空間) ** | Azure AD 和 SAS |
|**檔案儲存體** | 僅限 SAS |

### <a name="option-1-use-azure-active-directory"></a>選項1：使用 Azure Active Directory

藉由使用 Azure Active Directory，您可以提供一次認證，而不需要將 SAS 權杖附加至每個命令。  

> [!NOTE]
> 在目前的版本中，如果您打算在儲存體帳戶之間複製 blob，則必須將 SAS 權杖附加至每個來源 URL。 您只能從目的地 URL 中省略 SAS 權杖。 如需範例，請參閱 [在儲存體帳戶之間複製 blob](storage-use-azcopy-blobs.md)。

您需要的授權層級，取決於您是否打算上傳檔案或只下載檔案。

如果您只想要下載檔案，請確認 [儲存體 Blob 資料讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) 已指派給您的使用者身分識別、受控識別或服務主體。

> 「使用者身分識別」、「受控識別」和「服務主體」都是一種 *安全性主體*，因此我們將在本文的其餘部分使用「 *安全性主體* 」一詞。

如果您想要上傳檔案，請確認其中一個角色已指派給您的安全性主體：

- [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)
- [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

這些角色可以指派給這些範圍中的安全性主體：

- 容器 (檔案系統) 
- 儲存體帳戶
- 資源群組
- 訂用帳戶

若要瞭解如何驗證和指派角色，請參閱 [使用 Azure 入口網站指派 Azure 角色以存取 blob 和佇列資料](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 請記住，Azure 角色指派最多可能需要五分鐘的時間來傳播。

如果您的安全性主體已新增至目標容器或目錄 (ACL) 的存取控制清單中，您就不需要將其中一個角色指派給安全性主體。 在 ACL 中，安全性主體需要目標目錄的寫入權限，以及容器和每個父目錄的 execute 許可權。

若要深入瞭解，請參閱 [Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-a-user-identity"></a>驗證使用者身分識別

在確認您的使用者身分識別已獲得必要的授權層級之後，請開啟命令提示字元，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login
```

如果您收到錯誤，請嘗試包含儲存體帳戶所屬組織的租使用者識別碼。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

將預留位置取代為 `<tenant-id>` 儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請選取 Azure 入口網站中 **> 目錄識別碼 Azure Active Directory > 屬性** 。

此命令傳回驗證碼和網站的 URL。 開啟網站，提供程式碼，然後選擇 [下一步]**** 按鈕。

![建立容器](media/storage-use-azcopy-v10/azcopy-login.png)

隨即會出現登入視窗。 在該視窗中，使用您的 Azure 帳戶認證登入 Azure 帳戶。 順利登入之後，您可以關閉瀏覽器視窗，然後開始使用 AzCopy。

<a id="service-principal"></a>

#### <a name="authenticate-a-service-principal"></a>驗證服務主體

如果您打算在不需使用者互動的情況下執行的腳本內使用 AzCopy，特別是在內部部署執行時，這是很好的選擇。 如果您打算在 Azure 中執行的 Vm 上執行 AzCopy，受控服務識別更容易管理。 若要深入瞭解，請參閱本文的「 [驗證受控識別](#managed-identity) 」一節。

執行腳本之前，您必須以互動方式至少登入一次，以便您可以提供服務主體的認證給 AzCopy。  這些認證會儲存在安全和加密的檔案中，因此您的腳本不需要提供該機密資訊。

您可以使用用戶端密碼或使用與服務主體應用程式註冊相關聯之憑證的密碼，來登入您的帳戶。

若要深入瞭解如何建立服務主體，請參閱 [如何：使用入口網站來建立可存取資源 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

若要深入瞭解服務主體的一般資訊，請參閱[Azure Active Directory 中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

##### <a name="using-a-client-secret"></a>使用用戶端密碼

首先，將 `AZCOPY_SPA_CLIENT_SECRET` 環境變數設定為服務主體應用程式註冊的用戶端密碼。

> [!NOTE]
> 請務必從命令提示字元設定此值，而不是在作業系統的環境變數設定中設定。 如此一來，此值只適用于目前的會話。

此範例示範如何在 PowerShell 中進行這項作業。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 請考慮使用如本範例所示的提示。 如此一來，您的密碼就不會出現在主控台的命令歷程記錄中。  

接著，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

將預留位置取代為 `<application-id>` 您服務主體應用程式註冊的應用程式識別碼。 將預留位置取代為 `<tenant-id>` 儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請選取 Azure 入口網站中 **> 目錄識別碼 Azure Active Directory > 屬性** 。 

##### <a name="using-a-certificate"></a>使用憑證

如果您想要使用自己的認證來進行授權，您可以將憑證上傳至您的應用程式註冊，然後使用該憑證來登入。

除了將憑證上傳至您的應用程式註冊之外，您還需要將憑證複本儲存至將執行 AzCopy 的電腦或 VM。 這份憑證複本應該在中。PFX 或。PEM 格式，而且必須包含私密金鑰。 私密金鑰應受密碼保護。 如果您使用的是 Windows，而且您的憑證只存在於憑證存放區中，請務必將該憑證匯出到 PFX 檔案， (包含私密金鑰) 。 如需指引，請參閱 [匯出-get-pfxcertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

接下來，將 `AZCOPY_SPA_CERT_PASSWORD` 環境變數設定為憑證密碼。

> [!NOTE]
> 請務必從命令提示字元設定此值，而不是在作業系統的環境變數設定中設定。 如此一來，此值只適用于目前的會話。

此範例說明如何在 PowerShell 中進行這項工作。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接著，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

將 `<path-to-certificate-file>` 預留位置取代為憑證檔案的相對或完整路徑。 AzCopy 會儲存此憑證的路徑，但不會儲存憑證的複本，因此請務必將該憑證保留在原處。 將預留位置取代為 `<tenant-id>` 儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請選取 Azure 入口網站中 **> 目錄識別碼 Azure Active Directory > 屬性** 。

> [!NOTE]
> 請考慮使用如本範例所示的提示。 如此一來，您的密碼就不會出現在主控台的命令歷程記錄中。 

<a id="managed-identity"></a>

#### <a name="authenticate-a-managed-identity"></a>驗證受控識別

如果您打算在不需使用者互動的情況下執行的腳本內使用 AzCopy，且腳本 (VM) 從 Azure 虛擬機器執行，這是一個絕佳的選項。 使用此選項時，您不需要在 VM 上儲存任何認證。

您可以使用您已在 VM 上啟用的整個系統受控識別，或使用您已指派給 VM 的使用者指派受控識別的用戶端識別碼、物件識別碼或資源識別碼，來登入您的帳戶。

若要深入瞭解如何啟用全系統受控識別，或建立使用者指派的受控識別，請參閱 [使用 Azure 入口網站在 VM 上設定 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)識別。

##### <a name="using-a-system-wide-managed-identity"></a>使用全系統受控識別

首先，請確定您已在 VM 上啟用全系統受控識別。 請參閱 [系統指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)。

然後，在命令主控台中輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>使用使用者指派的受控識別

首先，請確定您已在 VM 上啟用使用者指派的受控識別。 請參閱 [使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)。

然後，在命令主控台中，輸入下列任何命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

將預留位置取代為 `<client-id>` 使用者指派受控識別的用戶端識別碼。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

將預留位置取代為 `<object-id>` 使用者指派受控識別的物件識別碼。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

將預留位置取代為 `<resource-id>` 使用者指派受控識別的資源識別碼。

### <a name="option-2-use-a-sas-token"></a>選項2：使用 SAS 權杖

您可以將 SAS 權杖附加至每個在 AzCopy 命令中使用的來源或目的地 URL。

此範例命令會以遞迴方式將資料從本機目錄複寫到 blob 容器。 虛構的 SAS 權杖會附加至容器 URL 的結尾。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要深入瞭解 SAS 權杖，以及如何取得 SAS 權杖，請參閱 [使用共用存取簽章 (SAS) ](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)。

## <a name="transfer-files"></a>傳輸檔案

驗證您的身分識別或取得 SAS 權杖之後，您就可以開始傳送檔案。

若要尋找範例命令，請參閱這些文章。

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [使用 AzCopy 和 Azure Stack 儲存體傳輸資料](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>在腳本中使用 AzCopy

### <a name="obtain-a-static-download-link"></a>取得靜態下載連結

經過一段時間後，AzCopy [下載連結](#download-and-install-azcopy) 會指向 AzCopy 的新版本。 如果您的腳本下載 AzCopy，如果較新版本的 AzCopy 修改您的腳本所依賴的功能，腳本可能會停止運作。

若要避免這些問題，請取得 AzCopy 目前版本的靜態 (不變更) 連結。 如此一來，您的腳本就會在每次執行時下載相同的 AzCopy 完全相同版本。

若要取得連結，請執行下列命令：

| 作業系統  | Command |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> 若是 Linux， `--strip-components=1` 命令上的 `tar` 命令會移除包含版本名稱的最上層資料夾，並改為將二進位檔直接解壓縮到目前的資料夾中。 這可讓您只更新 URL 來更新腳本的新版本 `azcopy` `wget` 。

URL 會出現在此命令的輸出中。 然後，您的腳本就可以使用該 URL 來下載 AzCopy。

| 作業系統  | Command |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>在 SAS 權杖中 Escape 特殊字元

在副檔名為的批次檔中 `.cmd` ，您必須將 `%` 出現在 SAS 權杖中的字元進行換用。 若要這麼做，您可以 `%` `%` 在 SAS 權杖字串中的現有字元旁新增額外的字元。

### <a name="run-scripts-by-using-jenkins"></a>使用 Jenkins 執行腳本

如果您打算使用 [Jenkins](https://jenkins.io/) 來執行腳本，請務必在腳本的開頭放置下列命令。

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>在 Azure 儲存體總管中使用 AzCopy

[儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 使用 AzCopy 來執行它的所有資料傳輸作業。 如果您想要利用 AzCopy 的效能優勢，但想要使用圖形化使用者介面而非命令列來與檔案互動，您可以使用 [儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 。

儲存體總管會使用您的帳戶金鑰來執行操作，因此在您登入儲存體總管之後，就不需要提供額外的授權認證。

<a id="previous-version"></a>

## <a name="use-the-previous-version-of-azcopy"></a>使用舊版 AzCopy

如果您需要使用舊版的 AzCopy，請參閱下列其中一個連結：

- [AzCopy on Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (v7) ](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>對 AzCopy 進行設定、最佳化及疑難排解

請參閱 [設定、優化和疑難排解 AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>後續步驟

如果您有任何疑問、問題或一般意見反應，請 [在 GitHub 頁面上](https://github.com/Azure/azure-storage-azcopy) 提交。
