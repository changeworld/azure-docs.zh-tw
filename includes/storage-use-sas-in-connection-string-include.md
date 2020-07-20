---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174197"
---
如果您具有授與您存取儲存體帳戶中資源的共用存取簽章 (SAS) URL，您可以在連接字串中使用 SAS。 因為 SAS 包含驗證要求所需的資訊，所以含有 SAS 的連接字串會提供通訊協定、服務端點，以及存取資源所需的認證。

若要建立包含共用存取簽章的連接字串，請以下列格式指定字串：

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

雖然連接字串必須包含至少一個服務端點，但是每個服務端點都是選用的。

> [!NOTE]
> 建議最好搭配使用 HTTPS 與 SAS。
>
> 如果您在組態檔的連接字串中指定 SAS，則可能需要編碼 URL 中的特殊字元。
>
>

### <a name="service-sas-example"></a>服務 SAS 範例
以下範例是包含服務 SAS for Blob 儲存體的連接字串：

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

而以下範例是具有特殊字元編碼的相同連接字串︰

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>帳戶 SAS 範例
以下範例是包含帳戶 SAS for Blob 和檔案儲存體的連接字串。 請注意，指定兩個服務的端點︰

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

而以下範例是具有 URL 編碼的相同連接字串︰

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

