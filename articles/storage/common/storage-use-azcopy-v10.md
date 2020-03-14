---
title: 使用 AzCopy v10 將資料複製或移動到 Azure 儲存體 |Microsoft Docs
description: AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製資料。 本文可協助您下載 AzCopy、連接到您的儲存體帳戶，然後傳輸檔案。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c4e2681121a15e0b84a11c7cf35119c3f1b69f11
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268206"
---
# <a name="get-started-with-azcopy"></a>開始使用 AzCopy

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 blob 或檔案。 本文可協助您下載 AzCopy、連接到您的儲存體帳戶，然後傳輸檔案。

> [!NOTE]
> AzCopy **V10**是目前支援的 AzCopy 版本。
>
> 如果您需要使用 AzCopy 的**8.1**版，請參閱本文的[使用舊版 AzCopy](#previous-version)一節。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>下載 AzCopy

首先，將 AzCopy V10 可執行檔下載到您電腦上的任何目錄。 AzCopy V10 只是一個可執行檔，因此沒有要安裝的東西。

- [Windows 64 位](https://aka.ms/downloadazcopy-v10-windows)（zip）
- [Windows 32 位](https://aka.ms/downloadazcopy-v10-windows-32bit)（zip）
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

這些檔案會壓縮成 zip 檔案（Windows 和 Mac）或 tar 檔案（Linux）。 若要在 Linux 上下載並解壓縮 tar 檔案，請參閱 Linux 散發套件的檔。

> [!NOTE]
> 如果您想要將資料複製到您的[Azure 資料表儲存體](https://docs.microsoft.com/azure/storage/tables/table-storage-overview)服務，請安裝[AzCopy 7.3 版](https://aka.ms/downloadazcopynet)。


## <a name="run-azcopy"></a>執行 AzCopy

為了方便起見，請考慮將 AzCopy 可執行檔的目錄位置新增至您的系統路徑，以方便使用。 如此一來，您就可以從系統上的任何目錄輸入 `azcopy`。

如果您選擇不要將 AzCopy 目錄新增至您的路徑，則必須將目錄變更為 AzCopy 可執行檔的位置，並在 Windows PowerShell 命令提示字元中輸入 `azcopy` 或 `.\azcopy`。

若要查看命令清單，請輸入 `azcopy -h`，然後按 ENTER 鍵。

若要瞭解特定的命令，請只包含命令的名稱（例如： `azcopy list -h`）。

![內嵌說明](media/storage-use-azcopy-v10/azcopy-inline-help.png)

若要尋找每個命令和命令參數的詳細參考檔，請參閱[azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> 身為 Azure 儲存體帳戶的擁有者，您不會自動獲指派存取資料的許可權。 您必須先決定要如何將授權認證提供給儲存體服務，才可以執行任何有意義的 AzCopy。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇您要如何提供授權認證

您可以使用 Azure Active Directory （AD）或使用共用存取簽章（SAS）權杖來提供授權認證。

使用此表格作為指南：

| 儲存體類型 | 目前支援的授權方法 |
|--|--|
|**Blob 儲存體** | Azure AD & SAS |
|**Blob 儲存體（階層命名空間）** | Azure AD & SAS |
|**檔案儲存體** | 僅限 SAS |

### <a name="option-1-use-azure-active-directory"></a>選項1：使用 Azure Active Directory

藉由使用 Azure Active Directory，您可以只提供認證一次，而不需要將 SAS 權杖附加至每個命令。  

> [!NOTE]
> 在目前版本中，如果您打算在儲存體帳戶之間複製 blob，則必須將 SAS 權杖附加至每個來源 URL。 您只能從目的地 URL 省略 SAS 權杖。 如需範例，請參閱[在儲存體帳戶之間複製 blob](storage-use-azcopy-blobs.md)。

您所需的授權層級取決於您是否計畫上傳檔案，或只是下載檔案。

如果您只想要下載檔案，請確認[儲存體 Blob 資料讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)已指派給您的使用者身分識別、受控識別或服務主體。

> 使用者身分識別、受控識別和服務主體都是一種*安全性主體*類型，因此我們將在本文的其餘部分使用「*安全性主體*」一詞。

如果您想要上傳檔案，請確認其中一個角色已指派給您的安全性主體：

- [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

這些角色可以指派給任何範圍內的安全性主體：

- 容器（檔案系統）
- 儲存體帳戶
- 資源群組
- 訂用帳戶

若要瞭解如何驗證和指派角色，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure blob 和佇列資料的存取權](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 請記住，RBAC 角色指派最多可能需要五分鐘的時間來傳播。

如果您的安全性主體已新增至目標容器或目錄的存取控制清單（ACL），則您不需要將這些角色指派給您的安全性主體。 在 ACL 中，您的安全性主體需要目標目錄的寫入權限，以及容器和每個父目錄的執行許可權。

若要深入瞭解，請參閱[Azure Data Lake Storage Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-a-user-identity"></a>驗證使用者身分識別

在確認您的使用者身分識別已獲得必要的授權層級之後，請開啟命令提示字元，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login
```

如果您隸屬于多個組織，請包括儲存體帳戶所屬組織的租使用者識別碼。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

將 `<tenant-id>` 預留位置取代為儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請在 Azure 入口網站中選取 [ **Azure Active Directory > 屬性] > [目錄識別碼**]。

此命令傳回驗證碼和網站的 URL。 開啟網站，提供程式碼，然後選擇 [下一步] 按鈕。

![建立容器](media/storage-use-azcopy-v10/azcopy-login.png)

隨即會出現登入視窗。 在該視窗中，使用您的 Azure 帳戶認證登入 Azure 帳戶。 順利登入之後，您可以關閉瀏覽器視窗，然後開始使用 AzCopy。

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>驗證服務主體

如果您打算在不需使用者互動的腳本內使用 AzCopy，特別是在內部部署執行時，這是很好的選擇。 如果您打算在 Azure 中執行的 Vm 上執行 AzCopy，受控服務識別會比較容易管理。 若要深入瞭解，請參閱本文的[驗證受控識別](#managed-identity)一節。

執行腳本之前，您必須以互動方式至少一次登入，讓您可以使用服務主體的認證來提供 AzCopy。  這些認證會儲存在安全的加密檔案中，讓您的腳本不需要提供敏感性資訊。

您可以使用用戶端密碼或與服務主體的應用程式註冊相關聯之憑證的密碼，來登入您的帳戶。

若要深入瞭解如何建立服務主體，請參閱[如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

若要深入瞭解服務主體的一般資訊，請參閱[Azure Active Directory 中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>使用用戶端密碼

首先，將 `AZCOPY_SPA_CLIENT_SECRET` 環境變數設定為服務主體之應用程式註冊的用戶端密碼。

> [!NOTE]
> 請務必從您的命令提示字元設定此值，而不是作業系統的環境變數設定。 如此一來，此值僅適用于目前的會話。

這個範例示範如何在 PowerShell 中執行這項操作。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 請考慮使用如下列範例所示的提示。 如此一來，您的密碼就不會出現在主控台的命令歷程記錄中。  

接下來，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

以服務主體之應用程式註冊的應用程式識別碼取代 `<application-id>` 預留位置。 將 `<tenant-id>` 預留位置取代為儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請在 Azure 入口網站中選取 [ **Azure Active Directory > 屬性] > [目錄識別碼**]。 

##### <a name="using-a-certificate"></a>使用憑證

如果您想要使用自己的認證來進行授權，您可以將憑證上傳至您的應用程式註冊，然後使用該憑證登入。

除了將憑證上傳至您的應用程式註冊之外，您還需要將憑證的複本儲存到 AzCopy 執行所在的電腦或 VM。 這個憑證複本應該在中。PFX 或。PEM 格式，而且必須包含私密金鑰。 私密金鑰應受密碼保護。 如果您使用的是 Windows，而您的憑證只存在於憑證存放區中，請務必將該憑證匯出到 PFX 檔案（包括私密金鑰）。 如需指引，請參閱[Export-get-pfxcertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

接下來，將 `AZCOPY_SPA_CERT_PASSWORD` 環境變數設定為憑證密碼。

> [!NOTE]
> 請務必從您的命令提示字元設定此值，而不是作業系統的環境變數設定。 如此一來，此值僅適用于目前的會話。

這個範例示範如何在 PowerShell 中執行這項工作。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接下來，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

將 `<path-to-certificate-file>` 預留位置取代為憑證檔案的相對或完整路徑。 AzCopy 會儲存此憑證的路徑，但不會儲存憑證的複本，因此請務必將該憑證保留在原處。 將 `<tenant-id>` 預留位置取代為儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請在 Azure 入口網站中選取 [ **Azure Active Directory > 屬性] > [目錄識別碼**]。

> [!NOTE]
> 請考慮使用如下列範例所示的提示。 如此一來，您的密碼就不會出現在主控台的命令歷程記錄中。 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>驗證受控識別

如果您打算在不需使用者互動的腳本內使用 AzCopy，而且腳本是從 Azure 虛擬機器（VM）執行，這就是絕佳的選項。 使用此選項時，您不需要在 VM 上儲存任何認證。

您可以登入您的帳戶，方法是使用您在 VM 上啟用的全系統受控識別，或使用您已指派給 VM 的使用者指派受控識別的用戶端識別碼、物件識別碼或資源識別碼。

若要深入瞭解如何啟用全系統受控識別或建立使用者指派的受控識別，請參閱[使用 Azure 入口網站在 VM 上設定 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)識別。

##### <a name="using-a-system-wide-managed-identity"></a>使用全系統受控識別

首先，請確定您已在 VM 上啟用全系統受控識別。 請參閱[系統指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)。

然後，在您的命令主控台中輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>使用使用者指派的受控識別

首先，請確定您已在 VM 上啟用使用者指派的受控識別。 請參閱[使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)。

然後，在您的命令主控台中，輸入下列任何命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

以使用者指派的受控識別的用戶端識別碼取代 `<client-id>` 預留位置。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

以使用者指派的受控識別的物件識別碼取代 `<object-id>` 預留位置。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

以使用者指派受控識別的資源識別碼取代 `<resource-id>` 預留位置。

### <a name="option-2-use-a-sas-token"></a>選項2：使用 SAS 權杖

您可以將 SAS 權杖附加至在 AzCopy 命令中使用的每個來源或目的地 URL。

此範例命令會以遞迴方式將資料從本機目錄複寫到 blob 容器。 將虛構的 SAS 權杖附加至容器 URL 的結尾。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要深入瞭解 SAS 權杖以及如何取得它，請參閱[使用共用存取簽章（SAS）](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)。

## <a name="transfer-files"></a>傳輸檔案

在驗證您的身分識別或取得 SAS 權杖之後，您就可以開始傳輸檔案。

若要尋找範例命令，請參閱任何一篇文章。

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [使用 AzCopy 和 Azure Stack 儲存體傳輸資料](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>在腳本中使用 AzCopy

### <a name="obtain-a-static-download-link"></a>取得靜態下載連結

經過一段時間後，AzCopy[下載連結](#download-and-install-azcopy)會指向新版本的 AzCopy。 如果您的腳本下載 AzCopy，當較新版本的 AzCopy 修改腳本所相依的功能時，腳本可能會停止運作。

若要避免這些問題，請取得 AzCopy 目前版本的靜態（未變更）連結。 如此一來，您的腳本就會在每次執行時下載相同的 AzCopy 版本。

若要取得連結，請執行此命令：

| 作業系統  | Command |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> 針對 Linux，`tar` 命令上的 `--strip-components=1` 會移除包含版本名稱的最上層資料夾，並改為直接將二進位檔解壓縮至目前的資料夾。 如此一來，只要更新 `wget` URL，就可以使用新版本的 `azcopy` 來更新腳本。

此 URL 會出現在此命令的輸出中。 接著，您的腳本就可以使用該 URL 來下載 AzCopy。

| 作業系統  | Command |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>在 SAS 權杖中換用特殊字元

在具有 `.cmd` 延伸模組的批次檔中，您必須將出現在 SAS 權杖中的 `%` 字元加以轉義。 若要這麼做，您可以在 SAS 權杖字串中的現有 `%` 字元旁新增 `%` 字元。

### <a name="run-scripts-by-using-jenkins"></a>使用 Jenkins 執行腳本

如果您打算使用[Jenkins](https://jenkins.io/)來執行腳本，請務必將下列命令放在腳本的開頭。

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>在 Azure 儲存體總管中使用 AzCopy

[儲存體總管](https://azure.microsoft.com/features/storage-explorer/)使用 AzCopy 來執行其所有的資料傳輸作業。 如果您想要利用 AzCopy 的效能優勢，可以使用[儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，但是您想要使用圖形化使用者介面，而不是命令列來與您的檔案互動。

儲存體總管會使用您的帳戶金鑰來執行作業，因此當您登入儲存體總管之後，就不需要提供額外的授權認證。

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用舊版的 AzCopy

如果您需要使用舊版的 AzCopy （AzCopy 8.1），請參閱下列其中一個連結：

- [Windows 上的 AzCopy （v8）](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Linux 上的 AzCopy （v8）](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>設定、優化和疑難排解 AzCopy

請參閱[設定、優化和疑難排解 AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>後續步驟

如果您有任何問題、問題或一般意見反應，請[在 GitHub 頁面上](https://github.com/Azure/azure-storage-azcopy)提交。
