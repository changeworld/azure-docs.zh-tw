---
title: 針對 Azure Data Factory 中的資料複製工具進行疑難排解
description: 瞭解如何針對 Azure Data Factory 中的資料複製工具問題進行疑難排解。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388356"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>針對 Azure Data Factory 中的資料複製工具進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中資料複製工具的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見錯誤和訊息

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>錯誤碼：無法在資料複製工具中進行驗證

- **徵兆**：在資料複製工具的第一個步驟中，您會遇到「無法驗證」的警告訊息。
- **原因**：當所有協力廠商 cookie 都已停用時，就會發生這種情況。
- **解決方案**： 
    - 使用 Internet Explorer 或 Microsoft Edge 瀏覽器。
    - 如果您使用 Chrome 瀏覽器，請遵循下列指示來新增*microsoftonline.com*和*windows.net*的 cookie 例外狀況。
        1.  開啟 Chrome 瀏覽器。
        2.  按一下右側的扳手或三行（自訂和控制 Google Chrome）。
        3.  按一下 [設定]。
        4.  搜尋**cookie** ，或移至 [高級設定] 底下的 [**隱私權**]。
        5.  選取 [**內容設定**]。    
        6.  Cookie 應設定為**允許設定本機資料（建議使用）**。
        7.  按一下 [**管理例外**狀況]。 在 [**主機名稱模式**] 下輸入下列各項，並確定 [**允許**] 是設定的行為。
            - login.microsoftonline.com
            - login.windows.net
        8.  關閉瀏覽器並重新啟動。
    - 如果您使用 Firefox 瀏覽器，請遵循下列指示來新增 Cookie 例外狀況。
        1. 從 Firefox 功能表中，移至 [**工具**] [  >  **選項**]。
        2. 在 [**隱私權**歷程  >  **記錄**] 下，您可能會看到目前的設定為 [**使用自訂設定來進行歷程記錄**]。
        3. 在 [**接受協力廠商 cookie**] 中，您目前的設定可能**永遠不**會，然後您應該按一下右側的 [**例外**狀況] 來新增下列網站。
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  關閉瀏覽器並重新啟動。 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>錯誤碼：無法開啟登入頁面並輸入密碼

- **徵兆**：資料複製工具會將您重新導向至登入頁面，但登入頁面未成功顯示。
- **原因**：如果您將網路環境從 office 網路變更為「家用網路」，則可能會發生此問題。 瀏覽器中有一些快取。 
- **解決方案**： 
    1.  關閉瀏覽器，然後再試一次。 如果問題仍然存在，請移至下一個步驟。   
    2.  如果您使用的是 Internet Explorer 瀏覽器，請嘗試以私用模式開啟它（按下 [Ctrl] + "Shift" + "P"）。 如果您使用 Chrome 瀏覽器，請嘗試在 incognito 模式中開啟它（按下 [Ctrl] + "shift" + "N"）。 如果問題仍然存在，請移至下一個步驟。 
    3.  嘗試使用另一個瀏覽器。 


## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：
*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory 的 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [ADF 對應資料流程效能指南](concepts-data-flow-performance.md)
