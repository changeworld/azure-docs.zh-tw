---
title: Azure AD Connect 雲端布建隨選布建
description: 本文說明隨選布建功能。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637098"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Azure AD Connect 雲端布建隨選布建

Azure AD Connect 雲端布建推出了一項新功能，可讓您藉由將這些變更套用至單一使用者，來測試設定變更。  您可以使用此驗證並確認對設定所做的變更已正確套用，並已正確地同步處理至 Azure AD。  

> [!IMPORTANT] 
> 當您使用隨選布建時，範圍篩選器不會套用至您所選取的使用者。  這表示您可以在您指定的 Ou 以外的使用者上使用隨選布建。


## <a name="using-on-demand-provisioning"></a>使用隨選布建
若要使用新功能，請遵循下列步驟。


1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
2.  選取 [Azure AD Connect]****。
3.  選取 [ **管理**布建]。

    ![管理布建](media/how-to-configure/manage1.png)
4. 在 [設定] **底下，選取您的設定**。
5. 在 [ **驗證** ] 下，按一下 [布建 **使用者** ] 按鈕。 

 ![布建使用者](media/how-to-on-demand-provision/on-demand2.png)

6. 在 [隨選布建] 畫面上。  輸入使用者的 **辨別名稱** ，然後 **按一下 [布** 建] 按鈕。  
 
 ![依需求布建](media/how-to-on-demand-provision/on-demand3.png)
7. 完成之後，您應該會看到成功畫面，以及4個綠色的核取方塊，指出已成功布建。  任何錯誤都會出現在左側。

  ![成功](media/how-to-on-demand-provision/on-demand4.png)

現在您可以查看使用者，並判斷您在設定中所做的變更是否已套用。  本檔的其餘部分將說明在成功同步處理使用者的詳細資料中所顯示的個別區段。

## <a name="import-user-details"></a>匯入使用者詳細資料
本節提供從 Active Directory 匯入之使用者的相關資訊。  這是使用者在將其布建至 Azure AD 之前的外觀。  按一下 [ **視圖詳細資料** ] 連結，顯示此資訊。

![匯入使用者](media/how-to-on-demand-provision/on-demand5.png)

您可以使用此資訊，查看已匯入的各種屬性及其值。  如果您已建立自訂屬性對應，您將可以在這裡看到值。
![匯入使用者詳細資料](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>判斷使用者是否在範圍詳細資料
本節提供匯入 Azure AD 的使用者是否在範圍中的相關資訊。  按一下 [ **視圖詳細資料** ] 連結，顯示此資訊。

![使用者範圍](media/how-to-on-demand-provision/on-demand7.png)

您可以使用這項資訊，查看使用者範圍的其他相關資訊。

![使用者範圍詳細資料](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>比對來源與目標系統詳細資料之間的使用者
本節提供有關使用者是否已存在於 Azure AD 中的資訊，以及是否應該進行聯結，而不是布建新使用者的資訊。  按一下 [ **視圖詳細資料** ] 連結，顯示此資訊。
![檢視詳細資料](media/how-to-on-demand-provision/on-demand8.png)

您可以使用這項資訊，查看是否找到相符項，或是否要建立新的使用者。

![使用者資訊](media/how-to-on-demand-provision/on-demand11.png)

相符的詳細資料會顯示一則訊息，其中包含下列三項作業的其中一項。  其中包括：
- 建立-在 Azure AD 中建立使用者
- 更新-使用者會根據設定中的變更進行更新
- Delete-從 Azure AD 移除使用者。

根據您所執行的作業類型而定，訊息會有所不同。

## <a name="perform-action-details"></a>執行動作詳細資料
本節提供在套用設定之後，布建或匯出至 Azure AD 的使用者相關資訊。  這就是使用者布建到 Azure AD 時的外觀。  按一下 [ **視圖詳細資料** ] 連結，顯示此資訊。
![執行動作詳細資料](media/how-to-on-demand-provision/on-demand9.png)

使用這項資訊，您可以在套用設定之後查看屬性的值。  它們看起來像是匯入的還是不同的？  設定是否適用成功？  

這將可讓您追蹤屬性轉換，因為它會在雲端移動到您的 Azure AD 租使用者中。

![trace 屬性](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>後續步驟 

- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
- [如何安裝 Azure AD Connect 雲端布建](how-to-install.md)
 