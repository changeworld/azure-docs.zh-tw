---
title: 使用 Azure Active Directory 的 SSH 驗證
description: 使用 Azure Active Directory 達成 SSH 整合的架構指引
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b77ab0832fa19149c270d6ba5a6641069548cbe
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172715"
---
# <a name="ssh"></a>SSH  

安全殼層 (SSH) 是一種網路通訊協定，可透過不安全的網路安全地加密作業系統服務。 SSH 也提供命令列登入、執行遠端命令，以及安全地傳輸檔案。 它通常用於 UNIX 系統，例如 Linux®。 SSH 會取代 Telnet 通訊協定，此通訊協定不會在不安全的網路中提供加密。 

Azure Active Directory (Azure AD) 針對在 Azure 上執行的 Linux (型系統，提供虛擬機器) VM®擴充功能。 

## <a name="use-when"></a>使用時機 

* 使用需要遠端登入的 Linux®型 Vm

* 在以 Linux®為基礎的系統中執行遠端命令

* 在不安全的網路中安全地傳輸檔案

![使用 SSH 通訊協定 Azure AD 的圖表](./media/authentication-patterns/ssh-auth.png)

SSH 與 Azure AD

## <a name="components-of-system"></a>系統的元件 

* **使用者**：啟動 SSH 用戶端以設定與 Linux® vm 的連線，並提供驗證用的認證。

* **Web 瀏覽器**：與使用者互動的元件。 它會與身分識別提供者通訊 (Azure AD) 來安全地驗證及授權使用者。

* **SSH 用戶端**：驅動連接設定程式。

* **Azure AD**：使用裝置流程驗證使用者的身分識別，並將權杖發行至 Linux vm。

* **LINUX VM**：接受權杖並提供成功的連接。

## <a name="implement-ssh-with-azure-ad"></a>使用 Azure AD 來執行 SSH 

* [使用 Azure Active Directory 認證登入 Linux® VM-Azure 虛擬機器 ](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2.0 裝置程式碼流程-Microsoft 身分識別平臺 ](../develop/v2-oauth2-device-code.md)

* [與 Azure Active Directory (akamai.com) 整合 ](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

