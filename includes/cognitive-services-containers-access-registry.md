---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "67704245"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>使用 Docker CLI 來驗證私人容器登錄

您可以透過數種方式使用認知服務容器的私人容器登錄進行驗證，但從命令列建議的方法是使用[DOCKER CLI](https://docs.docker.com/engine/reference/commandline/cli/)。

如下列範例所示，使用[ `docker login`命令](https://docs.docker.com/engine/reference/commandline/login/)來登入`containerpreview.azurecr.io`認知服務容器的私人容器登錄。 將* \<username\> *取代為使用者名稱和* \<密碼\> * ，並使用您從 Azure 認知服務小組收到的認證所提供的密碼。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您已在文字檔中保護您的認證，則可以使用`cat`命令將該文字檔的內容串連至`docker login`命令，如下列範例所示。 將* \<passwordFile\> *取代為包含密碼的文字檔路徑和名稱，並* \< \> *以您的認證中提供的使用者名稱取代 username。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
