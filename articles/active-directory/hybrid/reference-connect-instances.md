---
title: Azure AD Connect︰同步服務執行個體 | Microsoft Docs
description: 本頁記載 Azure AD 執行個體的特殊考量。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298823"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect：執行個體的特殊考量
Azure AD Connect 最常搭配 Azure AD 和 Office 365 的全球執行個體。 但還有其他執行個體，而它們有不同的 URL 需求和其他特殊考量。

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) 是由德國資料信託所運作的最高雲端。

| 要在 Proxy 伺服器中開啟的 URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +憑證撤銷清單 |

當您登入 Azure AD 租用戶時，必須使用 onmicrosoft.de 網域中的帳戶。

Microsoft Cloud Germany 目前沒有的功能︰

* **密碼寫回**可用於預覽 Azure AD Connect 版本 1.1.570.0 及之後。
* 無法使用其他 Azure AD Premium 服務。

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Microsoft Azure Government 雲端](https://azure.microsoft.com/features/gov/)是用於 US Gov 的雲端。

此雲端是以舊版的 DirSync 提供支援。 從 Azure AD Connect 組建 1.1.180 起，即支援新一代的雲端。 這一代使用僅限美國的端點，而且有不同的 URL 清單要在您的 Proxy 伺服器中開啟。

| 要在 Proxy 伺服器中開啟的 URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net（自動 Azure 政府租戶檢測需要） |
| \*.gov.us.microsoftonline.com |
| +憑證撤銷清單 |

> [!NOTE]
> 從 Azure AD Connect 版本 1.1.647.0 開始，如果 *.windows.net在代理伺服器上打開，則不再需要在註冊表中設置 Azure 實例值。 但是，對於不允許從 Azure AD Connect 伺服器進行 Internet 連接的客戶，可以使用以下手動設定。

### <a name="manual-configuration"></a>手動組態

以下手動設定步驟用於確保 Azure AD 連接使用 Azure 政府同步終結點。

1. 開始 Azure AD Connect 安裝。
2. 當您看見您應在其中接受 EULA 的第一頁時，請勿繼續進行，但讓安裝精靈保持執行。
3. 啟動 regedit 並將登錄機碼 `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` 變更為 `4` 值。
4. 回到 Azure AD Connect 安裝精靈，接受 EULA 並繼續進行。 在安裝過程中，請確保使用**自訂配置**安裝路徑（而不是快速安裝），然後照常繼續安裝。

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
