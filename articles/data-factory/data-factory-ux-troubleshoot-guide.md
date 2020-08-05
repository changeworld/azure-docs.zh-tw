---
title: 針對 Azure Data Factory UX 進行疑難排解
description: 瞭解如何針對 Azure Data Factory UX 問題進行疑難排解。
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567902"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>針對 Azure Data Factory UX 問題進行疑難排解
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory UX 的常見疑難排解方法。

## <a name="adf-ux-not-loading"></a>ADF UX 未載入

> [!NOTE]
> Azure Data Factory UX 正式支援 Microsoft Edge 和 Google Chrome。 使用其他網頁瀏覽器可能會導致未預期或未記載的行為。

### <a name="third-party-cookies-blocked"></a>已封鎖協力廠商 cookie

ADF UX 會使用瀏覽器 cookie 來保存使用者會話，並啟用互動式開發和監視體驗。 您的瀏覽器可能會封鎖協力廠商 cookie，因為您使用的是 incognito 會話，或已啟用 ad 封鎖程式。 封鎖協力廠商 cookie 可能會在載入入口網站時發生問題，例如重新導向至空白頁面， https://adf.azure.com/accesstoken.html 或收到警告訊息，指出已封鎖協力廠商 cookie。 若要解決此問題，請使用下列步驟，在您的瀏覽器上啟用協力廠商 cookie 選項：

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>允許所有 cookie

1. 前往瀏覽器中的**chrome://settings/cookies** 。
1. 選取 [**允許所有 cookie** ] 選項 ![ Chrome-允許-全部-cookie](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. 重新整理 ADF UX，然後再試一次。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>僅允許 ADF UX 使用 cookie
如果您不想要允許所有 cookie，可以選擇只允許 ADF UX：
1. 請造訪**chrome://settings/cookies**。
1. 按一下 [**新增**] 底下**的 [可一律使用 cookie 的網站**] 選項 ![ 將 ADF UX 新增至允許的網站](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. 新增**adf.azure.com**網站，勾選 [**所有 cookie** ] 選項，然後 [儲存]。 ![允許來自 ADF UX 網站的所有 cookie](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. 重新整理 ADF UX，然後再試一次。


### <a name="microsoft-edge"></a>Microsoft Edge

1. 前往瀏覽器中的**edge://settings/content/cookies** 。
1. 確定 [**允許網站儲存和讀取 cookie 資料**] 已啟用，而且 [**封鎖協力廠商 cookie** ] 選項已停用 [ ![ 允許 Edge 中的所有 cookie]](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. 重新整理 ADF UX，然後再試一次。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>僅允許 ADF UX 使用 cookie

如果您不想要允許所有 cookie，可以選擇只允許 ADF UX：

1. 請造訪**edge://settings/content/cookies**。
1. 在 [**允許**] 區段下，按一下 [**新增**並新增**adf.azure.com**網站]。 ![將 ADF UX 新增至允許的網站](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. 重新整理 ADF UX，然後再試一次。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：

* [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory 的 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
* [Azure 影片](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
