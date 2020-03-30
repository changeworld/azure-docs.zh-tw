---
title: 建立 NetApp 帳戶以存取 Azure NetApp Files | Microsoft Docs
description: 說明如何存取 Azure NetApp Files 及建立 NetApp 帳戶，以便設定容量集區及建立磁碟區。
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012578"
---
# <a name="create-a-netapp-account"></a>建立 NetApp 帳戶
建立 NetApp 帳戶可讓您設定容量集區，並接著建立磁碟區。 您可以使用 [Azure NetApp Files] 刀鋒視窗來建立新的 NetApp 帳戶。

## <a name="before-you-begin"></a>開始之前
您必須收到 Azure NetApp 檔團隊的電子郵件，確認已被授予對服務的存取權限。 請參閱[提交訪問服務的等待清單請求](azure-netapp-files-register.md#waitlist)。

您還必須註冊了使用 NetApp 資源供應商的訂閱。 請參閱[註冊 NetApp 資來源提供者](azure-netapp-files-register.md#resource-provider)。

## <a name="steps"></a>步驟 

1. 登入 Azure 入口網站。 
2. 使用下列其中一個方法存取 [Azure NetApp Files] 刀鋒視窗：  
   * 在 Azure 入口網站搜尋方塊中搜尋 **Azure NetApp Files**。  
   * 按一下導覽中的 [所有服務]****，然後篩選為 Azure NetApp Files。  

   您可以藉由按一下 [Azure NetApp Files] 刀鋒視窗旁邊的星號，將其加入「我的最愛」。 

3. 按一下 [+ 新增]**** 以建立新的 NetApp 帳戶。  
   [新增 NetApp 帳戶] 視窗隨即出現。  

4. 請提供 NetApp 帳戶的下列資訊： 
   * **帳戶名稱**  
     指定訂用帳戶的唯一名稱。
   * **訂閱**  
     從現有訂用帳戶選取訂用帳戶。
   * **資源組**   
     使用現有的資源群組或建立新群組。
   * **位置**  
     選取您想要帳戶及其子資源位在哪個區域。  

     ![新增 NetApp 帳戶](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. 按一下 **[建立]**。     
   您剛建立的 NetApp 帳戶現在會出現在 [Azure NetApp Files] 刀鋒視窗中。 

> [!NOTE] 
> 如果您尚未被授予對 Azure NetApp 檔服務的存取權限，則當您嘗試創建第一個 NetApp 帳戶時，您將收到以下錯誤：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>後續步驟  

[設定容量集區](azure-netapp-files-set-up-capacity-pool.md)

