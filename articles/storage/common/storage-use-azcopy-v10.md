---
title: 使用 AzCopy v10 將資料複製或移動到 Azure 存儲 |微軟文檔
description: AzCopy 是一個命令列實用程式，可用於將資料複製到存儲帳戶、從存儲帳戶中或存儲帳戶之間。 本文可説明您下載 AzCopy、連接到存儲帳戶，然後傳輸檔。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 90adb3b973b6952d10ef89e4b40d0fc68489e224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061099"
---
# <a name="get-started-with-azcopy"></a>開始使用 AzCopy

AzCopy 是一個命令列實用程式，可用於將 Blob 或檔案複製到存儲帳戶或從存儲帳戶複製。 本文可説明您下載 AzCopy、連接到存儲帳戶，然後傳輸檔。

> [!NOTE]
> AzCopy **V10**是當前支援的 AzCopy 版本。
>
> 如果需要使用 AzCopy **v8.1，** 請參閱使用本文[的 AzCopy 早期版本](#previous-version)部分。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>下載 AzCopy

首先，將 AzCopy V10 可執行檔下載到電腦上的任何目錄。 AzCopy V10 只是一個可執行檔，因此沒有什麼可安裝的。

- [視窗 64 位](https://aka.ms/downloadazcopy-v10-windows)（壓縮）
- [視窗 32 位](https://aka.ms/downloadazcopy-v10-windows-32bit)（壓縮）
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

這些檔被壓縮為 ZIP 檔案（Windows 和 Mac）或 tar 檔 （Linux）。 要下載並解壓縮 Linux 上的 tar 檔，請參閱 Linux 發行版本的文檔。

> [!NOTE]
> 如果要將資料複製到[Azure 表存儲](https://docs.microsoft.com/azure/storage/tables/table-storage-overview)服務並從中複製，請安裝[AzCopy 版本 7.3](https://aka.ms/downloadazcopynet)。


## <a name="run-azcopy"></a>運行阿茲比

為方便起見，請考慮將 AzCopy 可執行檔的目錄位置添加到系統路徑中，以便方便使用。 這樣，就可以從系統上`azcopy`的任何目錄鍵入。

如果選擇不將 AzCopy 目錄添加到路徑中，則必須將目錄更改為 AzCopy 可執行檔和類型`azcopy`的位置，或在`.\azcopy`Windows PowerShell 命令提示中。

要查看命令清單，請鍵入`azcopy -h`，然後按 ENTER 鍵。

要瞭解特定命令，只需包括命令的名稱（例如： `azcopy list -h`。

![內聯説明](media/storage-use-azcopy-v10/azcopy-inline-help.png)

要查找每個命令和命令參數的詳細參考文檔，請參閱[azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> 作為 Azure 存儲帳戶的擁有者，不會自動分配訪問資料的許可權。 在使用 AzCopy 執行任何有意義的操作之前，您需要決定如何向存儲服務提供授權憑據。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇提供授權憑據的方式

可以使用 Azure 活動目錄 （AD） 或使用共用訪問簽名 （SAS） 權杖來提供授權憑據。

使用此表作為指南：

| 儲存體類型 | 當前支援的授權方法 |
|--|--|
|**Blob 儲存體** | Azure AD & SAS |
|**Blob 存儲（階層命名空間）** | Azure AD & SAS |
|**檔案儲存體** | 僅限 SAS |

### <a name="option-1-use-azure-active-directory"></a>選項 1：使用 Azure 活動目錄

通過使用 Azure 活動目錄，可以提供一次憑據，而不必將 SAS 權杖追加到每個命令。  

> [!NOTE]
> 在當前版本中，如果計畫在存儲帳戶之間複製 Blob，則必須為每個源 URL 追加 SAS 權杖。 您只能從目標 URL 省略 SAS 權杖。 有關示例，請參閱[在存儲帳戶之間複製 blob。](storage-use-azcopy-blobs.md)

您需要的授權級別取決於您計畫上傳檔還是僅下載檔案。

如果只想下載檔案，請驗證[存儲 Blob 資料讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)是否已分配給您的使用者標識、託管標識或服務主體。

> 使用者標識、託管標識和服務主體都是一種*安全主體*類型，因此，我們將在本文中的其餘部分使用術語*安全主體*。

如果要上載檔，請驗證這些角色之一已分配給您的安全主體：

- [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

這些角色可以分配給以下任一作用域中的安全主體：

- 容器（檔案系統）
- 儲存體帳戶
- 資源群組
- 訂用帳戶

要瞭解如何驗證和分配角色，請參閱在[Azure 門戶中使用 RBAC 授予對 Azure Blob 和佇列資料的存取權限](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 請記住，RBAC 角色指派最多可能需要五分鐘才能傳播。

如果將安全主體添加到目標容器或目錄的存取控制清單 （ACL）中，則不需要將這些角色之一分配給安全主體。 在 ACL 中，安全主體需要對目標目錄寫入權限，並在容器和每個父目錄中執行許可權。

要瞭解更多資訊，請參閱[Azure 資料湖存儲 Gen2 中的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-a-user-identity"></a>驗證使用者身份

驗證使用者標識已獲得必要的授權級別後，打開命令提示符，鍵入以下命令，然後按 ENTER 鍵。

```azcopy
azcopy login
```

如果您屬於多個組織，請包括存儲帳戶所屬組織的租戶 ID。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

將`<tenant-id>`預留位置替換為存儲帳戶所屬組織的租戶 ID。 要查找租戶 ID，請在 Azure 門戶中選擇**Azure 活動目錄>屬性>目錄 ID。**

此命令傳回驗證碼和網站的 URL。 開啟網站，提供程式碼，然後選擇 [下一步]**** 按鈕。

![建立容器](media/storage-use-azcopy-v10/azcopy-login.png)

隨即會出現登入視窗。 在該視窗中，使用您的 Azure 帳戶認證登入 Azure 帳戶。 順利登入之後，您可以關閉瀏覽器視窗，然後開始使用 AzCopy。

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>驗證服務主體

如果您計畫在沒有使用者交互的情況下運行的腳本內部使用 AzCopy，尤其是在本地運行時，這是一個不錯的選擇。 如果計畫在 Azure 中運行的 VM 上運行 AzCopy，則託管服務標識更易於管理。 要瞭解更多資訊，請參閱本文[的託管標識身份驗證](#managed-identity)部分。

在運行腳本之前，您必須至少交互登錄一次，以便向 AzCopy 提供服務主體的憑據。  這些憑據存儲在安全和加密檔中，因此腳本不必提供該敏感資訊。

您可以使用用戶端金鑰或使用與服務主體的應用註冊關聯的證書的密碼登錄您的帳戶。

要瞭解有關創建服務主體的更多內容，請參閱[：使用門戶創建可以訪問資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

要瞭解有關服務主體的一般內容，請參閱[Azure 活動目錄中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>使用用戶端金鑰

首先將`AZCOPY_SPA_CLIENT_SECRET`環境變數設置為服務主體的應用註冊中的用戶端機密。

> [!NOTE]
> 請確保從命令提示符（而不是作業系統的環境變數設置中）設置此值。 這樣，該值僅對當前會話可用。

此示例演示如何在 PowerShell 中執行此操作。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 請考慮使用提示，如本示例所示。 這樣，您的密碼將不會顯示在主控台的命令歷史記錄中。  

接下來，鍵入以下命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

將`<application-id>`預留位置替換為服務主體的應用註冊的應用程式 ID。 將`<tenant-id>`預留位置替換為存儲帳戶所屬組織的租戶 ID。 要查找租戶 ID，請在 Azure 門戶中選擇**Azure 活動目錄>屬性>目錄 ID。** 

##### <a name="using-a-certificate"></a>使用憑證

如果您更喜歡使用自己的憑據進行授權，則可以將證書上載到應用註冊，然後使用該證書登錄。

除了將證書上載到應用註冊之外，還需要將證書的副本保存到運行 AzCopy 的電腦或 VM。 證書的此副本應位於 中。PFX 或 。PEM 格式，並且必須包括私密金鑰。 私密金鑰應受密碼保護。 如果您使用的是 Windows，並且您的證書僅存在於憑證存放區中，請確保將該證書匯出到 PFX 檔（包括私密金鑰）。 有關指導，請參閱[匯出 Pfx 證書](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

接下來，將`AZCOPY_SPA_CERT_PASSWORD`環境變數設置為證書密碼。

> [!NOTE]
> 請確保從命令提示符（而不是作業系統的環境變數設置中）設置此值。 這樣，該值僅對當前會話可用。

此示例演示如何在 PowerShell 中執行此操作。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接下來，鍵入以下命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

將`<path-to-certificate-file>`預留位置替換為證書檔的相對或完全限定路徑。 AzCopy 保存此證書的路徑，但它不會保存證書的副本，因此請確保保持該證書的位置。 將`<tenant-id>`預留位置替換為存儲帳戶所屬組織的租戶 ID。 要查找租戶 ID，請在 Azure 門戶中選擇**Azure 活動目錄>屬性>目錄 ID。**

> [!NOTE]
> 請考慮使用提示，如本示例所示。 這樣，您的密碼將不會顯示在主控台的命令歷史記錄中。 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>驗證託管標識

如果計畫在沒有使用者交互的情況下運行的腳本內部使用 AzCopy，並且腳本從 Azure 虛擬機器 （VM） 運行，則這是一個不錯的選擇。 使用此選項時，您不必在 VM 上存儲任何憑據。

您可以使用在 VM 上啟用的系統範圍託管標識，或使用分配給 VM 的使用者分配的託管標識的用戶端 ID、物件識別碼 或資源識別碼 登錄帳戶。

要瞭解有關如何啟用系統範圍的託管標識或創建使用者分配的託管標識，請參閱[使用 Azure 門戶在 VM 上配置 Azure 資源的託管標識](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。

##### <a name="using-a-system-wide-managed-identity"></a>使用系統範圍的託管標識

首先，請確保在 VM 上啟用了系統範圍的託管標識。 請參閱[系統分配的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)。

然後，在命令主控台中鍵入以下命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>使用使用者分配的託管標識

首先，請確保在 VM 上啟用了使用者分配的託管標識。 請參閱[使用者分配的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)。

然後，在命令主控台中鍵入以下任一命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

將`<client-id>`預留位置替換為使用者分配的託管標識的用戶端 ID。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

將`<object-id>`預留位置替換為使用者分配的託管標識的物件識別碼。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

將`<resource-id>`預留位置替換為使用者分配的託管標識的資源識別碼。

### <a name="option-2-use-a-sas-token"></a>選項 2：使用 SAS 權杖

您可以將 SAS 權杖追加到 AzCopy 命令中使用的每個源或目標 URL。

此示例命令遞迴地將資料從本地目錄複寫到 Blob 容器。 虛構的 SAS 權杖將追加到容器 URL 的末尾。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

要瞭解有關 SAS 權杖以及如何獲取權杖的更多，請參閱[使用共用訪問簽名 （SAS）。](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

## <a name="transfer-files"></a>傳輸檔案

驗證身份或獲取 SAS 權杖後，可以開始傳輸檔。

要查找示例命令，請參閱其中任何一條。

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [使用 AzCopy 和 Azure 堆疊存儲傳輸資料](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>在腳本中使用 AzCopy

### <a name="obtain-a-static-download-link"></a>獲取靜態下載連結

隨著時間的推移，AzCopy[下載連結](#download-and-install-azcopy)將指向新版本的 AzCopy。 如果腳本下載 AzCopy，則如果較新版本的 AzCopy 修改腳本所依賴的功能，腳本可能會停止工作。

為了避免這些問題，請獲取指向當前版本的 AzCopy 的靜態（未更改）連結。 這樣，腳本每次運行時都會下載相同的 AzCopy 版本。

要獲取連結，運行以下命令：

| 作業系統  | Command |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> 對於 Linux，`--strip-components=1``tar`在 命令上刪除包含版本名稱的頂級資料夾，而是將二進位檔案直接提取到當前資料夾中。 這允許僅更新`azcopy``wget`URL 即可使用 新版本更新腳本。

URL 將顯示在此命令的輸出中。 然後，腳本可以使用該 URL 下載 AzCopy。

| 作業系統  | Command |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>在 SAS 權杖中轉義特殊字元

在具有副檔名的`.cmd`批次檔中，必須轉義 SAS`%`權杖中顯示的字元。 可以通過在 SAS 權杖字串`%`中現有`%`字元旁邊添加一個附加字元來執行此操作。

### <a name="run-scripts-by-using-jenkins"></a>使用 Jenkins 運行腳本

如果計畫使用[Jenkins](https://jenkins.io/)運行腳本，請確保將以下命令放在腳本的開頭。

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>在 Azure 存儲資源管理器中使用 AzCopy

[存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)使用 AzCopy 執行其所有資料傳輸操作。 如果要利用 AzCopy 的性能優勢，則可以使用[存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)，但您希望使用圖形化使用者介面而不是命令列與檔進行交互。

存儲資源管理器使用帳戶金鑰執行操作，因此在登錄到存儲資源管理器後，您無需提供其他授權憑據。

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用早期版本的 AzCopy

如果您需要使用早期版本的 AzCopy（AzCopy v8.1），請參閱以下任一連結：

- [AzCopy on Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>對 AzCopy 進行設定、最佳化及疑難排解

請參閱[配置、優化和排除 AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>後續步驟

如果您有疑問、問題或一般回饋，請將這些問題提交[到 GitHub](https://github.com/Azure/azure-storage-azcopy)頁面上。
