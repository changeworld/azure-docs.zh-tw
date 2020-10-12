---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704245"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>使用 Docker CLI 驗證私人容器登錄

您可以使用下列其中一種方式，在認知服務容器的私人容器登錄中進行驗證，但命令列中的建議方法是使用 [DOCKER CLI](https://docs.docker.com/engine/reference/commandline/cli/)。

使用[ `docker login` 命令](https://docs.docker.com/engine/reference/commandline/login/)（如下列範例所示）登入 `containerpreview.azurecr.io` 認知服務容器的私人容器登錄。 *\<username\>* 以使用者名稱取代，並 *\<password\>* 以您從 Azure 認知服務小組收到的認證所提供的密碼取代。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您已在文字檔中保護您的認證，您可以使用命令將該文字檔的內容串連 `cat` 至 `docker login` 命令，如下列範例所示。 取代 *\<passwordFile\>* 為包含密碼之文字檔的路徑和名稱，並 *\<username\>* 以您的認證中提供的使用者名稱取代。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
