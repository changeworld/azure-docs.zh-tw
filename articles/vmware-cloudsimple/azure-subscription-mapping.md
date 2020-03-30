---
title: 使用 Azure 訂閱映射創建資源池
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述如何通過 Azure 訂閱映射為私有雲創建資源池
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014958"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>使用 Azure 訂閱映射為私有雲創建資源池
Azure 訂閱映射允許您從可用的 vSphere 資源池為私有雲創建資源池。 在 CloudSimple 門戶中，您可以查看和管理私有雲的 Azure 訂閱。

> [!NOTE]
> 映射資源池還會映射任何子資源池。 如果已映射任何子資源池，則無法映射父資源池。

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)。
2. 打開 **"資源"** 頁並選擇**Azure 訂閱映射**。  
3. 按一下 **"編輯 Azure 訂閱映射**"。  
4. 要映射可用資源池，請在左側選擇它們，然後按一下右側箭頭。 
5. 要刪除映射，請在右側選擇它們，然後按一下左側箭頭。 

    ![Azure 訂用帳戶](media/resources-azure-mapping.png)

6. 按一下 [確定]****。
