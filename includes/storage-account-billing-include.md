---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851618"
---
根據存儲帳戶使用方式為 Azure 存儲計費。 儲存體帳戶中的所有物件會做為群組共同計費。 

存儲成本根據以下因素計算： 

* **區域**是指您的帳戶所在的地理區域。
* **帳戶類型**是指您使用的存儲帳戶類型。 
* **訪問層**是指為通用 v2 或 Blob 存儲帳戶指定的資料使用模式。
* 存儲**容量**是指用於存儲資料的存儲帳戶分配量。
* **複製**確定一次維護資料的副本數以及位於哪些位置。
* **事務**是指 Azure 存儲的所有讀寫操作。
* 「資料輸出」**** 係指從 Azure 區域傳出的任何資料。 當存儲帳戶中的資料由未在同一區域中運行的應用程式訪問時，將收取資料出口的費用。 如需了解如何使用資源群組來將您的資料和服務聚集在相同區域以限制輸出費用，請參閱[什麼是 Azure 資源群組?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) 

[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/) 頁面提供了以帳戶類型、儲存體容量、複寫和交易為基礎的詳細價格資訊。 [資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/) 則提供了出口流量的詳細定價資訊。 您可以使用 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) ，以協助消除成本。

