---
title: 註冊 Azure NetApp Files | Microsoft Docs
description: 說明如何註冊以使用 Azure NetApp Files。
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
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: cdb96f08f78e22dd0e46070ab62bf9327e2d72a3
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956299"
---
# <a name="register-for-azure-netapp-files"></a>註冊 Azure NetApp Files

> [!IMPORTANT] 
> 在註冊 Azure NetApp Files 資源提供者之前，您必須先從 Azure NetApp Files 小組收到一封電子郵件，確認您已獲得服務的存取權。 

在本文中，您將瞭解如何註冊 Azure NetApp Files，讓您可以開始使用服務。

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>提交等候清單要求以存取服務

1. 提交等候清單要求，以透過[Azure Netapp files 等候清單提交頁面](https://aka.ms/azurenetappfiles)來存取 Azure netapp files 服務。 

    等候清單註冊不保證能夠立即存取服務。 

2. 在繼續進行其他工作之前，請先從 Azure NetApp Files 小組等候官方的確認電子郵件。 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>註冊 NetApp 資源提供者

若要使用服務，您必須註冊 Azure NetApp Files 的 Azure 資源提供者。

> [!NOTE] 
> 即使沒有授與服務的存取權，您仍然可以成功註冊 NetApp 資源提供者。 不過，若沒有存取授權，任何建立 NetApp 帳戶或任何其他 Azure NetApp Files 資源的 Azure 入口網站或 API 要求將會遭到拒絕，並出現下列錯誤：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. 在 Azure 入口網站中，按一下右上角的 Azure Cloud Shell 圖示：

      ![Azure Cloud Shell 圖示](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 如果您的 Azure 帳戶中有多個訂用帳戶，請選取已列入 Azure NetApp Files 允許清單中的訂用帳戶：
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. 在 Azure Cloud Shell 主控台中輸入下列命令，以驗證您的訂用帳戶已列入白清單中：
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   命令輸出顯示如下：
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` 是您的訂用帳戶識別碼。

    如果您看不到此功能名稱 `Microsoft.NetApp/ANFGA` ，表示您沒有服務的存取權。 在此步驟停止。 請遵循[提交用於存取服務的等候清單要求](#waitlist)中的指示來要求服務存取，然後再繼續。 

4. 在 Azure Cloud Shell 中輸入下列命令，以註冊 Azure 資源提供者： 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   `--wait` 參數會指示主控台等候註冊完成。 註冊程序可能需要一些時間才能完成。

5. 在 Azure Cloud Shell 中輸入下列命令，以驗證 Azure 資源提供者已註冊： 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   命令輸出顯示如下：
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` 是您的訂用帳戶識別碼。  `state` 參數值會指出 `Registered`。

6. 從 Azure 入口網站中，按一下 [訂用帳戶]**** 刀鋒視窗。
7. 在 [訂用帳戶] 刀鋒視窗中，按一下您的訂用帳戶識別碼。 
8. 在訂用帳戶設定中按一下 [資源提供者]****，以驗證 Microsoft.NetApp 提供者顯示為 [已註冊] 狀態： 

      ![已註冊的 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>後續步驟

[建立 NetApp 帳戶](azure-netapp-files-create-netapp-account.md)
