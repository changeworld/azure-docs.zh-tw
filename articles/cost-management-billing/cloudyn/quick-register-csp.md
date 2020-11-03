---
title: 使用 CSP 合作夥伴資訊向 Azure 中的 Cloudyn 註冊
description: 深入了解合作夥伴用來將客戶上線至 Cloudyn 入口網站的註冊程序詳細資料。
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543316"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>註冊 CSP 合作夥伴計劃並檢視成本資料

身為 CSP 合作夥伴和已註冊的 Cloudyn 使用者，您可以在 Cloudyn 中查看和分析您的雲端支出。 [Azure 成本管理依其原生狀態即適用於已將客戶登錄至 Microsoft 客戶合約，並且已購買 Azure 方案的直接合作夥伴](../costs/get-started-partners.md)。

註冊將可提供 Cloudyn 入口網站的存取權。 本快速入門詳細說明建立 Cloudyn 試用訂用帳戶並登入 Cloudyn 入口網站所需的註冊程序。

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>設定 Cloudyn 中的間接 CSP 存取

根據預設，只有直接 CSP 可以存取夥伴中心 API。 不過，直接 CSP 提供者可以使用 Cloudyn 中的實體群組，為其間接 CSP 客戶或合作夥伴設定存取。

要讓間接 CSP 客戶或合作夥伴能夠存取，請使用 Cloudyn 實體群組，完成下列步驟以分割間接 CSP 的資料。 然後，將適當的使用者權限指派給實體群組。

1. 在[建立實體](tutorial-user-access.md#create-and-manage-entities)提供資訊建立實體群組。
2. 遵循[將訂用帳戶指派給成本實體](https://www.youtube.com/watch?v=d9uTWSdoQYo)的步驟。 將間接 CSP 客戶的帳戶及其 Azure 訂用帳戶，與您先前建立的實體產生關聯。
3. 依照[建立具有系統管理員存取權的使用者](tutorial-user-access.md#create-a-user-with-admin-access)的步驟，建立具有系統管理員存取權的使用者帳戶。 接著，確定此使用者帳戶具有特定實體 (您先前為間接帳戶建立) 的系統管理員存取權。

間接 CSP 夥伴使用您為他們建立的帳戶登入 Cloudyn 入口網站。


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您會使用 CSP 資訊向 Cloudyn 註冊。 您也登入了 Cloudyn 入口網站，並開始檢視成本資料。 若要深入了解 Cloudyn，請繼續 Cloudyn 的教學課程。

> [!div class="nextstepaction"]
> [檢閱使用量和成本](tutorial-review-usage.md)