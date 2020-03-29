---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229317"
---
填寫並提交[認知服務視覺容器請求表單](https://aka.ms/VisionContainersPreview)以請求訪問容器。 該表格需要有關您本身、您的公司，以及您將會使用該容器之使用者情節的資訊。 提交表單後，Azure 認知服務團隊會審核它，以確保滿足訪問專用容器註冊表的條件。

> [!IMPORTANT]
> 您必須使用與表單中的 Microsoft 帳戶 （MSA） 或 Azure 活動目錄 （Azure AD） 帳戶關聯的電子郵件地址。

如果您的請求獲得批准，您將收到一封電子郵件，其中包含說明如何獲取憑據和訪問專用容器註冊表的說明。

## <a name="log-in-to-the-private-container-registry"></a>登入私人容器登錄

有幾種方法可以使用認知服務容器的專用容器註冊表進行身份驗證。 我們建議您使用[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)使用命令列方法。

使用[docker 登錄](https://docs.docker.com/engine/reference/commandline/login/)命令（如以下示例所示）登錄到`containerpreview.azurecr.io`，這是認知服務容器的專用容器註冊表。 將*\<使用者名\>* 替換為*\<使用者名和密碼\>，以及*從 Azure 認知服務團隊收到的憑據中提供的密碼。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果在文字檔中保護憑據，則可以將該文字檔的內容串聯到命令中`docker login`。 使用`cat`命令，如以下示例所示。 將*\<密碼檔\>* 替換為包含密碼的文字檔的路徑和名稱。 將*\<使用者名\>* 替換為憑據中提供的使用者名。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

