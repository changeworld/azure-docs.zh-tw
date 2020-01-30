---
title: JavaScript 和頁面配置版本
titleSuffix: Azure AD B2C
description: 瞭解如何啟用 JavaScript，並在 Azure Active Directory B2C 中使用頁面配置版本。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f6d4849b02f320c7479469b4ee56be50e4f8dee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840089"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和頁面配置版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 為使用者流程和自訂原則中的使用者介面元素，提供一組封裝內容，其中包含 HTML、CSS 和 JavaScript。 若要為您的應用程式啟用 JavaScript，您必須將專案新增至您的[自訂原則](custom-policy-overview.md)，或在入口網站中為使用者流程啟用它、選取頁面配置，然後在您的要求中使用[b2clogin.com](b2clogin.md) 。

如果您想要啟用[javascript](javascript-samples.md)用戶端程式代碼，您會想要確定以 javascript 為基礎的元素是不可變的。 否則，任何變更都可能會在使用者頁面上造成非預期的行為。 若要避免這些問題，您可以強制使用頁面配置，並指定頁面配置版本。 這麼做可確保您根據 JavaScript 的所有內容定義都是不可變的。 即使您不想要啟用 JavaScript，您也可以為頁面指定頁面配置版本。

## <a name="user-flows"></a>使用者流程

在使用者流程**屬性**中，您可以啟用 JavaScript，這也會強制使用頁面配置。 接著，您可以設定使用者流程的頁面配置版本，如下一節所述。

![反白顯示啟用 JavaScript 設定的 [使用者流程屬性] 頁面](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>選取頁面配置版本

無論您是否在使用者流程的屬性中啟用 JavaScript，都可以為使用者流程頁面指定頁面配置版本。 開啟使用者流程，然後選取 [**頁面配置**]。 在 [**版面配置名稱**] 底下，選取 [使用者流程] 頁面，然後選擇**頁面配置版本**。

如需不同頁面配置版本的詳細資訊，請參閱[版本變更記錄](page-layout.md#version-change-log)檔。

![入口網站中顯示頁面配置版本下拉式清單的頁面配置設定](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>自訂原則

若要在自訂原則中啟用 JavaScript，請將**ScriptExecution**元素新增至自訂原則檔案中的**RelyingParty**元素。 如需詳細資訊，請參閱[JavaScript 範例以在 Azure Active Directory B2C 中使用](javascript-samples.md)。

無論您是否在自訂原則中啟用 JavaScript，都可以為頁面指定頁面配置版本。 如需指定頁面配置的詳細資訊，請參閱[使用自訂原則在 Azure Active Directory B2C 中選取頁面配置](page-layout.md)。

## <a name="next-steps"></a>後續步驟

如需不同頁面配置版本的詳細資訊，請參閱[使用自訂原則在 Azure Active Directory B2C 中選取頁面配置](page-layout.md#version-change-log)的**版本變更記錄**檔一節。

您可以在 JAVAscript 範例中找到 JavaScript 使用方式的範例[，以用於 Azure Active Directory B2C](javascript-samples.md)。
