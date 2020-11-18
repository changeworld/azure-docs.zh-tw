---
title: Azure Marketplace 應用程式的單鍵單一登入 (SSO) 設定 | Microsoft Docs
description: 為 Azure Marketplace 中應用程式設定單鍵 SSO 的步驟。
services: active-directory
documentationCenter: na
author: kenwith
manager: celestedg
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe4e981782878ce4f86dd7dcb8ae635a60b2405a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658701"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>單一登入的單鍵應用程式設定

 在本教學課程中，您將了解如何為 Azure Marketplace 中支援 SAML 的 Azure Active Directory (Azure AD) 應用程式執行單鍵單一登入 (SSO) 設定。

## <a name="introduction-to-one-click-sso"></a>單鍵 SSO 簡介

單鍵 SSO 功能旨在針對可支援 SAML 通訊協定的 Azure Marketplace 應用程式設定單一登入。 在 Azure AD SSO 設定頁面上，此選項可讓您自動在應用程式端設定 Azure AD 中繼資料。 如此一來，您就可以以最少的手動作業來快速設定 SSO。

## <a name="advantages-of-one-click-sso"></a>單鍵 SSO 的優點

- 快速完成需要在應用程式端手動設定的 Azure Marketplace 應用程式 SSO 設定。
- 更有效且精確的 SSO 設定。
- 設定時不需要與合作夥伴通訊或請求支援。 應用程式會提供用於設定 SAML 的 UI。

## <a name="prerequisites"></a>必要條件

- 可設定 SSO 的有效應用程式訂用帳戶。 您也需要管理員認證。
- 在瀏覽器中安裝 Microsoft 所提供的 **我的應用程式安全登入擴充功能**。 如需詳細資訊，請參閱[在「我的應用程式」入口網站上存取和使用應用程式](../user-help/my-apps-portal-end-user-access.md)。

## <a name="one-click-sso-configuration-steps"></a>單鍵 SSO 設定步驟

1. 從 Azure Marketplace 新增應用程式。

2. 選取 [單一登入]。

3. 選取 [啟用單一登入]。

4. 在 [基本 SAML 組態] 區段中填入必要的組態值。

    > [!NOTE]
    > 如果應用程式有您需要設定的自訂宣告，請在執行單鍵 SSO 之前加以處理。

5. 如果您的 Azure Marketplace 應用程式可使用單鍵 SSO 功能，您會看到下列畫面。 您可能必須選取 [安裝擴充功能] 來安裝「我的應用程式安全登入瀏覽器擴充功能」。

   ![安裝我的應用程式安全登入瀏覽器擴充功能](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. 將擴充功能新增至瀏覽器之後，請選取 [設定 \<Application Name\>]。 當您重新導向應用程式管理員入口網站後，請以系統管理員身分登入。

   ![設定應用程式名稱](./media/one-click-sso-tutorial/setup-sso.png)

7. 瀏覽器擴充功能會自動在應用程式上設定 SSO。 請選取 [是] 來表示確認。

   ![儲存自動填入的資料](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > 如果您應用程式的 SSO 設定需要額外的步驟，請遵循提示來執行步驟。

8. 設定完成後，請選取 [確定] 來儲存變更。

   ![儲存自動填入的資料](./media/one-click-sso-tutorial/save-data.png)

9. 確認視窗會隨即顯示，讓您知道已成功完成 SSO 設定。

   ![已設定 SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 設定成功之後，您會登出應用程式並返回 Azure 入口網站。

11. 您可以選取 [測試] 來測試單一登入。

## <a name="additional-resources"></a>其他資源

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](../saas-apps/tutorial-list.md)
* [什麼是我的應用程式安全登入瀏覽器擴充功能？](../user-help/my-apps-portal-end-user-access.md)
