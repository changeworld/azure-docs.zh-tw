---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "73903682"
---
但是，如果您想要大規模共用 Azure 租使用者的影像，您應該建立應用程式註冊，以促進共用。  使用應用程式註冊可實現更複雜的共用案例，例如： 

* 當某家公司取得另一家公司時管理共用映射，Azure 基礎結構會散佈到不同的租使用者。 
* Azure 合作夥伴代表其客戶管理 Azure 基礎結構。 映射的自訂是在合作夥伴租使用者內進行，但基礎結構部署將會在客戶的租使用者中進行。 


## <a name="create-the-app-registration"></a>建立應用程式註冊

建立兩個租使用者將用來共用映射庫資源的應用程式註冊。
1. [在 Azure 入口網站中開啟應用程式註冊 (preview) ](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)。    
1. 從頁面頂端的功能表中選取 [ **新增註冊** ]。
1. 在 [ **名稱**] 中，輸入 *myGalleryApp*。
1. 在 [ **支援的帳戶類型**] 中，選取 **任何組織目錄中的帳戶及個人的 Microsoft 帳戶**。
1. 在 [重新 **導向 URI**] 中，輸入 *https://www.microsoft.com* ，然後選取 [ **註冊**]。 建立應用程式註冊之後，將會開啟 [總覽] 頁面。
1. 在 [總覽] 頁面上，將 **應用程式複製 (用戶端) 識別碼** ，並儲存以供稍後使用。   
1. 選取 [ **憑證] & 秘密**，然後選取 [ **新增用戶端密碼**]。
1. 在 [ **描述**] 中，輸入 *共用映射庫跨租使用者應用程式秘密*。
1. 在 [ **到期**] 中，將預設值保留為 **1 年** ，然後選取 [ **新增**]。
1. 複製秘密的值，並將它儲存到安全的位置。 離開頁面之後，您就無法取出。


授與應用程式註冊許可權，以使用共用映射庫。
1. 在 [Azure 入口網站中，選取您要與另一個租使用者共用的共用映射庫。
1. 選取 [ **選取存取控制] (IAM) **，然後在 [ **新增角色指派** ] 底下選取 [ *加入*]。 
1. 在 [ **角色**] 底下，選取 [ **讀取**者]。
1. 在 [ **將存取權指派給：**] 下，將此設為 **Azure AD 使用者、群組或服務主體**。
1. 在 [ **選取**] 下，輸入 *myGalleryApp* ，然後在清單中顯示時選取它。 完成之後，請選取 [儲存]****。


## <a name="give-tenant-2-access"></a>授與租使用者2存取權

使用瀏覽器要求登入，為租使用者2的應用程式提供存取權。 取代為您想要與之 *\<Tenant2 ID>* 共用映射庫之租使用者的租使用者識別碼。 取代 *\<Application (client) ID>* 為您所建立之應用程式註冊的應用程式識別碼。 完成取代之後，請將 URL 貼到瀏覽器中，並遵循登入提示以登入租使用者2。

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

在 [Azure 入口網站](https://portal.azure.com) 以租使用者2的形式登入，並將應用程式註冊存取權授與您要在其中建立 VM 的資源群組。

1. 選取資源群組，然後選取 [ **存取控制] (IAM) **。 在 [ **新增角色指派** ] 底下，選取 [ **新增**]。 
1. 在 [ **角色**] 下，輸入 **參與者**。
1. 在 [ **將存取權指派給：**] 下，將此設為 **Azure AD 使用者、群組或服務主體**。
1. 在 [ **選取** 類型] *myGalleryApp* 下，在清單中顯示時選取它。 完成之後，請選取 [儲存]****。

> [!NOTE]
> 您必須等候映像版本完全完成建立和複寫後，才能使用相同的受控映像來建立另一個映像版本。

