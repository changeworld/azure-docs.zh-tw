---
title: Azure AD Connect 同步：目錄擴充 | Microsoft Docs
description: 本主題說明 Azure AD Connect 中的目錄擴充功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917908"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步處理：目錄擴充
您可以使用目錄擴充功能，從內部部署 Active Directory 利用自己的屬性擴充 Azure Active Directory (Azure AD) 中的結構描述。 此功能可讓您建置 LOB 應用程式，方法是取用您在內部部署中持續進行管理的屬性。 這些屬性可以通過[擴展使用](https://docs.microsoft.com/graph/extensibility-overview
)。 您可以使用[Microsoft 圖形資源管理器](https://developer.microsoft.com/graph/graph-explorer)查看可用屬性。 您還可以使用此功能在 Azure AD 中創建動態組。

目前沒有任何 Office 365 工作負載取用這些屬性。

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>自訂要與 Azure AD 同步的屬性

您可以在安裝精靈的自訂設定路徑中，設定您想要同步處理的其他屬性。

>[!NOTE]
>[可用屬性] 方塊區分大小寫。

![結構描述擴充功能精靈](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

 安裝會顯示下列屬性，這些都是有效的候選項目：

* 使用者和群組物件類型
* 單一值屬性︰字串、布林值、整數、二進位檔
* 多值屬性︰字串、二進位檔


>[!NOTE]
> 儘管 Azure AD Connect 支援將多重值的 Active Directory 屬性同步處理為 Azure AD 作為多重值的目錄擴充，但是目前沒有辦法可以擷取/取用在多重值目錄擴充屬性中上傳的資料。

屬性清單是從 Azure AD Connect 安裝期間建立的結構描述快取讀取。 如果您已使用其他屬性擴充 Active Directory 結構描述，就必須[重新整理結構描述](how-to-connect-installation-wizard.md#refresh-directory-schema)，才可看見這些新屬性。

Azure AD 中的物件最多可有 100 個目錄擴充功能的屬性。 長度上限是 250 個字元。 如果屬性值較長，同步處理引擎就會加以截斷。

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>嚮導在 Azure AD 中所做的配置更改

在 Azure AD Connect 安裝期間，會註冊可提供這些屬性的應用程式。 您可以在 Azure 入口網站中看到這個應用程式。 它的名字始終是**租戶架構擴展應用程式**。

![結構描述擴充功能應用程式](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

請確保選擇 **"所有應用程式**"以查看此應用程式。

屬性預綴與**擴展\_[應用程式 Id]\_**。 應用程式 Id 對於 Azure AD 租戶中的所有屬性具有相同的值。 在本主題中所有其他方案都需要此值。

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>使用 Microsoft 圖形 API 查看屬性

這些屬性現在可以通過微軟圖形 API 使用 Microsoft[圖形資源管理器](https://developer.microsoft.com/graph/graph-explorer#)。

>[!NOTE]
> 在 Microsoft 圖形 API 中，您需要要求返回屬性。 明確選取如下所示的屬性： `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`.
>
> 如需詳細資訊，請參閱 [Microsoft Graph：使用查詢參數](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)。

## <a name="use-the-attributes-in-dynamic-groups"></a>使用動態組中的屬性

一個更有用的方案是在動態安全或 Office 365 組中使用這些屬性。

1. 在 Azure AD 中創建新組。 給它一個好名字，並確保**成員資格類型**是**動態使用者**。

   ![具有新組的螢幕截圖](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. 選擇以**添加動態查詢**。 如果查看屬性，則看不到這些擴充屬性。 您需要首先添加它們。 按一下 **"獲取自訂擴充屬性**"，輸入應用程式 ID，然後按一下 **"刷新屬性**"。

   ![已添加目錄擴展的螢幕截圖](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. 打開屬性下拉清單，並注意您添加的屬性現在可見。

   ![UI 中顯示新屬性的螢幕截圖](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   完成運算式以滿足您的要求。 在我們的示例中，規則設置為 **（使用者.extension_9d98ed114c4840d298fad781915f27e4_division -eq "銷售和行銷"）。**

4. 創建組後，給 Azure AD 一些時間填充成員，然後查看成員。

   ![動態組中的成員的螢幕截圖](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>後續步驟
深入了解 [Azure AD Connect 同步](how-to-connect-sync-whatis.md) 組態。

深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
