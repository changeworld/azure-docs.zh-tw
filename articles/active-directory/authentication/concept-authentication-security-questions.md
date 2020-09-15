---
title: 安全性問題驗證方法-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中使用安全性問題，以協助改善和保護登入事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bf90909cdd3bd3013dc43d50a9589945f5215c7
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532613"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Azure Active Directory 中的驗證方法-安全性問題

在登入事件期間，安全性問題不會作為驗證方法。 您反而可在自助式密碼重設 (SSPR) 程序期間，使用安全性問題來確認您的身分。 系統管理員帳戶無法使用安全性問題作為搭配 SSPR 運作的驗證方法。

當使用者註冊 SSPR 時，系統會提示他們選擇要使用的驗證方法。 如果選擇使用安全性問題，他們會從一組提示問題中挑選，然後提供自己的答案。

![Azure 入口網站的螢幕擷取畫面，其中顯示安全性問題的驗證方法和選項](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。 系統管理員無法讀取或修改使用者問題或答案。

安全性問題可能會比其他方法更不安全，因為有些人可能會知道其他使用者問題的答案。 如果您使用安全性問題搭配 SSPR，建議結合另一個方法共同使用。 系統會提示使用者使用 Microsoft Authenticator 應用程式或電話驗證，在 SSPR 過程中驗證其身分識別，且只有在他們沒有電話或已註冊的裝置時，才選擇安全性問題。

## <a name="predefined-questions"></a>預先定義的問題

下列預先定義的安全性問題可作為搭配 SSPR 使用的驗證方法。 所有這些安全性問題都會根據使用者的瀏覽器地區設定，轉譯和當地語系化成一組完整的 Microsoft 365 語言：

* 您在哪個城市遇見第一個配偶/伴侶？
* 您的父母在哪個城市相遇？
* 您最親近的手足住在哪個城市？
* 您的父親在哪個城市出生？
* 您的第一份工作是在哪個城市？
* 您的母親在哪個城市出生？
* 您在哪個城市渡過 2000 年的新年？
* 您最喜愛的高中老師姓什麼？
* 您已申請但未就讀的大專名稱為何？
* 您舉辦第一次婚宴的地點名稱為何？
* 您父親的中間名是什麼？
* 您最愛的食物是什麼？
* 您外婆的姓名為何？
* 您母親的中間名是什麼？
* 您最年長手足的生日月份和年度為何？ (例如，1985 年 11 月)
* 您最年長手足的中間名是什麼？
* 您祖父的姓名為何？
* 您最年幼手足的中間名稱是什麼？
* 您六年級時就讀哪間學校？
* 您童年最要好朋友的姓名為何？
* 您第一個密友的姓名為何？
* 您最喜愛的小學老師姓什麼？
* 第一輛汽車或機車的製造商和型號為何？
* 您就讀的第一所學校名稱為何？
* 您出生的醫院名稱為何？
* 您兒時第一個家的街道名稱為何？
* 您的童年英雄名稱為何？
* 您最喜愛的娃娃名稱為何？
* 您第一隻寵物的名稱為何？
* 您童年時期的綽號是什麼？
* 您中學最喜愛的運動是什麼？
* 您的第一份工作是什麼？
* 您兒時電話號碼的末四碼是什麼？
* 在您年輕時，您長大想做什麼？
* 您遇過最有名的人物是誰？

## <a name="custom-security-questions"></a>自訂安全性問題

如需更多彈性，您可定義自己的自訂安全性問題。 自訂安全性問題的長度上限為 200 個字元。

自訂安全性問題不會像預設安全性問題一樣自動當地語系化。 所有自訂問題會以您在系統管理使用者介面中輸入它們的語言顯示，即使使用者的瀏覽器地區設定不同。 如果您需要已當地語系化的問題，請使用預先定義的問題。

## <a name="security-question-requirements"></a>安全性問題需求

預設和自訂安全性問題適用下列需求和限制：

* 答案字元限制下限為三個字元。
* 答案字元限制上限為 40 個字元。
* 使用者不能回答相同的問題一次以上。
* 使用者不能對一個以上的問題提供相同的答案。
* 可以使用任何字元集來定義問題和答案 (包括 Unicode 字元)。
* 定義的問題數目必須大於或等於註冊所需的問題數目。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱 [自助式密碼重設 (SSPR) 的教學 ][tutorial-sspr]課程。

若要深入了解 SSPR 概念，請參閱 [Azure AD 自助式密碼重設的運作方式][concept-sspr]。

深入瞭解如何使用 [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)來設定驗證方法。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
