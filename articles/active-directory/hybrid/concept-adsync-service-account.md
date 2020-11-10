---
title: Azure AD Connect： ADSync 服務帳戶 |Microsoft Docs
description: 本主題說明 ADSync 服務帳戶，並提供有關此帳戶的最佳做法。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410890"
---
# <a name="adsync-service-account"></a>ADSync 服務帳戶
Azure AD Connect 會安裝內部部署服務，以協調 Active Directory 和 Azure Active Directory 之間的同步處理。  Microsoft Azure AD 同步處理服務 (ADSync) 會在內部部署環境中的伺服器上執行。  服務的認證預設會在快速安裝中設定，但可自訂以符合您的組織安全性需求。  這些認證不會用來連接到您的內部部署樹系或 Azure Active Directory。

選擇 ADSync 服務帳戶是在安裝 Azure AD Connect 之前必須進行的重要規劃決策。  在安裝後嘗試變更認證的任何嘗試都會導致服務無法啟動、無法存取同步處理資料庫，也無法使用您的連線目錄進行驗證， (Azure 和 AD DS) 。  在還原原始認證之前，不會進行任何同步處理。

## <a name="the-default-adsync-service-account"></a>預設 ADSync 服務帳戶

在成員伺服器上執行時，AdSync 服務會在 (VSA) 的虛擬服務帳戶內容中執行。  由於產品的限制，在網域控制站上安裝時，會建立自訂服務帳戶。  如果快速設定服務帳戶不符合您的組織安全性需求，請選擇 [自訂] 選項來部署 Azure AD Connect。  然後選擇符合組織需求的 [服務帳戶] 選項。

>[!NOTE]
>安裝在網域控制站上的預設服務帳戶格式為 Domain \ AAD_InstallationIdentifier。  此帳戶的密碼會隨機產生，並針對復原和密碼輪替帶來重大挑戰。  Microsoft 建議您在網域控制站的初始安裝期間自訂服務帳戶，以使用獨立或群組受管理的服務帳戶 (sMSA/gMSA) 

|Azure AD Connect 位置|已建立服務帳戶|
|-----|-----|
|成員伺服器|NT SERVICE\ADSync|
|網域控制站|Domain \ AAD_74dc30c01e80 (請參閱附注) |

## <a name="custom-adsync-service-accounts"></a>自訂 ADSync 服務帳戶
Microsoft 建議在虛擬服務帳戶或獨立或群組受管理的服務帳戶的內容中執行 ADSync 服務。  您的網域系統管理員也可以選擇建立已布建的服務帳戶，以符合您特定的組織安全性需求。   若要自訂安裝期間所使用的服務帳戶，請選擇下方 [快速設定] 頁面上的 [自訂] 選項。   可用選項如下：

- 預設帳戶– Azure AD Connect 將會依照上述方式布建服務帳戶
- 受管理的服務帳戶–使用由系統管理員布建的獨立或群組 MSA
- 網域帳戶–使用由系統管理員布建的網域服務帳戶

![[Azure AD Connect 快速設定] 頁面的螢幕擷取畫面，其中包含 [自訂] 或 [使用快速設定] 選項按鈕。](media/concept-adsync-service-account/adsync1.png)

![Azure AD Connect [安裝必要元件] 頁面的螢幕擷取畫面，其中包含已選取的現有受管理的服務帳戶選項。](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>診斷 ADSync 服務帳戶變更
在安裝之後變更 ADSync 服務的認證將會導致服務無法啟動、無法存取同步處理資料庫，也無法使用您的連線目錄進行驗證， (Azure 和 AD DS) 。  將資料庫存取權授與新的 ADSync 服務帳戶，不足以從這個問題復原。 在還原原始認證之前，不會進行任何同步處理。

ADSync 服務會在無法啟動時，將錯誤層級訊息發行至事件記錄檔。  訊息的內容會隨著內建資料庫 (localdb) 或完整 SQL 正在使用中而有所不同。  以下是可能存在的事件記錄檔專案的範例。

### <a name="example-1"></a>範例 1

找不到 AdSync 服務加密金鑰，且已重新建立。  在修正此問題之前，將不會進行同步處理。

針對此問題進行疑難排解：如果已變更 AdSync 服務登入認證，Microsoft Azure AD 同步加密金鑰將變成無法存取。  如果認證已變更，請使用 [服務] 應用程式將登入帳戶變更回原先設定的值 (例如 NT SERVICE\AdSync) 並重新啟動服務。  這會立即還原正確的 AdSync 服務操作。

如需詳細資訊，請參閱下列 [文章](./whatis-hybrid-identity.md) 。

### <a name="example-2"></a>範例 2

無法啟動服務，因為無法建立本機資料庫 (localdb) 的連接。

針對此問題進行疑難排解：如果已變更 AdSync 服務登入認證，Microsoft Azure AD 同步服務將會失去存取本機資料庫提供者的許可權。  如果認證已變更，請使用服務應用程式將登入帳戶變更回原先設定的值 (例如 NT SERVICE\AdSync) 並重新啟動服務。  這會立即還原正確的 AdSync 服務操作。

如需詳細資訊，請參閱下列 [文章](./whatis-hybrid-identity.md) 。

提供者傳回下列錯誤資訊的其他詳細資料：
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。