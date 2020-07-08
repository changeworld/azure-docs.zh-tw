---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67673263"
---
## <a name="configure-your-application-to-access-azure-storage"></a>設定您的應用程式以存取 Azure 儲存體
有兩種方式可驗證您的應用程式對儲存體服務的存取權：

* 共用金鑰：僅為測試用途使用共用金鑰
* 共用存取簽章 (SAS)：請為生產應用程式使用 SAS

### <a name="shared-key"></a>共用金鑰
共用金鑰驗證表示您的應用程式會使用您的帳戶名稱和帳戶金鑰來存取儲存體服務。 為了快速示範如何使用此程式庫，我們將在此快速入門中使用共用金鑰驗證。

> [!WARNING] 
> **共用金鑰驗證請只限於測試目的之用！** 您的帳戶名稱和帳戶金鑰 (會提供對相關儲存體帳戶的完整讀取/寫入權限) 將會分送給下載您的應用程式的每個人。 這並「不是」  的實作方式，因為您的金鑰有可能遭到不受信任的用戶端破壞。
> 
> 

使用共用金鑰驗證時，您將會建立 [連接字串](../articles/storage/common/storage-configure-connection-string.md)。 連接字串包含：  

* **DefaultEndpointsProtocol** - 您可以選擇 HTTP 或 HTTPS。 不過，強烈建議您使用 HTTPS。
* **帳戶名稱** - 您儲存體帳戶的名稱。
* **帳戶金鑰** - 在 [Azure 入口網站](https://portal.azure.com)上，瀏覽至儲存體帳戶並按一下 [金鑰] 圖示來找出這項資訊。
* (選擇性) **EndpointSuffix** - 這用於具有不同端點尾碼的儲存體服務，例如 Azure China 或 Azure 控管的區域。

使用共用金鑰驗證的連接字串的範例如下︰

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>共用存取簽章 (SAS)
對於行動應用程式，對 Azure 儲存體服務驗證用戶端所提要求的建議方法是使用共用存取簽章 (SAS)。 SAS 可讓您授與對資源的用戶端存取權一段指定的時間，且限定於指定的權限集。
身為儲存體帳戶擁有者，您必須產生 SAS 供您行動用戶端取用。 為了產生 SAS，您可能會想要撰寫個別的服務，以產生會分送給用戶端的 SAS。 針對測試目的，您可以使用 [Microsoft Azure 儲存體總管](https://storageexplorer.com)或 [Azure 入口網站](https://portal.azure.com)來產生 SAS。 當您建立 SAS 時，您可以指定 SAS 有效的時間間隔，和 SAS 授與給用戶端的權限。

下列範例示範如何使用 Microsoft Azure 儲存體總管來產生 SAS。

1. 如果您還沒有這麼做， [請安裝 Microsoft Azure 儲存體總管](https://storageexplorer.com)
2. 連線至您的訂用帳戶。
3. 按一下 [儲存體帳戶]，並按一下左下方的 [動作] 索引標籤。 按一下 [取得共用存取簽章]，以產生您 SAS 的連接字串。
4. 以下是 SAS 連接字串的範例，會針對儲存體帳戶的 Blob 服務，授予對服務、容器和物件層級的讀取與寫入權限。
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

如您所見，使用 SAS 時，您並不會在應用程式中公開您的帳戶金鑰。 若要了解 SAS 與使用 SAS 的最佳做法，請參閱[共用存取簽章：了解 SAS 模型](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)。

