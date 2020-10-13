---
title: Azure AD Connect：在安裝期間針對來源錨點問題進行疑難排解 |Microsoft Docs
description: 本主題提供如何在安裝期間針對來源錨點問題進行疑難排解的步驟。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4e6460015430850c11fbf70a005d7440ce1b92fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91305999"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>在安裝期間針對來源錨點問題進行疑難排解
本文說明安裝期間可能會發生的不同來源錨點相關問題，並提供解決這些問題的方法。

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory 中的來源錨點無效

### <a name="custom-installation"></a>自訂安裝

在自訂安裝期間，Azure AD Connect 從 Azure Active Directory 讀取來源錨點原則。 如果原則存在於 Azure Active Directory 中，除非客戶覆寫該原則，否則 Azure AD Connect 會套用相同的原則。 Wizard 會通知您已讀取的屬性。 此外，如果您嘗試覆寫來源錨點原則，則嚮導會發出警告。

在此讀取作業期間，Azure Active Directory 中的來源錨點原則可能是非預期的。 在此情況下，Azure AD Connect 不知道要使用哪個來源錨點，而且需要手動覆寫。</br>
![顯示要在哪裡手動覆寫來源錨點的螢幕擷取畫面。](media/tshoot-connect-source-anchor/source1.png)

若要解決此問題，您可以選取特定屬性以手動覆寫來源錨點。 只有在您確定要選取哪一個屬性時，才會繼續使用這個選項。 如果您不確定，請洽詢 [Microsoft 支援服務](https://support.microsoft.com/contactus/) 以取得指導方針。 如果您變更來源錨點原則，它可能會中斷內部部署使用者與其相關 Azure 資源之間的關聯。</br>
![顯示覆寫來源錨點之指定屬性的螢幕擷取畫面。](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>快速安裝
在快速安裝期間，Azure AD Connect 從 Azure Active Directory 讀取來源錨點原則。 如果原則存在於 Azure Active Directory 中，Azure AD Connect 會套用相同的原則。 沒有手動覆寫的選項。

在此讀取作業期間，Azure Active Directory 中的來源錨點原則可能是非預期的。 在此情況下，Azure AD Connect 不知道來源錨點應該是什麼。</br>
![顯示 Azure Active Directory 中的來源錨點未預期時，會發生什麼情況的螢幕擷取畫面。](media/tshoot-connect-source-anchor/source3.png)

若要解決此問題，您必須使用自訂模式重新安裝，並選取特定屬性以手動覆寫來源錨點。 只有在您確定要選取哪一個屬性時，才會繼續使用這個選項。 如果您不確定，請洽詢 [Microsoft 支援服務](https://support.microsoft.com/contactus/) 以取得指導方針。 如果您變更來源錨點原則，它可能會中斷內部部署使用者與其相關 Azure 資源之間的關聯。

### <a name="invalid-source-anchor-in-sync-engine"></a>同步處理引擎中的來源錨點無效
在安裝期間，可能 Azure AD Connect 嘗試使用不正確來源錨點設定同步處理引擎。 這項作業很可能是產品問題，而 Azure AD Connect 的安裝將會失敗。 如果您遇到此問題，請洽詢 [Microsoft 支援服務](https://support.microsoft.com/contactus/) 。</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
