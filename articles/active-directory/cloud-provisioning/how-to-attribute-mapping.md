---
title: Azure AD Connect 雲端布建屬性編輯器
description: 本文說明如何使用屬性編輯器。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b7aa0bf8c82990a00b4e41041145a67ee2f02b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637122"
---
# <a name="azure-ad-connect-cloud-provisioning-attibute-mapping"></a>Azure AD Connect 雲端布建屬性對應

Azure AD Connect 雲端布建推出了一項新功能，可讓您輕鬆地對應內部部署使用者/群組物件與 Azure AD 中物件之間的屬性。  這項功能已新增至雲端布建設定。

您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。  如需已同步處理的屬性清單，請參閱 [已同步](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)處理的屬性。

## <a name="understanding-attribute-mapping-types"></a>了解屬性對應類型
有了屬性對應，您就可以控制如何在 Azure AD 中填入屬性。
支援四種不同的對應類型：

- **Direct** –目標屬性會填入 AD 中連結化物件的屬性值。
- **常數** - 目標屬性會填入您所指定的特定字串。
- **運算式** - 目標屬性會根據類似指令碼的運算式結果填入。
  如需詳細資訊，請參閱 [撰寫屬性對應的運算式](reference-expressions.md)。
- **無** - 目標屬性保留未修改。 不過，如果目標屬性是空的，就會填入您所指定的預設值。

除了這四個基本類型以外，自訂屬性對應還支援選擇性的**預設**值指派的概念。 預設值指派可確保當 Azure AD 中和目標物件都沒有值時，目標屬性會填入某個值。 最常見的設定是將其保留空白。

## <a name="understanding-attribute-mapping-properties"></a>了解屬性對應屬性

在上一節中，我們已為您介紹屬性對應類型屬性。
除了這個屬性之外，屬性對應也支援下列屬性：

- **來源屬性** -來源系統中的使用者屬性 (範例： Active Directory) 。
- **目標屬性** –目標系統中的使用者屬性 (範例： Azure Active Directory) 。
- **Null 時的預設值 (選擇性)** - 來源屬性為 Null 時將傳至目標系統的值。 只有在建立使用者時，才會佈建此值。 更新現有的使用者時不會佈建「Null 時的預設值」。  
- **套用此對應**
  - **一律** – 將此對應套用於使用者建立和更新動作。
  - **僅限建立期間** - 僅將此對應套用於使用者建立動作。

> [!NOTE]
> 本檔說明如何使用 Azure 入口網站來對應屬性。  如需使用圖形的詳細資訊，請參閱 [轉換](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>使用屬性對應
若要使用新功能，請遵循下列步驟。

 1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
 2.  選取 [Azure AD Connect]****。
 3.  選取 [ **管理**布建]。

   ![管理布建](media/how-to-configure/manage1.png)
 
 4. 在 [設定] **底下，選取您的設定**。
 5. 選取 [ **按一下以編輯**對應]。  這會開啟 [屬性對應] 畫面。

 ![加入屬性](media/how-to-attribute-mapping/mapping6.png)
 6.  按一下 [ **加入屬性**]。

 ![對應類型](media/how-to-attribute-mapping/mapping1.png)
 
 7. 選取 **對應類型**。  在此範例中，我們使用 Expression。
 8.  在方塊中輸入運算式。  在此範例中，我們使用： `Replace([mail], "@contoso.com", , ,"", ,).`
 9.  輸入目標屬性。  在此範例中，我們使用 ExtensionAttribute15。
 10. 選取套用此的時機，**然後按一下 [** 套用]
   
   ![編輯對應](media/how-to-attribute-mapping/mapping2a.png)
 11. 回到 [屬性對應] 畫面上，您應該會看到新的屬性對應。  
 12. 按一下 [ **儲存架構**]。

 ![儲存結構描述](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>測試您的屬性對應
若要測試您的屬性對應，您可以使用 [隨選布建](how-to-on-demand-provision.md)。  從 

1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
2.  選取 [Azure AD Connect]****。
3.  選取 [ **管理**布建]。
4. 在 [設定] **底下，選取您的設定**。
5. 在 [ **驗證** ] 下，按一下 [布建 **使用者** ] 按鈕。 
6. 在 [隨選布建] 畫面上。  輸入使用者或群組的**分辨名稱**，然後按一下 [布建 **] 按鈕。**  
7. 完成之後，您應該會看到成功畫面，以及4個綠色的核取方塊，指出已成功布建。  
  ![布建成功](media/how-to-attribute-mapping/mapping4.png)
1. 在 [ **執行動作** ] 下，按一下 [ **查看詳細資料**]。  在右側，您應該會看到新的屬性 syncrhonized 和套用的運算式。

  ![執行動作](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
- [撰寫屬性對應的運算式](reference-expressions.md)
- [已同步處理的屬性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)