---
title: 授予對具有共用訪問簽名 （SAS） 的資料的有限存取權限
titleSuffix: Azure Storage
description: 瞭解如何使用共用訪問簽名 （SAS） 委派對 Azure 存儲資源（包括 Blob、佇列、表和檔）的訪問。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255232"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>使用共用訪問簽名 （SAS） 授予對 Azure 存儲資源的有限存取權限

共用訪問簽名 （SAS） 提供對存儲帳戶中資源的安全委派訪問，而不會損害資料的安全性。 使用 SAS，您可以對用戶端存取資料的方式進行精細控制。 您可以控制用戶端可以訪問哪些資源、他們對這些資源擁有哪些許可權以及 SAS 的有效期以及其他參數。

## <a name="types-of-shared-access-signatures"></a>共用存取簽章的類型

Azure 存儲支援三種類型的共用訪問簽名：

- **使用者委派 SAS。** 使用者委派 SAS 使用 Azure 活動目錄 （Azure AD） 憑據以及為 SAS 指定的許可權進行保護。 使用者委派 SAS 僅適用于 Blob 存儲。

    有關使用者委派 SAS 的詳細資訊，請參閱[創建使用者委派 SAS （REST API）。](/rest/api/storageservices/create-user-delegation-sas)

- **服務 SAS。** 使用存儲帳戶金鑰保護服務 SAS。 服務 SAS 僅委託訪問其中一個 Azure 存儲服務中的資源：Blob 存儲、佇列存儲、表存儲或 Azure 檔。 

    有關服務 SAS 的詳細資訊，請參閱[創建服務 SAS （REST API）。](/rest/api/storageservices/create-service-sas)

- **帳戶 SAS。** 使用存儲帳戶金鑰保護帳戶 SAS。 帳戶 SAS 則將存取權限委派給一或多個儲存體服務的資源。 通過服務或使用者委派 SAS 提供的所有操作也可通過帳戶 SAS 獲得。 此外，使用帳戶 SAS，您可以委派對在服務等級應用的操作的存取權限，例如**獲取/設置服務屬性**和**獲取服務統計資訊**操作。 您也可以將 Blob 容器、資料表、佇列和檔案共用的讀取、寫入和刪除作業的存取權限，委派給本無權限的服務 SAS。 

    有關帳戶 SAS 的詳細資訊，[請創建帳戶 SAS （REST API）。](/rest/api/storageservices/create-account-sas)

> [!NOTE]
> Microsoft 建議您盡可能使用 Azure AD 憑據作為安全最佳做法，而不是使用帳戶金鑰，因為帳戶金鑰更易於洩露。 當應用程式設計需要共用訪問簽名才能訪問 Blob 存儲時，請盡可能使用 Azure AD 憑據創建使用者委派 SAS，以確保更高的安全性。

共用存取簽章可以接受以下兩種格式其中之一：

- **臨時 SAS：** 創建臨時 SAS 時，SAS 的開始時間、到期時間和許可權都在 SAS URI 中指定（如果省略了開始時間，則隱含）。 任何類型的 SAS 都可以是臨時 SAS。
- **具有存儲訪問策略的服務 SAS：** 存儲的訪問策略在資源容器上定義，該容器可以是 blob 容器、表、佇列或檔共用。 存儲的訪問策略可用於管理一個或多個服務共用訪問簽名的約束。 將服務 SAS 與存儲的訪問策略關聯時，SAS 將繼承為存儲&mdash;的訪問策略定義的開始時間、到期時間和許可權&mdash;的約束。

> [!NOTE]
> 使用者委派 SAS 或帳戶 SAS 必須是臨時 SAS。 使用者委派 SAS 或帳戶 SAS 不支援存儲的訪問策略。

## <a name="how-a-shared-access-signature-works"></a>共用存取簽章的運作方式

共用存取簽章是指向一或多個儲存體資源，並包括含有一組特殊的查詢參數權杖的已簽署 URI。 權杖指出用戶端可以如何存取資源。 其中一個查詢參數（簽名）由 SAS 參數構造，並與用於創建 SAS 的鍵進行簽名。 Azure 儲存體會使用此簽章來授權存取儲存體資源。

### <a name="sas-signature"></a>SAS 簽名

您可以通過兩種方式之一對 SAS 進行簽名：

- 使用 Azure 活動目錄 （Azure AD） 憑據創建*的使用者委派金鑰*。 使用者委派 SAS 使用使用者委派金鑰進行簽名。

    要獲取使用者委派金鑰並創建 SAS，必須為 Azure AD 安全主體分配一個基於角色的存取控制 （RBAC） 角色，該角色包括**Microsoft.存儲/存儲帳戶/blob 服務/生成User委派鍵**操作。 有關具有獲取使用者委派金鑰許可權的 RBAC 角色的詳細資訊，請參閱[創建使用者委派 SAS （REST API）。](/rest/api/storageservices/create-user-delegation-sas)

- 使用存儲帳戶金鑰。 服務 SAS 和帳戶 SAS 都使用存儲帳戶金鑰進行簽名。 要創建使用帳戶金鑰簽名的 SAS，應用程式必須有權存取帳戶金鑰。

### <a name="sas-token"></a>SAS 權杖

SAS 權杖是在用戶端生成的字串，例如通過使用 Azure 存儲用戶端庫之一。 Azure 存儲不會以任何方式跟蹤 SAS 權杖。 您可以在用戶端建立不限數量的 SAS 權杖。 創建 SAS 後，可以將其分發到需要訪問存儲帳戶中的資源的用戶端應用程式。

當用戶端應用程式作為請求的一部分向 Azure 存儲提供 SAS URI 時，服務將檢查 SAS 參數和簽名，以驗證它是否對授權請求有效。 如果服務確認簽章有效，則要求會獲得授權。 否則要求會遭到拒絕，並產生錯誤碼 403 (禁止)。

下面是服務 SAS URI 的示例，它顯示了資源 URI 和 SAS 權杖：

![服務 SAS URI 的元件](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>何時使用共用訪問簽名

當您要向沒有對這些資源的許可權的任何用戶端提供安全訪問存儲帳戶中的資源時，請使用 SAS。

證明 SAS 非常有用的一個常見案例，就是使用者在您的儲存體帳戶中讀取和寫入自己的資料。 在儲存體帳戶儲存使用者資料的案例中，典型的設計模式有兩種：

1. 用戶端通過前端 Proxy 服務 (執行驗證) 來上傳與下載資料。 此前端 Proxy 服務有個好處，那就是允許商務規則的驗證，但在大量資料或大量交易的情況下，建立可調整以符合需求的服務可能十分昂貴或困難。

   ![案例圖表︰前端 Proxy 服務](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. 輕量型服務可視需要驗證用戶端，然後產生 SAS。 一旦用戶端應用程式收到 SAS，他們可以直接使用 SAS 定義的許可權和 SAS 允許的時間間隔訪問存儲帳戶資源。 SAS 可減輕透過前端 Proxy 服務路由所有資料的需求。

   ![案例圖表︰SAS 提供者服務](./media/storage-sas-overview/sas-storage-provider-service.png)

許多實際服務可能會混合運用這兩種方法。 例如，某些資料可能會透過前端 Proxy 處理和驗證，其他資料則會直接使用 SAS 來儲存和/或讀取。

此外，在某些情況下，需要 SAS 來授權在複製操作中訪問源物件：

- 當您將 Blob 複製到另一個位於不同儲存體帳戶的 Blob 時，必須使用 SAS 來授權存取來源 Blob。 您也可以選擇性地使用 SAS 來授權存取目的地 Blob。
- 當您將檔案複製到另一個位於不同儲存體帳戶的檔案時，必須使用 SAS 來授權存取來源檔案。 您也可以選擇性地使用 SAS 來授權存取目的檔案。
- 當您將 Blob 複製到檔案，或將檔案複製到 Blob 時，必須使用 SAS 來授權存取來源物件，即使來源和目的地物件位於相同的儲存體帳戶內也一樣。

## <a name="best-practices-when-using-sas"></a>使用 SAS 時的最佳做法

當您在應用程式中使用共用存取簽章時，您必須留意兩個潛在風險：

- 如果 SAS 洩漏出去，則取得該 SAS 的任何人都可以使用它，這有可能會洩露您的儲存體帳戶。
- 如果提供給用戶端應用程式的 SAS 已過期，且此應用程式無法從您的服務擷取新的 SAS，那麼該應用程式的功能可能會受到影響。

下列關於使用共用存取簽章的建議，將可協助您平衡這些風險：

- **永遠使用 HTTPS** 來建立或散佈 SAS。 若透過 HTTP 來傳遞 SAS 並遭到攔截，執行攔截式攻擊的攻擊者即可讀取並使用 SAS (就如同預期使用者執行般)，這有可能會洩露敏感資料或允許惡意使用者損毀資料。
- **盡可能使用使用者委派 SAS。** 使用者委派 SAS 為服務 SAS 或帳戶 SAS 提供卓越的安全性。 使用者委派 SAS 使用 Azure AD 憑據進行保護，因此無需將帳戶金鑰與代碼一起存儲。
- **制定 SAS 的吊銷計畫。** 確保準備好在 SAS 受損時做出回應。
- **為服務 SAS 定義存儲的訪問策略。** 存儲的訪問策略允許您選擇撤銷服務 SAS 的許可權，而無需重新生成存儲帳戶金鑰。 將到期日設在未來 (或無限) 的日期，並確定定期更新到期日以將到期日再往未來的日期移動。
- **在臨時 SAS 服務 SAS 或帳戶 SAS 上使用短期過期時間。** 如此一來，即使 SAS 遭到入侵，亦僅會造成短期影響。 如果您無法參考預存存取原則，此做法格外重要。 短期到期時間亦可協助限制可寫入 Blob 的資料量，方法是限制可對其上傳的可用時間。
- **讓用戶端視需要自動更新 SAS。** 用戶端應在到期日之前就更新 SAS，以便如果提供 SAS 的服務無法使用的話，還有時間可以進行重試。 如果您打算將 SAS 用於少量的即時短期操作 (預計可在到期期限內完成的操作)，則此建議可能沒有必要，因為沒有更新 SAS 的打算。 不過，如果您有定期透過 SAS 做出要求的用戶端，則到期的可能性便有可能發生。 主要考量是要平衡下列兩個需求：短期的 SAS (如先前所述)，與確保用戶端提早要求更新以避免成功更新之前因 SAS 過期而中斷。
- **請小心使用 SAS 開始時間。** 如果您將 SAS 的開始時間設為 [現在]****，則由於時鐘誤差 (根據不同機器會有不同的目前時間)，前幾分鐘可能偶爾會被視為失敗。 一般而言，請將開始時間設為至少 15 分鐘之前的時間。 或是不進行任何設定，這會針對所有案例立即生效。 同樣的道理通常亦適用於過期時間，請記住，您可針對任何要求保留前後多達 15 分鐘的時鐘誤差。 若是用戶端使用 2012-02-12 之前的 REST 版本，則不參考預存存取原則之 SAS 的最大持續期限是 1 個小時，且任何指定比 1 個小時還要長的原則都會失敗。
- **小心使用 SAS 日期時間格式。** 如果為 SAS 設置開始時間和/或到期，則某些實用程式（例如，對於命令列實用程式 AzCopy）需要日期時間格式為"+%Y-%m-%dT%H：%M：%SZ"，具體包括秒，以便使用 SAS 權杖工作。  
- **請具體指出要存取的資源。** 安全性最佳做法是提供使用者最低需求權限。 如果使用者只需要單一實體的讀取存取權，則授與他們該單一實體的讀取存取權，而非授與他們所有實體的讀取/寫入/刪除存取權。 這有助於減輕洩露 SAS 遭受的損害，因為當 SAS 落入攻擊者手中時，即無法發揮固有功能。
- **瞭解您的帳戶將收取任何用途，包括通過 SAS。** 如果提供對 Blob 的寫入存取權限，則使用者可以選擇上載 200 GB Blob。 若您也同時提供使用者讀取存取權，則他們可能會選擇下載 10 次，而您便會產生 2TB 的出口成本。 再次強調，提供有限的權限有助於減少惡意使用者採取的潛在動作。 使用短期 SAS 以降低此威脅 (但請注意結束時間的時鐘誤差)。
- **驗證使用 SAS 寫入的資料。**  當用戶端應用程式將資料寫入您的儲存體帳戶時，請留意該資料可能會造成問題。 如果您的應用程式要求在開始使用資料之前先驗證或授權資料，則您應在寫入資料之後但應用程式尚未開始使用資料之前執行此驗證。 此做法也可防止正確取得 SAS 的使用者或是利用洩漏 SAS 的使用者，損毀資料或將惡意資料寫入您的帳戶。
- **知道何時不使用 SAS。** 有時，與針對存儲帳戶的特定操作相關的風險大於使用 SAS 的好處。 針對此類作業，請建立一個中介層服務，在執行商務規則驗證、驗證及稽核之後才寫入您的儲存體帳戶。 另外，有時候以其他方式管理存取權可能比較簡單。 例如，如果您想要讓容器中的所有 Blob 都可供公開讀取，則您可以將此容器設定為 [公用]，而不是將 SAS 提供給每個用戶端進行存取。
- **使用 Azure 監視器和 Azure 存儲日誌監視應用程式。** 可以使用 Azure 監視器和存儲分析日誌記錄來觀察由於 SAS 提供程式服務中斷或無意中刪除存儲的訪問策略而導致的授權故障的任何峰值。 有關詳細資訊，請參閱 Azure 監視器和[Azure 存儲分析日誌記錄](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)[中的 Azure 存儲指標](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="get-started-with-sas"></a>開始使用 SAS

要開始使用共用訪問簽名，請參閱以下有關每種 SAS 類型的文章。

### <a name="user-delegation-sas"></a>使用者委派 SAS

- [使用 PowerShell 為容器或 Blob 創建使用者委派 SAS](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [使用 Azure CLI 為容器或 Blob 創建使用者委派 SAS](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [為具有 .NET 的容器或 blob 創建使用者委派 SAS](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>服務 SAS

- [為具有 .NET 的容器或 blob 創建服務 SAS](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>帳戶 SAS

- [使用 .NET 創建帳戶 SAS](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>後續步驟

- [使用共用訪問簽名 （REST API） 委派存取權限](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [創建使用者委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
- [創建服務 SAS （REST API）](/rest/api/storageservices/create-service-sas)
- [創建帳戶 SAS （REST API）](/rest/api/storageservices/create-account-sas)
