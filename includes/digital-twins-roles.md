---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044915"
---
下表描述 Azure Digital Twins 中可用的角色：

| **角色** | **描述** | **識別碼** |
| --- | --- | --- |
| 空間管理員 | 適用於指定空間及其下所有節點的建立**、讀取**、更新** 和刪除** 權限。 全域權限。 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| 使用者管理員| 適用於使用者和使用者相關物件的建立**、讀取**、更新** 和刪除** 權限。 *讀取*空格的許可權。 | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| 裝置管理員 | 適用於裝置和裝置相關物件的建立**、讀取**、更新** 和刪除** 權限。 *讀取*空格的許可權。 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| 金鑰管理員 | 適用於存取金鑰的建立**、讀取**、更新** 和刪除** 權限。 *讀取*空格的許可權。 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| 權杖管理員 |  適用於存取金鑰的讀取** 和更新** 權限。 *讀取*空格的許可權。 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| User |  適用於空間、感應器和使用者 (包括其對應的相關物件) 的讀取** 權限。 | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| 支援專家 |  存取金鑰以外各個項目的讀取** 權限。 | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| 裝置安裝人員 | 適用於裝置和感應器 (包括其對應的相關物件) 的讀取** 和更新** 權限。 *讀取*空格的許可權。 | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| 閘道裝置 | 感應器的建立** 權限。 *讀取*設備和感應器的讀取權限，包括其相應的相關物件。 | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |