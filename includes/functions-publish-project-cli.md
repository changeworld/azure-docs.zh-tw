---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424643"
---
## <a name="deploy-the-function-project-to-azure"></a>將函式專案部署至 Azure

在 Azure 中成功建立函式應用程式之後，您就可以開始使用 [func Azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) 命令部署本機函式專案。  

在下列範例中，請將 `<APP_NAME>` 取代為您的應用程式名稱。

```console
func azure functionapp publish <APP_NAME>
```

發佈命令會顯示類似於下列輸出的結果 (為了簡單起見已將其截斷)：

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
