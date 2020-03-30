---
title: Azure API 管理層的功能式比較 | Microsoft Docs
description: 本文根據 API 管理層提供的功能來比較這些管理層。
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2019
ms.author: apimpm
ms.openlocfilehash: 2e84138419986ef1033ab076b3745187812e91b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335894"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Azure API 管理層的功能式比較

每個 API 管理[定價層](https://aka.ms/apimpricing)提供一系列獨特的功能以及每單位[容量](api-management-capacity.md)。 下表簡要說明各層中提供的重要功能。 根據階層而定，某些功能可能運作方式不同，或是具備不同的能力。 說明文件的文章會提及此情況下的差異，並說明個別功能。

> [!IMPORTANT]
> 請注意，開發人員層用於非生產用例和評估。 它不提供 SLA。

| 功能                                                                                      | 耗用量 | 開發人員 | 基本 | 標準 | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD 整合<sup>1</sup>                                                             | 否          | 是       | 否    | 是      | 是     |
| 虛擬網路 (VNet) 支援                                                               | 否          | 是       | 否    | 否       | 是     |
| 多重區域部署                                                                      | 否          | 否        | 否    | 否       | 是     |
| 多重自訂網域名稱                                                                 | 否          | 否        | 否    | 否       | 是     |
| 開發人員入口網站<sup>2</sup>                                                                 | 否          | 是       | 是   | 是      | 是     |
| 內建快取                                                                               | 否          | 是       | 是   | 是      | 是     |
| 內建分析                                                                           | 否          | 是       | 是   | 是      | 是     |
| [自託管閘道](self-hosted-gateway-overview.md)<sup>3</sup>                           | 否          | 是       | 否    | 否       | 是     |
| [TLS 設定](api-management-howto-manage-protocols-ciphers.md)                             | 是         | 是       | 是   | 是      | 是     |
| [外部快取](https://aka.ms/apimbyoc)                                                    | 是         | 是       | 是   | 是      | 是     |
| [用戶端憑證身份驗證](api-management-howto-mutual-certificates-for-clients.md) | 是         | 是       | 是   | 是      | 是     |
| [備份和還原](api-management-howto-disaster-recovery-backup-restore.md)               | 否          | 是       | 是   | 是      | 是     |
| [透過 Git 管理](api-management-configuration-repository-git.md)                        | 否          | 是       | 是   | 是      | 是     |
| 直接管理 API                                                                        | 否          | 是       | 是   | 是      | 是     |
| Azure 監視器記錄和計量                                                               | 否          | 是       | 是   | 是      | 是     |
| 靜態 IP                                                                                    | 否          | 是       | 是   | 是      | 是     |

<sup>1</sup>啟用使用 Azure AD（和 Azure AD B2C）作為使用者登錄開發人員門戶的標識提供程式。<br/>
<sup>2</sup> 包括相關的功能，例如使用者、群組、問題、應用程式以及電子郵件範本和通知。<br/>
<sup>3</sup>僅限於具有單個閘道節點的單個自託管閘道部署。<br/>
