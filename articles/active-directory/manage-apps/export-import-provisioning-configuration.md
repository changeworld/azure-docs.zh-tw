---
title: 使用圖形 API 匯出或匯入您的布建設定 |Microsoft Docs
description: 瞭解如何使用圖形 API 匯出和匯入布建設定。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609031bfad23a14a954a09a447e363e89a9d29d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711741"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>使用圖形 API 匯出或匯入您的布建設定

您可以使用 Microsoft Graph API 和 Graph Explorer，將您的使用者布建屬性對應和架構匯出至 JSON 檔案，並將其匯入回 Azure AD。 您也可以使用此處所提供的步驟來建立布建設定的備份。 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步驟1：取出您的布建 App Service 主體識別碼（物件識別碼）

1. 啟動[Azure 入口網站](https://portal.azure.com)，然後流覽至布建應用程式的 [屬性] 區段。 例如，如果您想要將 Workday 匯出*至 AD 使用者布建應用程式*對應，請流覽至該應用程式的 [屬性] 區段。 
1. 在佈建應用程式的 [屬性] 區段中，複製與 [物件識別碼] 欄位相關的 GUID 值。 此值也稱為您應用程式的 **ServicePrincipalId**，並且會在「Graph 總管」作業中用到。

   ![Workday 應用程式服務主體識別碼](media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>步驟2：登入 Microsoft Graph Explorer

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)
1. 按一下 [使用 Microsoft 登入] 按鈕，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證來登入。

    ![Graph 登入](media/export-import-provisioning-mappings/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>步驟3：取出布建應用程式的布建作業識別碼

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以從**步驟 1** 擷取的 [ServicePrincipalId](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 取代 [servicePrincipalId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

您會得到如下所示的回應。 請複製回應中的「id 屬性」。 此值是 **ProvisioningJobId**，且將會用來擷取基礎結構描述中繼資料。

   [![布建作業識別碼](media/export-import-provisioning-mappings/wd_export_03.png)](media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>步驟4：下載布建架構

在「Microsoft Graph 總管」中，執行下列 GET 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

從回應中複製 JSON 物件，然後將它儲存成檔案以建立結構描述備份。

## <a name="step-5-import-the-provisioning-schema"></a>步驟5：匯入布建架構

> [!CAUTION]
> 請只有在您需要為無法使用 Azure 入口網站來變更的設定修改結構描述，或您需要從含有效且可運作之結構描述的先前備份檔案還原設定時，才執行此步驟。

在「Microsoft Graph 總管」中，設定下列 PUT 查詢，其中以在先前步驟中擷取的 ServicePrincipalId 和 ProvisioningJobId 取代 [servicePrincipalId] 和 [ProvisioningJobId]。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

在 [要求本文] 索引標籤中，複製 JSON 結構描述檔案的內容。

   [![要求本文](media/export-import-provisioning-mappings/wd_export_04.png)](media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

在 [要求標頭] 索引標籤中，新增值為 “application/json” 的 Content-Type 標頭屬性

   [![要求標頭](media/export-import-provisioning-mappings/wd_export_05.png)](media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

按一下 [執行查詢] 按鈕以匯入新的結構描述。
