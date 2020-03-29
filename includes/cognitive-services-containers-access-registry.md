---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704245"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>使用 Docker CLI 對專用容器註冊表進行身份驗證

您可以通過多種方式對認知服務容器的專用容器註冊表進行身份驗證，但命令列中的建議方法是使用[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)。

使用[`docker login`命令](https://docs.docker.com/engine/reference/commandline/login/)（如以下示例所示）登錄到`containerpreview.azurecr.io`認知服務容器的專用容器註冊表。 將*\<使用者名\>* 替換為*\<使用者名和密碼\>，將*從 Azure 認知服務團隊收到的憑據中提供的密碼替換。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果已保護文字檔中的憑據，則可以使用 命令`cat`將該文字檔的內容與`docker login`命令串聯，如以下示例所示。 將*\<密碼檔\>* 替換為包含密碼和*\<使用者名\>* 的文字檔的路徑和名稱以及憑據中提供的使用者名。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
