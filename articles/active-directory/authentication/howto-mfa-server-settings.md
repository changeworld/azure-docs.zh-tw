---
title: 設定 MFA Server-Azure Active Directory
description: 瞭解如何在 Azure 入口網站中設定 Azure MFA 伺服器的設定
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69733071c5b43ee9c8e6450e3a9924bc656d5c84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84485491"
---
# <a name="configure-mfa-server-settings"></a>設定 MFA Server 設定

本文可協助您管理 Azure 入口網站中的 Azure MFA 伺服器設定。

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起，Microsoft 不再為新的部署提供 MFA 伺服器。 希望要求使用者使用多重要素驗證的新客戶應該使用雲端式 Azure Multi-Factor Authentication。 在 7 月 1 日前啟用 MFA 伺服器的現有客戶，將能夠下載最新版本及未來的更新，並如常產生啟用認證。

可用的 MFA Server 設定如下：

| 功能 | 描述 |
| ------- | ----------- |
| 伺服器設定 | 下載 MFA 伺服器並產生啟用認證，以初始化您的環境 |
| [一次性略過](#one-time-bypass) | 允許使用者在一段有限時間未執行多重要素驗證的情況下進行驗證。 |
| [快取規則](#caching-rules) |  快取主要是在內部部署系統 (例如 VPN) 於第一個要求仍在進行中的同時，傳送多個驗證要求時使用。 此功能可讓後續要求在使用者成功完成進行中的第一次驗證後自動成功。 |
| 伺服器狀態 | 查看您內部部署 MFA 伺服器的狀態，包括版本、狀態、IP 和最後的通訊時間與日期。 |

## <a name="one-time-bypass"></a>一次性略過

單次許可功能可讓使用者在不執行多重要素驗證的情況下驗證一次。 許可只是暫時性，經過指定的秒數之後就會到期。 在行動裝置應用程式或電話沒有收到通知或來電的情況下，您可以啟用單次許可，讓使用者能夠存取所需的資源。

若要建立一次性略過，請完成下列步驟：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [ **Azure Active Directory**]，然後流覽至 [**安全性**  >  **MFA**  >  **一次性略過**]。
1. 選取 [新增]。
1. 如有必要，請選取此許可的複寫群組。
1. 輸入 `username\@domain.com` 作為使用者名稱。 輸入略過的間隔秒數，以及略過的原因。
1. 選取 [新增]。 時間限制會立即生效。 使用者必須在單次許可到期之前登入。

您也可以從這個視窗中查看單次略過報表。

## <a name="caching-rules"></a>快取規則

您可以使用_快取_功能，來設定使用者已驗證之後，允許驗證嘗試的時段。 使用者在指定期間內的後續驗證嘗試會成功自動。

快取主要是在內部部署系統 (例如 VPN) 於第一個要求仍在進行中的同時，傳送多個驗證要求時使用。 此功能可讓後續要求在使用者成功完成進行中的第一次驗證後自動成功。

>[!NOTE]
> 快取功能的目的不是要用於登入 Azure Active Directory (Azure AD)。

若要設定快取，請完成下列步驟：

1. 瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [快取規則]。
1. 選取 [新增]。
1. 從下拉式清單中選取 [快取類型]。 輸入**快取秒數**的最大數目。
1. 如有必要，選取驗證類型並指定應用程式。
1. 選取 [新增]。

## <a name="next-steps"></a>接下來的步驟

您可以從 MFA Server 本身的 web 主控台取得其他 MFA Server 設定選項。 您也可以 [設定 Azure MFA 伺服器以獲得高可用性](howto-mfaserver-deploy-ha.md)。
