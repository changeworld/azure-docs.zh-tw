---
title: 什麼是 Azure Active Directory 中的企業狀態漫遊？
description: 企業狀態漫遊可讓使用者在其 Windows 裝置上有一致的體驗
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "77194274"
---
# <a name="what-is-enterprise-state-roaming"></a>企業狀態漫遊是什麼？

使用 Windows 10， [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) 使用者能夠安全地將其使用者設定和應用程式設定資料同步處理至雲端。 企業狀態漫遊提供使用者跨 Windows 裝置的一致體驗，並且減少設定新的裝置所需的時間。 企業狀態漫遊的運作類似於首先在 Windows 8 中引進的標準 [取用者設定同步處理](https://go.microsoft.com/fwlink/?linkid=2015135) 。 此外，企業狀態漫遊提供：

* **分隔公司和取用者資料** – 組織可以控制他們的資料，取用者雲端帳戶中不會混合公司資料，企業雲端帳戶中也不會混合取用者資料。
* **增強式安全性** – 資料會在離開使用者的 Windows 10 裝置之前自動加密，方法是使用 Azure Rights Management (Azure RMS)，資料在雲端中待用時會保持加密。 所有內容在雲端中待用時會保持加密，除了命名空間，例如設定名稱和 Windows 應用程式名稱。  
* **更好的管理和監視** – 透過 Azure AD 入口網站整合，對於誰能同步處理組織中的設定以及在哪些裝置上提供更多的控制權和可見性。 

企業狀態漫遊也可以在多個 Azure 區域中使用。 您可以在 Azure Active Directory 下方的 [依區域提供的 Azure 服務](https://azure.microsoft.com/regions/#services) 上找到可用區域的更新清單。

| 發行項 | 描述 |
| --- | --- |
| [在 Azure Active Directory 中啟用企業狀態漫遊](enterprise-state-roaming-enable.md) |企業狀態漫遊適用於具有 Premium Azure Active Directory (Azure AD) 訂用帳戶的任何組織。 如需有關如何取得 Azure AD 訂用帳戶的詳細資訊，請參閱 [Azure AD 產品頁面](https://azure.microsoft.com/services/active-directory)。 |
| [設定和資料漫遊常見問題集](enterprise-state-roaming-faqs.md) |本文將回答 IT 系統管理員可能會遇到的設定和應用程式資料同步處理的一些問題。 |
| [設定同步處理的群組原則和 MDM 設定](enterprise-state-roaming-group-policy-settings.md) |Windows 10 提供群組原則和行動裝置管理 (MDM) 原則設定，以限制設定同步處理。 |
| [Windows 10 漫遊設定參考](enterprise-state-roaming-windows-settings-reference.md) |將在 Windows 10 中進行漫遊及/或備份的設定清單。 |
| [疑難排解](enterprise-state-roaming-troubleshooting.md) |本文會執行一些疑難排解基本步驟，並且包含一份已知問題清單。 |

## <a name="next-steps"></a>後續步驟

如需啟用企業狀態漫遊的詳細資訊，請參閱[啟用企業狀態漫遊](enterprise-state-roaming-enable.md)。
