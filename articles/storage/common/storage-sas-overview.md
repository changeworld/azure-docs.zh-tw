---
title: 使用 (SAS) 的共用存取簽章來授與資料的有限存取權
titleSuffix: Azure Storage
description: 瞭解如何使用共用存取簽章 (SAS) 將存取權委派給 Azure 儲存體資源，包括 blob、佇列、資料表和檔案。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: cc3a2a1ee28ad3c1fdefad08c8ec133ad45cb408
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435054"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>使用 (SAS) 的共用存取簽章來授與 Azure 儲存體資源的有限存取權

 (SAS) 的共用存取簽章，可提供儲存體帳戶中資源的安全委派存取權。 使用 SAS 時，您可以更精確地控制用戶端存取資料的方式。 例如：
 
- 用戶端可以存取哪些資源。

- 這些資源的許可權。

- SAS 的有效時間長度。

## <a name="types-of-shared-access-signatures"></a>共用存取簽章的類型

Azure 儲存體支援三種類型的共用存取簽章：

- 使用者委派 SAS

- 服務 SAS

- 帳戶 SAS

### <a name="user-delegation-sas"></a>使用者委派 SAS

使用者委派 SAS 可透過 Azure Active Directory (Azure AD) 認證，以及為 SAS 指定的許可權來保護。 使用者委派 SAS 只適用于 Blob 儲存體。

如需有關使用者委派 SAS 的詳細資訊，請參閱 [建立使用者委派 sas (REST API) ](/rest/api/storageservices/create-user-delegation-sas)。

### <a name="service-sas"></a>服務 SAS

使用儲存體帳戶金鑰保護服務 SAS。 服務 SAS 只會將存取權委派給其中一個 Azure 儲存體服務的資源： Blob 儲存體、佇列儲存體、資料表儲存體或 Azure 檔案儲存體。

如需有關服務 SAS 的詳細資訊，請參閱 [建立服務 sas (REST API) ](/rest/api/storageservices/create-service-sas)。

### <a name="account-sas"></a>帳戶 SAS

使用儲存體帳戶金鑰來保護帳戶 SAS。 帳戶 SAS 則將存取權限委派給一或多個儲存體服務的資源。 透過服務或使用者委派 SAS 提供的所有作業，也可透過帳戶 SAS 取得。 

您也可以將存取權委派給下列各項：

- 服務層級的作業 (例如， **取得/設定服務屬性** ，以及 **取得服務統計** 資料作業) 。 

- 服務 SAS 不允許的讀取、寫入和刪除作業。 

如需帳戶 SAS 的詳細資訊，請 [ (REST API) 建立帳戶 sas ](/rest/api/storageservices/create-account-sas)。

> [!NOTE]
> Microsoft 建議您盡可能使用 Azure AD 認證作為安全性最佳做法，而不是使用帳戶金鑰，這可能更容易遭到入侵。 當您的應用程式設計需要共用存取簽章以存取 Blob 儲存體時，請使用 Azure AD 認證來建立使用者委派 SAS （如果可能）以獲得較佳的安全性。 如需詳細資訊，請參閱 [使用 Azure Active Directory 授權存取 blob 和佇列](storage-auth-aad.md)。

共用存取簽章可以採用下列兩種形式的其中一種：

- **特定 SAS**。 當您建立臨機操作 SAS 時，會在 SAS URI 中指定開始時間、到期時間和許可權。 任何類型的 SAS 都可以是特定的 SAS。

- **具有預存存取原則的服務 SAS**。 預存存取原則會在資源容器上定義，而資源容器可以是 blob 容器、資料表、佇列或檔案共用。 儲存的存取原則可以用來管理一或多個服務共用存取簽章的條件約束。 當您將服務 SAS 與預存存取原則建立關聯時，SAS 會將 &mdash; 開始時間、到期時間和 &mdash; 為預存存取原則所定義的許可權繼承給條件約束。

> [!NOTE]
> 使用者委派 SAS 或帳戶 SAS 必須是特定的 SAS。 使用者委派 SAS 或帳戶 SAS 不支援儲存的存取原則。

## <a name="how-a-shared-access-signature-works"></a>共用存取簽章的運作方式

共用存取簽章是指向一或多個儲存體資源的帶正負號的 URI。 URI 包含權杖，其中包含一組特殊的查詢參數。 權杖指出用戶端可以如何存取資源。 其中一個查詢參數（簽章）是由 SAS 參數所建立，並以用來建立 SAS 的金鑰進行簽署。 Azure 儲存體會使用此簽章來授權存取儲存體資源。

### <a name="sas-signature-and-authorization"></a>SAS 簽章和授權

您可以使用使用者委派金鑰或使用儲存體帳戶金鑰來簽署 SAS 權杖， (共用金鑰) 。 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>使用使用者委派金鑰簽署 SAS 權杖

您可以使用使用 Azure Active Directory (Azure AD) 認證建立的 *使用者委派金鑰* 來簽署 SAS 權杖。 使用者委派 SAS 會以使用者委派金鑰簽署。

若要取得金鑰，然後建立 SAS，必須將包含該動作的 Azure 角色指派給 Azure AD 的安全性主體 `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` 。 如需詳細資訊，請參閱 [建立使用者委派 SAS (REST API) ](/rest/api/storageservices/create-user-delegation-sas)。

#### <a name="signing-a-sas-token-with-an-account-key"></a>使用帳戶金鑰簽署 SAS 權杖

服務 SAS 和帳戶 SAS 都會使用儲存體帳戶金鑰進行簽署。 若要建立以帳戶金鑰簽署的 SAS，應用程式必須具有帳戶金鑰的存取權。

當要求包含 SAS 權杖時，該要求會根據 SAS 權杖的簽署方式獲得授權。 Azure 儲存體也會使用您用來建立 SAS 權杖的存取金鑰或認證，將存取權授與擁有 SAS 的用戶端。

下表摘要說明每一種 SAS 權杖的授權方式。

| SAS 類型 | 授權類型 |
|-|-|
| 僅限 (Blob 儲存體的使用者委派 SAS)  | Azure AD |
| 服務 SAS | 共用金鑰 |
| 帳戶 SAS | 共用金鑰 |

Microsoft 建議您盡可能使用使用者委派 SAS 來取得較佳的安全性。

### <a name="sas-token"></a>SAS 權杖

SAS 權杖是您在用戶端產生的字串，例如，使用其中一個 Azure 儲存體用戶端程式庫。 Azure 儲存體不會以任何方式追蹤 SAS 權杖。 您可以在用戶端建立不限數量的 SAS 權杖。 建立 SAS 之後，您可以將其散發至需要存取儲存體帳戶中資源的用戶端應用程式。

用戶端應用程式會提供 SAS URI，以 Azure 儲存體為要求的一部分。 然後，服務會檢查 SAS 參數和簽章，以確認它是否有效。 如果服務確認簽章有效，則要求會獲得授權。 否則要求會遭到拒絕，並產生錯誤碼 403 (禁止)。

以下是服務 SAS URI 的範例，其中顯示資源 URI 與 SAS 權杖：

![服務 SAS URI 的元件](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>使用共用存取簽章的時機

使用 SAS，將儲存體帳戶中資源的安全存取權提供給任何不具有這些資源許可權的用戶端。

證明 SAS 非常有用的一個常見案例，就是使用者在您的儲存體帳戶中讀取和寫入自己的資料。 在儲存體帳戶儲存使用者資料的案例中，典型的設計模式有兩種：

1. 用戶端通過前端 Proxy 服務 (執行驗證) 來上傳與下載資料。 此前端 proxy 服務允許驗證商務規則。 但對於大量資料或大量交易，建立可調整以符合需求的服務可能會很昂貴或困難。

   ![案例圖表︰前端 Proxy 服務](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. 輕量型服務可視需要驗證用戶端，然後產生 SAS。 一旦用戶端應用程式收到 SAS，它就可以直接存取儲存體帳戶資源。 存取權限是由 SAS 和 SAS 允許的間隔所定義。 SAS 可減輕透過前端 Proxy 服務路由所有資料的需求。

   ![案例圖表︰SAS 提供者服務](./media/storage-sas-overview/sas-storage-provider-service.png)

許多實際服務可能會混合運用這兩種方法。 例如，某些資料可能會透過前端 proxy 處理和驗證。 其他資料則是使用 SAS 直接儲存及/或讀取。

此外，在某些情況下，需要 SAS 來授權複製作業中的來源物件存取權：

- 當您將 blob 複製到另一個位於不同儲存體帳戶的 blob 時。 
  
  您也可以選擇性地使用 SAS 來授權存取目的地 Blob。

- 當您將檔案複製到另一個位於不同儲存體帳戶的檔案時。 

  您也可以選擇性地使用 SAS 來授權存取目的檔案。

- 當您將 blob 複製到檔案，或將檔案複製到 blob 時。 

  即使來源和目的地物件位於相同的儲存體帳戶中，您也必須使用 SAS。

## <a name="best-practices-when-using-sas"></a>使用 SAS 時的最佳做法

當您在應用程式中使用共用存取簽章時，您必須留意兩個潛在風險：

- 如果 SAS 洩漏出去，則取得該 SAS 的任何人都可以使用它，這有可能會洩露您的儲存體帳戶。

- 如果提供給用戶端應用程式的 SAS 已過期，且此應用程式無法從您的服務擷取新的 SAS，那麼該應用程式的功能可能會受到影響。

下列關於使用共用存取簽章的建議，將可協助您平衡這些風險：

- **永遠使用 HTTPS** 來建立或散佈 SAS。 如果透過 HTTP 傳遞 SAS 並遭到攔截，攻擊者就可以讀取 SAS，以進行攔截式攻擊。 然後，他們可以像預期的使用者一樣使用 SAS。 這可能會危害機密資料，或讓惡意使用者損毀資料。

- **可能的話，請使用使用者委派 SAS。** 使用者委派 SAS 可為服務 SAS 或帳戶 SAS 提供絕佳的安全性。 使用 Azure AD 認證保護使用者委派 SAS，因此您不需要將帳戶金鑰與您的程式碼一起儲存。

- **具有適用于 SAS 的撤銷計畫。** 確定您已準備好在 SAS 遭到入侵時回應。

- **定義服務 SAS 的預存存取原則。** 預存存取原則可讓您選擇撤銷服務 SAS 的許可權，而不需要重新產生儲存體帳戶金鑰。 將到期日設在未來 (或無限) 的日期，並確定定期更新到期日以將到期日再往未來的日期移動。

- **在臨機操作 SAS 服務 SAS 或帳戶 SAS 上使用近乎長期的到期時間。** 如此一來，即使 SAS 遭到入侵，亦僅會造成短期影響。 如果您無法參考預存存取原則，此做法格外重要。 短期到期時間亦可協助限制可寫入 Blob 的資料量，方法是限制可對其上傳的可用時間。

- **讓用戶端視需要自動更新 SAS。** 用戶端應在到期日之前就更新 SAS，以便如果提供 SAS 的服務無法使用的話，還有時間可以進行重試。 在某些情況下，這可能是不必要的。 例如，您可能想要將 SAS 用於少量的立即、短期作業。 這些作業預期會在到期期間內完成。 因此，您不需要更新 SAS。 但是，如果您的用戶端是透過 SAS 定期提出要求，則到期的可能性就會開始。 

- **請小心使用 SAS 開始時間。** 如果您將 SAS 的開始時間設定為目前的時間，前幾分鐘可能會間歇性發生失敗。 這是因為不同的電腦目前的時間稍微不同 (稱為「時鐘誤差」) 。 一般而言，請將開始時間設為至少 15 分鐘之前的時間。 或是不進行任何設定，這會針對所有案例立即生效。 同樣的道理通常亦適用於過期時間，請記住，您可針對任何要求保留前後多達 15 分鐘的時鐘誤差。 如果用戶端使用2012-02-12 之前的 REST 版本，則不參考預存存取原則之 SAS 的最大持續時間為1小時。 指定長期超過1小時的任何原則將會失敗。

- **請小心使用 SAS 日期時間格式。** 針對某些公用程式 (例如 AzCopy) ，您需要將 datetime 格式設為 ' +% Y-% m-% dT% H:%M：% SZ '。 此格式特別包含秒數。 

- **請具體指出要存取的資源。** 安全性最佳做法是提供使用者最低需求權限。 如果使用者只需要單一實體的讀取存取權，則授與他們該單一實體的讀取存取權，而非授與他們所有實體的讀取/寫入/刪除存取權。 這有助於減輕洩露 SAS 遭受的損害，因為當 SAS 落入攻擊者手中時，即無法發揮固有功能。

- **瞭解您的帳戶將會以任何使用量計費，包括透過 SAS。** 如果您提供 blob 的寫入權限，使用者可能會選擇上傳 200 GB 的 blob。 若您也同時提供使用者讀取存取權，則他們可能會選擇下載 10 次，而您便會產生 2TB 的出口成本。 再次強調，提供有限的權限有助於減少惡意使用者採取的潛在動作。 使用短期 SAS 以降低此威脅 (但請注意結束時間的時鐘誤差)。

- **驗證使用 SAS 撰寫的資料。**  當用戶端應用程式將資料寫入您的儲存體帳戶時，請留意該資料可能會造成問題。 如果您打算驗證資料，請在寫入資料之後以及應用程式使用之前執行驗證。 此做法也可防止正確取得 SAS 的使用者或是利用洩漏 SAS 的使用者，損毀資料或將惡意資料寫入您的帳戶。

- **知道何時不使用 SAS。** 有時候，針對您的儲存體帳戶與特定作業相關聯的風險，會超過使用 SAS 的優點。 針對此類作業，請建立一個中介層服務，在執行商務規則驗證、驗證及稽核之後才寫入您的儲存體帳戶。 另外，有時候以其他方式管理存取權可能比較簡單。 例如，如果您想要讓容器中的所有 Blob 都可供公開讀取，則您可以將此容器設定為 [公用]，而不是將 SAS 提供給每個用戶端進行存取。

- **使用 Azure 監視器和 Azure 儲存體記錄來監視您的應用程式。** 發生授權失敗的原因可能是您的 SAS 提供者服務中斷。 這也可能是因為意外移除儲存的存取原則。 您可以使用 Azure 監視器和儲存體分析記錄，來觀察這些授權失敗類型的任何尖峰。 如需詳細資訊，請參閱 [Azure 監視器中的 Azure 儲存體計量](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) 和 [Azure 儲存體分析記錄](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="get-started-with-sas"></a>開始使用 SAS

若要開始使用共用存取簽章，請參閱下列適用于每種 SAS 類型的文章。

### <a name="user-delegation-sas"></a>使用者委派 SAS

- [使用 PowerShell 建立容器或 blob 的使用者委派 SAS](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [使用 Azure CLI 建立容器或 blob 的使用者委派 SAS](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [使用 .NET 建立容器或 blob 的使用者委派 SAS](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>服務 SAS

- [使用 .NET 建立容器或 blob 的服務 SAS](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>帳戶 SAS

- [使用 .NET 建立帳戶 SAS](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>後續步驟

- [使用共用存取簽章來委派存取 (REST API) ](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [建立使用者委派 SAS (REST API) ](/rest/api/storageservices/create-user-delegation-sas)
- [建立服務 SAS (REST API) ](/rest/api/storageservices/create-service-sas)
- [建立帳戶 SAS (REST API) ](/rest/api/storageservices/create-account-sas)