---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593321"
---
填寫並提交[認知服務容器要求表單](https://aka.ms/cognitivegate)，以要求容器的存取權。 該表格需要有關您本身、您的公司，以及您將會使用該容器之使用者情節的資訊。 提交表單之後，Azure 認知服務小組會進行審查，以確保您符合存取私人容器登錄的準則。

> [!IMPORTANT]
> 您必須使用與 Microsoft 帳戶（MSA）或表單中的 Azure Active Directory （Azure AD）帳戶相關聯的電子郵件地址。 如需驗收準則的詳細資訊，請參閱[認知服務控制流程](../articles/cognitive-services/cognitive-services-gating-process.md)。

如果您的要求已通過核准，您會收到一封電子郵件，其中包含說明如何取得認證和存取私人容器登錄的指示。

## <a name="log-in-to-the-private-container-registry"></a>登入私人容器登錄

有數種方式可向認知服務容器的私人容器登錄進行驗證。 我們建議您使用[DOCKER CLI](https://docs.docker.com/engine/reference/commandline/cli/)來使用命令列方法。

使用[docker login](https://docs.docker.com/engine/reference/commandline/login/)命令（如下列範例所示）來登入 `containerpreview.azurecr.io` ，這是認知服務容器的私人容器登錄。 將* \< username \> *取代為使用者名稱和* \< 密碼 \> * ，並使用您從 Azure 認知服務小組收到的認證所提供的密碼。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您在文字檔中保護您的認證，則可以將該文字檔的內容串連至 `docker login` 命令。 使用 `cat` 命令，如下列範例所示。 將* \< passwordFile \> *取代為包含密碼之文字檔的路徑和名稱。 將* \< username \> *取代為您的認證中提供的使用者名稱。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

