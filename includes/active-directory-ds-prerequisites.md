---
title: 包含檔案
description: 包含檔案與先決條件
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426859"
---
> [!IMPORTANT]
> **請先啟用 Azure AD Domain Services 的密碼雜湊同步處理，再完成本文中的工作。**
>
> 請根據 Azure AD 目錄中的使用者類型來遵循下列指示。 如果您在 Azure AD 目錄中混合使用純雲端和已同步的使用者帳戶，則下列兩個指示都必須完成。 如果您嘗試使用 B2B 來賓帳戶（例如，您允許的其他標識供應商的 gmail 或 MSA），您可能無法執行以下操作，因為我們沒有將這些使用者的密碼同步到託管域，因為這些帳戶是目錄中的來賓帳戶。 有關這些帳戶（包括其密碼）的完整資訊將在 Azure AD 之外，並且此資訊不在 Azure AD 中，因此它甚至不會同步到託管域。 
> - [僅限雲端使用者帳戶的指示](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [從內部部署目錄同步的使用者帳戶指示](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
