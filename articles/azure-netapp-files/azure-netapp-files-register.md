---
title: 註冊 Azure NetApp Files | Microsoft Docs
description: 介紹如何註冊以使用 Azure NetApp 檔。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274056"
---
# <a name="register-for-azure-netapp-files"></a>註冊 Azure NetApp Files

> [!IMPORTANT] 
> 在註冊 Azure NetApp 檔資來源提供者之前，您必須收到 Azure NetApp 檔團隊的電子郵件，確認已被授予對服務的訪問。 

在本文中，瞭解如何註冊 Azure NetApp 檔，以便開始使用該服務。

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>提交訪問服務的等待清單請求

1. 通過[Azure NetApp 檔等待清單提交頁](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)提交訪問 Azure NetApp 檔服務的等待清單請求。 

    等待清單註冊不保證立即訪問服務。 

2. 等待 Azure NetApp 檔團隊的正式確認電子郵件，然後再繼續執行其他任務。 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>註冊 NetApp 資源提供者

若要使用服務，您必須註冊 Azure NetApp Files 的 Azure 資源提供者。

> [!NOTE] 
> 即使沒有獲得服務存取權限，您也能成功註冊 NetApp 資源供應商。 但是，如果沒有訪問授權，創建 NetApp 帳戶或任何其他 Azure NetApp 檔資源的任何 Azure 門戶或 API 請求都將被拒絕，並出現以下錯誤：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. 在 Azure 入口網站中，按一下右上角的 Azure Cloud Shell 圖示：

      ![Azure Cloud Shell 圖示](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 如果您的 Azure 帳戶中有多個訂用帳戶，請選取已列入 Azure NetApp Files 允許清單中的訂用帳戶：
    
        az account set --subscription <subscriptionId>

3. 在 Azure Cloud Shell 主控台中輸入下列命令，以驗證您的訂用帳戶已列入白清單中：
    
        az feature list | grep NetApp

   命令輸出顯示如下：
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` 是您的訂用帳戶識別碼。

    如果未看到功能名稱`Microsoft.NetApp/ANFGA`，則無法訪問該服務。 停止此步驟。 按照["提交訪問服務的等待清單請求](#waitlist)"中的說明進行操作，以請求服務訪問，然後再繼續。 

4. 在 Azure Cloud Shell 中輸入下列命令，以註冊 Azure 資源提供者： 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` 參數會指示主控台等候註冊完成。 註冊程序可能需要一些時間才能完成。

5. 在 Azure Cloud Shell 中輸入下列命令，以驗證 Azure 資源提供者已註冊： 
    
        az provider show --namespace Microsoft.NetApp

   命令輸出顯示如下：
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` 是您的訂用帳戶識別碼。  `state` 參數值會指出 `Registered`。

6. 從 Azure 入口網站中，按一下 [訂用帳戶]**** 刀鋒視窗。
7. 在 [訂用帳戶] 刀鋒視窗中，按一下您的訂用帳戶識別碼。 
8. 在訂用帳戶設定中按一下 [資源提供者]****，以驗證 Microsoft.NetApp 提供者顯示為 [已註冊] 狀態： 

      ![已註冊的 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>後續步驟

[建立 NetApp 帳戶](azure-netapp-files-create-netapp-account.md)