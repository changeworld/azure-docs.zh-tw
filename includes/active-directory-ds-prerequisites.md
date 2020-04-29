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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "68426859"
---
> [!IMPORTANT]
> **請先啟用 Azure AD Domain Services 的密碼雜湊同步處理，再完成本文中的工作。**
>
> 請根據 Azure AD 目錄中的使用者類型來遵循下列指示。 如果您在 Azure AD 目錄中混合使用純雲端和已同步的使用者帳戶，則下列兩個指示都必須完成。 如果您嘗試使用 B2B 來賓帳戶（例如，來自我們所允許的不同身分識別提供者的 gmail 或 MSA），則您可能無法執行下列作業，因為這些使用者的密碼不會同步到受控網域，因為這些是目錄中的來賓帳戶。 這些帳戶（包括其密碼）的完整相關資訊會在 Azure AD 之外，因為這項資訊不在 Azure AD 中，因此甚至不會同步到受控網域。 
> - [僅限雲端使用者帳戶的指示](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [從內部部署目錄同步的使用者帳戶指示](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
