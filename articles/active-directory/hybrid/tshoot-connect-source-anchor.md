---
title: Azure AD Connect：在安裝期間對來源錨點問題進行疑難排解 |Microsoft Docs
description: 本主題提供如何在安裝期間針對來源錨點的問題進行疑難排解的步驟。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: cb82eb2d8e23daec0ddb8856b713c1aa051f25c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85355942"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>在安裝期間針對來源錨點問題進行疑難排解
本文說明安裝期間可能發生的不同來源錨點相關問題，並提供解決這些問題的方法。

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory 中的來源錨點無效

### <a name="custom-installation"></a>自訂安裝

在自訂安裝期間，Azure AD Connect 會從 Azure Active Directory 讀取來源錨點原則。 如果原則存在於 Azure Active Directory 中，除非客戶覆寫，否則 Azure AD Connect 會套用相同的原則。 Wizard 會通知您已讀取的屬性。 此外，如果您嘗試覆寫來源錨點原則，則嚮導會發出警告。

在此讀取作業期間，Azure Active Directory 中的來源錨點原則可能不是預期的。 在此情況下，Azure AD Connect 不知道要使用的來源錨點，而且需要手動覆寫。</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

若要解決此問題，您可以藉由選取特定的屬性，手動覆寫來源錨點。 只有在您確定要選取哪一個屬性時，才能繼續使用此選項。 如果您不確定，請洽詢[Microsoft 支援服務](https://support.microsoft.com/contactus/)以取得指導方針。 如果您變更來源錨點原則，它可能會中斷內部部署使用者與其相關聯的 Azure 資源之間的關聯。</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>快速安裝
在快速安裝期間，Azure AD Connect 會從 Azure Active Directory 讀取來源錨點原則。 如果原則存在於 Azure Active Directory 中，Azure AD Connect 會套用相同的原則。 沒有任何選項可執行手動覆寫。

在此讀取作業期間，Azure Active Directory 中的來源錨點原則可能不是預期的。 在此情況下，Azure AD Connect 不知道來源錨點應該是什麼。</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

若要解決此問題，您必須使用自訂模式重新安裝，並藉由選取特定的屬性來手動覆寫來源錨點。 只有在您確定要選取哪一個屬性時，才能繼續使用此選項。 如果您不確定，請洽詢[Microsoft 支援服務](https://support.microsoft.com/contactus/)以取得指導方針。 如果您變更來源錨點原則，它可能會中斷內部部署使用者與其相關聯的 Azure 資源之間的關聯。

### <a name="invalid-source-anchor-in-sync-engine"></a>同步處理引擎中的來源錨點無效
在安裝期間，可能 Azure AD Connect 嘗試使用不正確來源錨點來設定同步處理引擎。 這項作業很可能是產品問題，而且安裝 Azure AD Connect 將會失敗。 如果您遇到此問題，請洽詢[Microsoft 支援服務](https://support.microsoft.com/contactus/)。</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
