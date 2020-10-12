---
title: 建立 Azure Kubernetes Service 叢集資源
titleSuffix: Azure Cognitive Services
description: 瞭解如何建立 Azure Kubernetes Service (AKS) 資源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87298747"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>建立 Azure Kubernetes Service 叢集資源

1. 移至 [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)，然後選取 [ **建立**]。

1. 在 [基本]**** 索引標籤上，輸入下列資訊：

    |設定|值|
    |--|--|
    |訂用帳戶|選取適當的訂用帳戶。|
    |資源群組|選取可用的資源群組。|
    |Kubernetes 叢集名稱|輸入 (小寫) 的名稱。|
    |區域|選取附近的位置。|
    |Kubernetes 版本|任何值會標示為 ** (預設) **。|
    |DNS 名稱前置詞|自動建立，但您可以覆寫。|
    |節點大小|標準 DS2 v2：<br>`2 vCPUs`, `7 GB`|
    |節點計數|將滑杆保留為預設值。|

1. 在 [ **節點** 集區] 索引標籤上，讓 **虛擬節點** 和 **VM 擴展集** 保持設定為其預設值。
1. 在 [ **驗證** ] 索引標籤上，保留 [ **服務主體** ]，並將 **RBAC** 設定為其預設值。
1. 在 [ **網路** 功能] 索引標籤上，輸入下列選項：

    |設定|值|
    |--|--|
    |HTTP 應用程式路由|否|
    |網路設定|基本|

1. **在 [整合**] 索引標籤上，確定 [**容器監視**] 設定為 [**已啟用**]，並將 [ **Log Analytics 工作區**] 保留為預設值。
1. 在 [ **標記** ] 索引標籤上，將名稱/值組保留為空白。
1. 選取 [ **審核並建立**]。
1. 通過驗證之後，請選取 [ **建立**]。

> [!NOTE]
> 如果驗證失敗，可能是因為「服務主體」錯誤。 返回至 [ **驗證** ] 索引標籤，然後返回 [ **審核] + [建立**]，驗證應該執行然後通過。
