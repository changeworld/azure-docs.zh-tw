---
title: 受支援 Azure PaaS 資源可用的 Azure 資訊安全中心功能。
description: 此頁面會顯示支援的 Azure PaaS 資源可使用哪些 Azure 資訊安全中心功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 4c56f8cba232220844a8a8f9c408a60c89c581cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613623"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Azure PaaS 服務的功能涵蓋範圍<a name="paas-services"></a>

下表說明支援的 Azure PaaS 資源可使用哪些 Azure 資訊安全中心功能。

|服務|建議 (免費)|安全性警示 (Azure Defender)|弱點評量 (Azure Defender)|
|:----|:----:|:----:|:----:|
|Azure App Service|✔|✔|-|
|Azure 自動化帳戶|✔|-|-|
|Azure Batch 帳戶|✔|-|-|
|Azure Blob 儲存體|✔|✔|-|
|Azure Cache for Redis|✔|-|-|
|Azure 雲端服務|✔|-|-|
|Azue 認知搜尋|✔|-|-|
|Azure Container Registry|-|-|✔|
|Azure Cosmos DB*|-|✔|-|
|Azure Data Lake Analytics|✔|-|-|
|Azure Data Lake 儲存體|✔|✔|-|
|適用於 MySQL 的 Azure 資料庫*|-|✔|-|
|適用於 PostgreSQL 的 Azure 資料庫*|-|✔|-|
|Azure 事件中樞命名空間|✔|-|-|
|Azure Functions 應用程式|✔|-|-|
|Azure 金鑰保存庫|✔|✔|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL Database|✔|✔|✔|
|Azure SQL 受控執行個體|✔|✔|✔|
|Azure 服務匯流排命名空間|✔|-|-|
|Azure Service Fabric 帳戶|✔|-|-|
|Azure 儲存體帳戶|✔|✔|-|
|Azure 串流分析|✔|-|-|
|Azure 訂用帳戶|✔ **|✔|-|
|Azure 虛擬網路</br> (包括子網路、NIC 和網路安全性群組)|✔|-|-|

\* 這些功能目前以預覽形式支援。

\*\* Azure Active Directory (Azure AD) 建議僅適用於已啟用 Azure Defender 的訂用帳戶。