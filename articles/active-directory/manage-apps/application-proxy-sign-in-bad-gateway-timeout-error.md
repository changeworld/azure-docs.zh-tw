---
title: App Proxy 應用程式發生「無法存取此公司應用程式」錯誤
description: 如何解決使用 Azure AD 應用程式 Proxy 應用程式時常見的存取問題。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18c3b0397a0ad3e9e368d1a14e20a999ced5545b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88690267"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>使用 Application Proxy 應用程式時發生「無法存取此企業應用程式」錯誤

當 Azure AD 應用程式 Proxy 此應用程式遭遇「無法存取此企業應用程式」錯誤時，本文可協助您對常見問題進行疑難排解。

## <a name="overview"></a>概觀

看見此錯誤時，請在錯誤頁面尋找狀態代碼。 該代碼可能會是下列狀態代碼之一：

- **閘道逾時**：應用程式 Proxy 服務無法連線到連接器。 此錯誤通常表示連接器指派、連接器本身，或連接器相關的網路規則有問題。
- **錯誤的閘道**：連接器無法連線到後端應用程式。 此錯誤表示應用程式的設定不正確。
- **禁止**：使用者未獲授權存取應用程式。 當未將使用者指派至 Azure Active Directory 中的應用程式，或者使用者在後端沒有存取應用程式的權限，便會發生此錯誤。

若要尋找程式碼，請查看錯誤訊息左下方的 [狀態碼] 欄位中的文字。 本頁面底端還有提供其他秘訣。

![範例：閘道逾時錯誤](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

如需如何為這些錯誤的根本原因疑難排解的詳細資訊，以及建議的修正程式詳細資料，請參閱下方對應的章節。

## <a name="gateway-timeout-errors"></a>閘道逾時錯誤

當服務嘗試連線到連接器時，若無法在逾時時間範圍內連線，即發生閘道逾時。 此錯誤通常是因為指派至連接器群組的應用程式沒有運作中的連接器，或連接器所需的某些連接埠並未開啟。

## <a name="bad-gateway-errors"></a>閘道錯誤

不正確的閘道是指連接器無法連線到後端應用程式。 請確定您所發佈的是正確的應用程式。 造成此錯誤的常見過失︰

- 打字錯誤或內部 URL 不正確
- 未發佈應用程式的根目錄。 例如，發佈的是 `http://expenses/reimbursement`，但嘗試存取 `http://expenses`
- Kerberos 限制委派 (KCD) 組態有問題
- 後端應用程式有問題

## <a name="forbidden-errors"></a>禁止錯誤

如果您看到禁止錯誤，則表示使用者尚未指派至該應用程式。 此錯誤可能發生在 Azure Active Directory 或後端應用程式。

若要了解如何將使用者指派至 Azure 中的應用程式，請參閱[組態文件](application-proxy-add-on-premises-application.md#test-the-application)。

如果您確認使用者已指派至 Azure 中的應用程式，請在後端應用程式中檢查使用者組態。 如果您使用的是 Kerberos 限制委派/整合式 Windows 驗證，請參閱 [KCD 疑難排解] 頁面的指導方針。

## <a name="check-the-applications-internal-url"></a>檢查應用程式的內部 URL

第一個快速步驟就是反覆檢查內部 URL 並加以修正，做法是透過 [企業應用程式] 開啟應用程式，然後選取 [應用程式 Proxy] 功能表。 確認內部 URL 與內部網路用於存取應用程式的 URL 相同。

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>確認應用程式已指派至運作中的連接器群組

若要確認應用程式已指派至運作中的連接器群組：

1. 移至 [Azure Active Directory]，然後依序按一下 [企業應用程式]、[所有應用程式]，以開啟入口網站中的應用程式  。 開啟應用程式，然後選取左側功能表中的 [Application Proxy]。
1. 找到 [連接器群組] 欄位。 如果群組中沒有作用中的連接器，您會看到一則警告。 如果您沒有看到任何警告，請繼續確認是否允許所有 [必要的埠](application-proxy-add-on-premises-application.md) 。
1. 如果顯示的是錯誤的連接器群組，請使用下拉式清單選取正確的群組，並確認未再出現任何警告。 如果顯示的是所需的連接器群組，請按一下警告訊息以開啟內含連接器管理資訊的頁面。
1. 此頁面提供數種方法可進一步向下切入：

   - 將作用中的連接器移至群組中：如果您有應該屬於群組的作用中連接器，而且能直接看到目標後端應用程式，可以將連接器移入指派的群組。 若要這麼做，請按一下連接器。 在 [連接器群組] 欄位中，使用下拉式清單選取正確的群組，然後按一下 [儲存]。
   - 為該群組下載新的連接器：此頁面上有連結可讓您[下載新連接器](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)。 在可以直接檢視後端應用程式的機器上安裝連接器。 通常連接器和應用程式都安裝在相同的伺服器上。 使用下載連接器連結，將連接器下載到目標電腦上。 接著，按一下連接器，然後使用 [連接器群組] 下拉式清單來確定它屬於正確的群組。
   - 調查非作用中的連接器：如果連接器會顯示為非作用中，則表示其無法連線到服務。 此錯誤通常肇因於某些必要的連接埠遭到封鎖。 若要解決此問題，請進一步確認已允許所有必要的連接埠。

在使用這些步驟確認應用程式已指派至有運作中連接器的群組後，請再次測試應用程式。 如果仍然無法運作，繼續進行下一節。

## <a name="check-all-required-ports-are-open"></a>檢查所有必要的連接埠都已開啟

確認所有必要的埠皆已開啟。 如需必要的埠，請參閱教學課程 [：新增內部部署應用程式以透過應用程式 Proxy 遠端存取 Azure Active Directory 中](application-proxy-add-on-premises-application.md)的「開啟埠」一節。 如果所有必要連接埠皆已開啟，請移至下一節。

## <a name="check-for-other-connector-errors"></a>檢查有無其他連接器錯誤

如果上述方法都無法解決此問題，下一步就是找出連接器本身的問題或錯誤。 您可以在[疑難排解文件](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)中看見一些常見錯誤。

您也可以直接查看連接器記錄，以找出任何錯誤。 許多錯誤訊息會提供具體的程式修正建議。 若要檢視記錄，請參閱[連接器文件](application-proxy-connectors.md#under-the-hood)。

## <a name="additional-resolutions"></a>其他解決方式

如果上述程式未修正問題，則有幾個不同的可能原因。 若要找出問題︰

如果您的應用程式是設定為使用整合式 Windows 驗證 (IWA)，請在未單一登入的情況下測試應用程式。 如果不是，請移至下一段。 若要在未單一登入的情況下檢查應用程式，請透過 [企業應用程式] 開啟您的應用程式，然後移至 [單一登入] 功能表 。 將下拉式清單從「整合式 Windows 驗證」變更為「已停用單一登入」 Azure AD。

現在開啟瀏覽器，然後再次嘗試存取應用程式。 系統應該會提示您輸入驗證並進入應用程式。 如果您可以完成驗證，則問題出在啟用單一登入的 Kerberos 限制委派 (KCD) 組態。 如需詳細資訊，請參閱 KCD 疑難排解頁面。

如果您繼續看到此錯誤，請移到已安裝連接器的電腦，開啟瀏覽器，並嘗試連線到應用程式使用的內部 URL。 連接器就像同一台電腦中的另一個用戶端。 如果您無法連線到應用程式，請調查電腦無法連線到應用程式的原因，或在能夠存取應用程式的伺服器上使用連接器。

如果您可以從電腦連線到應用程式，請找出連接器本身的問題或錯誤。 您可以在[疑難排解文件](application-proxy-troubleshoot.md#connector-errors)中看見一些常見錯誤。 您也可以直接查看連接器記錄，以找出任何錯誤。 我們的錯誤訊息中有許多能夠分享更具體的修正程式建議。 若要了解如何檢視記錄，請參閱[連接器文件](application-proxy-connectors.md#under-the-hood)。

## <a name="next-steps"></a>後續步驟

[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)
