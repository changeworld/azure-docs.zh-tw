---
title: Microsoft Cloud Germany 中的 Azure AD Connect
description: Azure AD Connect 會整合您的內部部署目錄與 Azure Active Directory。 這可讓您為與 Azure AD 整合的 Office 365、Azure 和 SaaS 應用程式提供通用身分識別。
keywords: Azure AD Connect 簡介, Azure AD Connect 概觀, 何謂 Azure AD Connect, 安裝 active directory, 德國, 黑森林
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62f0d82a543c0ae4e629eda3bca18b0a06322f2c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "60381227"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Microsoft Cloud Germany 中的 Azure AD Connect - 公開預覽
## <a name="introduction"></a>簡介
Azure AD Connect 可進行內部部署 Active Directory 與 Azure Active Directory 之間的同步處理。
[Microsoft Cloud Germany](https://azure.microsoft.com/global-infrastructure/germany/
) 中目前有許多案例必須由操作員來完成。 使用 Microsoft Cloud Germany 時，您必須注意下列資訊︰

* 下列 URL 必須在 Proxy 伺服器上開啟，才能成功進行同步處理：
  
  * *.microsoftonline.de
  * *.windows.net
  * * 憑證撤銷清單
* 當您登入 Azure AD 目錄時，必須使用 onmicrosoft.de 網域中的帳戶。

 
## <a name="download"></a>下載
您可以從入口網站中的 [Azure AD Connect] 刀鋒視窗下載 Azure AD Connect。  使用下列指示來尋找 Azure AD Connect 刀鋒視窗。

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect 刀鋒視窗
一旦登入 Azure 入口網站：

1. 移至瀏覽
2. 選取 Azure Active Directory
3. 然後選取 Azure AD Connect

您會看到以下詳細資料：

![Azure AD Connect 刀鋒視窗](./media/reference-connect-germany/germany1.png)

下表描述刀鋒視窗中顯示的功能。

| Title | 描述 |
| --- | --- |
| 同步處理狀態 |讓您知道已啟用或停用同步處理。 |
| 上次同步處理 |上次成功完成同步處理。 |
| 同盟網域 |顯示目前設定的同盟網域數目。 |

## <a name="installation"></a>安裝
若要安裝 Azure AD Connect，您可以使用 [這裡](how-to-connect-install-roadmap.md)的文件。

## <a name="advanced-features-and-additional-information"></a>進階功能和其他資訊
如需自訂設定或進階組態的其他資訊，請移至[整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。 此頁面會提供其他指引的資訊和連結。

