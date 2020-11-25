---
title: 針對安全性和存取控制問題進行疑難排解
description: 瞭解如何對 Azure Data Factory 中的安全性和存取控制問題進行疑難排解。
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008715"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>針對 Azure Data Factory 安全性和存取控制問題進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中安全性和存取控制的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見錯誤和訊息

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>停用公用網路存取後，無效或空白的驗證金鑰問題

#### <a name="symptoms"></a>徵狀

停用 Data Factory 的公用網路存取之後，自我裝載整合執行時間會擲回錯誤「驗證金鑰無效或為空白」。

#### <a name="cause"></a>原因

問題很可能是因為 DNS 解析問題所導致，因為停用公用連線和建立私人端點並不會協助重新連接。

#### <a name="resolution"></a>解決方法

1. 已 PsPing 至 ADF 的 FQDN，併發現緩衝區即將進入 ADF 的公用端點，即使在停用之後也是如此。

1. 變更 VM 中的主機檔案，以將私人 IP 對應到 FQDN，然後再次執行 PsPing。 緩衝區將能夠移至 ADF 的正確私人 IP。

1. 重新註冊自我裝載的整合執行時間，我們會發現它已啟動並在執行中。
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>因為 private link，所以無法在自我裝載的 Vm 上註冊 IR 驗證金鑰

#### <a name="symptoms"></a>徵狀

因為已啟用私人連結，所以無法在自我裝載的 VM 上註冊 IR 驗證金鑰。

錯誤資訊如下所示：

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>原因

此問題可能是因為 SHIR 嘗試安裝的 VM 所造成。 應啟用公用網路存取以連接到雲端。

#### <a name="resolution"></a>解決方法

 **解決方案1：** 您可以遵循下列步驟來解決此問題：

1. 流覽至下列連結： 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. 按一下 [ **試試看** ] 選項，並填寫所有必要的詳細資料。 **在本文** 中貼上下列屬性：

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. 按一下頁面結尾的 [ **執行** ] 以執行函數。 請確定您會收到回應碼200。 我們所貼上的屬性也會顯示在 JSON 定義中。

1. 然後，您可以嘗試在整合執行時間中再次新增 IR 驗證金鑰。


**解決方案2：** 您可以參閱下列文章以取得解決方案：

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

嘗試使用使用者介面啟用公用網路存取。

![啟用公用網路存取](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>後續步驟

如需疑難排解的詳細資訊，請嘗試下列資源：

*  [Data Factory 的 Private Link](data-factory-private-link.md)
*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](/answers/topics/azure-data-factory.html)
*  [Data Factory 的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)