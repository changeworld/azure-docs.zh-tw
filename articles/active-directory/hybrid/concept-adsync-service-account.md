---
title: Azure AD 連接：ADSync 服務帳戶 |微軟文檔
description: 本主題介紹 ADSync 服務帳戶，並提供有關該帳戶的最佳做法。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478715"
---
# <a name="adsync-service-account"></a>ADSync 服務帳戶
Azure AD Connect 安裝本機服務，該服務協調活動目錄和 Azure 活動目錄之間的同步。  Microsoft Azure AD 同步同步服務 （ADSync） 在本地環境中的伺服器上運行。  預設情況下，服務的憑據在 Express 安裝中設置，但可以自訂以滿足組織安全要求。  這些憑據不用於連接到本地林或 Azure 活動目錄。

選擇 ADSync 服務帳戶是在安裝 Azure AD Connect 之前做出的重要規劃決策。  安裝後更改憑據的任何嘗試都將導致服務無法啟動、無法訪問同步資料庫以及無法對連接的目錄（Azure 和 AD DS）進行身份驗證。  在還原原始憑據之前，不會發生同步。

## <a name="the-default-adsync-service-account"></a>預設 ADSync 服務帳戶

在成員伺服器上運行時，AdSync 服務在虛擬服務帳戶 （VSA） 的上下文中運行。  由於產品限制，在網域控制站上安裝時將創建自訂服務帳戶。  如果"快速設置"服務帳戶不符合組織安全要求，請通過選擇"自訂"選項部署 Azure AD 連接。  然後選擇滿足組織要求的服務帳戶選項。

>[!NOTE]
>在網域控制站上安裝時，預設服務帳戶屬於"域\AAD_InstallationIdentifier"表單。  此帳戶的密碼是隨機生成的，對恢復和密碼輪換提出了重大挑戰。  Microsoft 建議在網域控制站上的初始安裝期間自訂服務帳戶，以使用獨立或組託管服務帳戶 （sMSA / gMSA）

|Azure AD 連接位置|已創建服務帳戶|
|-----|-----|
|成員伺服器|NT 服務_ADSync|
|網域控制站|域\AAD_74dc30c01e80（參見注釋）|

## <a name="custom-adsync-service-accounts"></a>自訂 ADSync 服務帳戶
Microsoft 建議在虛擬服務帳戶或獨立或組託管服務帳戶的上下文中運行 ADSync 服務。  您的域管理員還可以選擇創建為滿足您的特定組織安全要求而預配的服務帳戶。   要自訂安裝過程中使用的服務帳戶，請在下面的"快速設置"頁上選擇"自訂"選項。   有下列選項可供使用：

- 預設帳戶 = Azure AD 連接將預配服務帳戶，如上所述
- 託管服務帳戶 – 使用管理員預配的獨立或組 MSA
- 域帳戶 – 使用管理員預配的域服務帳戶

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>診斷 ADSync 服務帳戶更改
安裝後更改 ADSync 服務的憑據將導致服務無法啟動、無法訪問同步資料庫以及無法對連接的目錄（Azure 和 AD DS）進行身份驗證。  授予對新的 ADSync 服務帳戶的資料庫存取權限不足以從此問題中恢復。 在還原原始憑據之前，不會發生同步。

ADSync 服務將在無法啟動時向事件日誌發出錯誤級別消息。  消息的內容將因內置資料庫（localdb）還是完全 SQL 正在使用而異。  以下是可能存在的事件日誌條目的示例。

### <a name="example-1"></a>範例 1

找不到 AdSync 服務加密金鑰，並且已重新創建。  在更正此問題之前，不會發生同步。

如果更改 AdSync 服務登錄憑據，則 Microsoft Azure AD 同步加密金鑰將無法訪問。  如果憑據已更改，請使用服務應用程式將登錄帳戶更改回其最初配置的值（例如。 NT 服務_AdSync）並重新啟動服務。  這將立即恢復 AdSync 服務的正確操作。

有關詳細資訊，請參閱以下[文章](https://go.microsoft.com/fwlink/?linkid=2086764)。

### <a name="example-2"></a>範例 2

無法啟動該服務，因為無法建立與本機資料庫 （localdb） 的連接。

解決此問題 Microsoft Azure AD 同步服務將失去訪問本機資料庫提供程式的許可權（如果 AdSync 服務登錄憑據已更改）。  如果憑據已更改，請使用服務應用程式將登錄帳戶更改回其最初配置的值（例如。 NT 服務_AdSync）並重新啟動服務。  這將立即恢復 AdSync 服務的正確操作。

有關詳細資訊，請參閱以下[文章](https://go.microsoft.com/fwlink/?linkid=2086764)。

其他詳細資訊 提供程式返回以下錯誤資訊：
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。