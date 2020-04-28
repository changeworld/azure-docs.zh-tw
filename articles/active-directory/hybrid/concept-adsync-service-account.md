---
title: Azure AD Connect： ADSync 服務帳戶 |Microsoft Docs
description: 本主題描述 ADSync 服務帳戶，並提供有關此帳戶的最佳作法。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67478715"
---
# <a name="adsync-service-account"></a>ADSync 服務帳戶
Azure AD Connect 會安裝內部部署服務，以協調 Active Directory 和 Azure Active Directory 之間的同步處理。  Microsoft Azure AD 同步同步處理服務（ADSync）會在內部部署環境中的伺服器上執行。  服務的認證預設會在快速安裝中設定，但您可以根據組織的安全性需求進行自訂。  這些認證不會用來連接到您的內部部署樹系或 Azure Active Directory。

在安裝 Azure AD Connect 之前，選擇 ADSync 服務帳戶是一項重要的規劃決策。  任何嘗試在安裝後變更認證的動作，都會導致服務無法啟動、失去同步處理資料庫的存取權，以及無法使用您的連線目錄（Azure 和 AD DS）進行驗證。  在還原原始認證之前，不會進行同步處理。

## <a name="the-default-adsync-service-account"></a>預設的 ADSync 服務帳戶

在成員伺服器上執行時，AdSync 服務會在虛擬服務帳戶（VSA）的內容中執行。  由於產品的限制，在網域控制站上安裝時，會建立自訂服務帳戶。  如果快速設定服務帳戶不符合您的組織安全性需求，請選擇 [自訂] 選項來部署 Azure AD Connect。  然後選擇符合您組織需求的 [服務帳戶] 選項。

>[!NOTE]
>安裝在網域控制站上的預設服務帳戶格式為 Domain \ AAD_InstallationIdentifier。  此帳戶的密碼是隨機產生的，並針對修復和密碼輪替帶來重大的挑戰。  Microsoft 建議在網域控制站上的初始安裝期間自訂服務帳戶，以使用獨立或群組受管理的服務帳戶（sMSA/gMSA）

|Azure AD Connect 位置|已建立服務帳戶|
|-----|-----|
|成員伺服器|NT SERVICE\ADSync|
|網域控制站|網域 \ AAD_74dc30c01e80 （請參閱附注）|

## <a name="custom-adsync-service-accounts"></a>自訂 ADSync 服務帳戶
Microsoft 建議在虛擬服務帳戶或獨立或群組受管理的服務帳戶的內容中執行 ADSync 服務。  您的網域系統管理員也可以選擇建立已布建的服務帳戶，以符合您特定的組織安全性需求。   若要自訂安裝期間所使用的服務帳戶，請選擇下方 [快速設定] 頁面上的 [自訂] 選項。   有下列選項可供使用：

- 預設帳戶-Azure AD Connect 將會依照上述說明布建服務帳戶
- 受管理的服務帳戶-使用系統管理員布建的獨立或群組 MSA
- 網域帳戶-使用系統管理員所布建的網域服務帳戶

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>診斷 ADSync 服務帳戶變更
在安裝後變更 ADSync 服務的認證會導致服務無法啟動、失去對同步處理資料庫的存取權，以及無法使用您的連線目錄（Azure 和 AD DS）進行驗證。  將資料庫存取權授與新的 ADSync 服務帳戶不足，無法從此問題中復原。 在還原原始認證之前，不會進行同步處理。

當 ADSync 服務無法啟動時，將會發出錯誤層級訊息給事件記錄檔。  訊息的內容會根據內建的資料庫（localdb）或完整 SQL 是否在使用中而有所不同。  以下是可能存在的事件記錄檔專案的範例。

### <a name="example-1"></a>範例 1

找不到 AdSync 服務加密金鑰，因此已重新建立。  在更正此問題之前，不會進行同步處理。

疑難排解此問題：如果 AdSync 服務登入認證已變更，Microsoft Azure AD 同步加密金鑰將會變成無法存取。  如果認證已變更，請使用服務應用程式將登入帳戶變更回原先設定的值（例如 NT SERVICE\AdSync），然後重新開機服務。  這會立即還原 AdSync 服務的正確作業。

如需詳細資訊，請參閱下列[文章](https://go.microsoft.com/fwlink/?linkid=2086764)。

### <a name="example-2"></a>範例 2

無法啟動服務，因為無法建立與本機資料庫（localdb）的連接。

疑難排解此問題：如果 AdSync 服務登入認證已變更，Microsoft Azure AD 同步處理服務將會失去存取本機資料庫提供者的許可權。  如果認證已變更，請使用服務應用程式將登入帳戶變更回原先設定的值（例如 NT SERVICE\AdSync），然後重新開機服務。  這會立即還原 AdSync 服務的正確作業。

如需詳細資訊，請參閱下列[文章](https://go.microsoft.com/fwlink/?linkid=2086764)。

其他詳細資料：提供者傳回下列錯誤資訊。。
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。