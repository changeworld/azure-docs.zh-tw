---
title: 使用 Azure AD Connect Health 搭配 AD FS | Microsoft Docs
description: 這是如何監視內部部署 AD FS 基礎結構的 Azure AD Connect Health 頁面。
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9401532964e984147436f664b366fd613f3f919f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359376"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 監視 AD FS
下列文件適用於使用 Azure AD Connect Health 來監視 AD FS 基礎結構。 如需使用 Azure AD Connect Health 監視 Azure AD Connect （同步）的相關資訊，請參閱[使用 Azure AD Connect Health 進行同步](how-to-connect-health-sync.md)處理。此外，如需使用 Azure AD Connect Health 監視 Active Directory Domain Services 的詳細資訊，請參閱搭配[使用 Azure AD Connect Health 與 AD DS](how-to-connect-health-adds.md)。

## <a name="alerts-for-ad-fs"></a>AD FS 的警示
[Azure AD Connect Health 警示] 區段將為您提供作用中警示的清單。 每個警示都包含相關資訊、解決步驟，以及相關文件的連結。

您可以連按兩下作用中或已解決的警示，以開啟新的刀鋒視窗，其中含有額外資訊、解決警示可以採取的步驟，以及相關文件的連結。 您也可以檢視過去已解決的警示的歷史資料。

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>AD FS 的使用情況分析
Azure AD Connect Health 使用情況分析會分析您的同盟伺服器的驗證流量。 您可連按兩下使用情況分析方塊來開啟使用情況分析刀鋒視窗，以顯示數個度量和群組。

> [!NOTE]
> 若要搭配 AD FS 使用使用情況分析，您必須確定已啟用 AD FS 稽核。 如需詳細資訊，請參閱 [啟用 AD FS 的稽核](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs)。
>
>

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/report1.png)

若要選取其他度量，請指定時間範圍；若要變更群組，請在使用情況分析圖表上按一下滑鼠右鍵，並選取 [編輯圖表]。 接著，您可以指定時間範圍、選取不同的度量，以及變更群組。 您可以根據不同的「度量」檢視驗證流量的分佈，並使用下列各節所述的相關「分組依據」參數來為每個度量分組：

**計量：要求總數** - AD FS 服務所處理的要求總數。

|群組依據 | 分組是什麼意思，為什麼分組很有用？ |
| --- | --- |
| 全部 | 顯示所有 AD FS 伺服器所處理要求總數的計數。|
| Application | 根據目標信賴憑證者，為要求總數分組。 這個分組對於了解哪個應用程式收到多少百分比的總流量非常有幫助。 |
|  伺服器 |根據處理要求的伺服器，為要求總數分組。 這個分組對於了解總流量的負載分佈非常有幫助。
| 加入工作場所 |根據要求是否來自已加入工作場所的裝置 (已知)，為要求總數分組。 這個分組對於了解是否使用識別基礎結構未知的裝置存取您的資源非常有幫助。 |
|  驗證方法 | 根據用於驗證的驗證方法，為要求總數分組。 這個分組對於了解用於驗證的常見驗證方法非常有幫助。 以下是可能的驗證方法 <ol> <li>Windows 整合式驗證 (Windows)</li> <li>表單型驗證 (表單)</li> <li>SSO (單一登入)</li> <li>X509 憑證驗證 (憑證)</li> <br>如果同盟伺服器收到的要求含有 SSO Cookie，該要求就視為 SSO (單一登入)。 在這種情況下，如果 Cookie 有效，則不會要求使用者提供認證，並不間斷地存取應用程式。 如果您有多個同盟伺服器所保護的信賴憑證者，此行為非常常見。 |
| 網路位置 | 根據使用者的網路位置，為要求總數分組。 它可以是內部網路或外部網路。 這個分組對於了解流量百分比是來自內部網路還是外部網路非常有幫助。 |


**度量：失敗要求總數** - 同盟服務所處理的失敗要求總數。 (此度量僅能在適用於 Windows Server 2012 R2 的 AD FS 上使用)

|群組依據 | 分組是什麼意思，為什麼分組很有用？ |
| --- | --- |
| 錯誤類型 | 根據預先定義的錯誤類型，顯示錯誤數目。 這個分組對於了解常見的錯誤類型非常有幫助。 <ul><li>不正確的使用者名稱或密碼：由於不正確的使用者名稱或密碼而導致錯誤。</li> <li>「外部網路鎖定」：由於從外部網路收到已鎖定的使用者送來的要求而導致失敗 </li><li> 「已過期的密碼」：由於使用者以過期密碼登入而導致失敗。</li><li>「已停用的帳戶」：由於使用者以停用的帳戶登入而導致失敗。</li><li>「裝置驗證」：由於使用者無法使用裝置驗證來驗證而導致失敗。</li><li>「使用者憑證驗證」：由於憑證無效，使用者無法通過驗證而導致失敗。</li><li>"MFA"：由於使用者無法使用 Multi-Factor Authentication 通過驗證而導致失敗。</li><li>「其他認證」：「發行授權」：由於授權失敗而導致失敗。</li><li>「發行委派」：由於發行委派錯誤而導致失敗。</li><li>「權杖接受」：由於 ADFS 拒絕來自協力廠商識別提供者的權杖而導致失敗。</li><li>「通訊協定」：由於通訊協定錯誤而導致失敗。</li><li>「未知」：全部攔截。 無法歸入已定義的類別內的其他任何失敗。</li> |
| 伺服器 | 依伺服器為錯誤分組。 此分組對於了解跨伺服器的錯誤分佈非常有幫助。 分佈不平均可能是伺服器處於錯誤狀態的指標。 |
| 網路位置 | 根據要求的網路位置 (內部網路與外部網路)，為錯誤分組。 此分組對於了解失敗的要求類型非常有幫助。 |
|  Application | 根據目標應用程式 (信賴憑證者)，為失敗分組。 此分組對於了解哪個目標應用程式將看到最多錯誤數目非常有幫助。 |

**度量︰使用者計數** - 使用 AD FS 主動驗證的唯一使用者平均數目

|群組依據 | 分組是什麼意思，為什麼分組很有用？ |
| --- | --- |
|全部 |此度量會提供在所選時間配量內，使用同盟服務的使用者平均數目計數。 系統不會為使用者分組。 <br>平均值取決於選取的時間配量。 |
| Application |根據目標應用程式 (信賴憑證者)，為使用者的平均數目分組。 此分組對於了解有多少使用者正在使用哪一個應用程式非常有幫助。 |

## <a name="performance-monitoring-for-ad-fs"></a>AD FS 的效能監視
Azure AD Connect Health 效能監視會提供關於度量的監視資訊。 選取 [監視] 方塊，以開啟內含度量詳細資訊的新刀鋒視窗。

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/perf1.png)

選取刀鋒視窗頂端的 [篩選] 選項，您可以依伺服器篩選以查看個別伺服器的度量。 若要變更度量，請在監視刀鋒視窗底下的監視圖表上按一下滑鼠右鍵，然後選取 [編輯圖表] \(或選取 [編輯圖表] 按鈕)。 在開啟的新刀鋒視窗中，您可以從下拉式清單中選取其他計量，並指定檢視效能資料的時間範圍。

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>使用者名稱/密碼登入失敗的前 50 個使用者
AD FS 伺服器上驗證要求失敗的常見原因之一就是要求所提供的認證無效，也就是錯誤的使用者名稱或密碼。 使用者通常是因為密碼太複雜、忘記密碼或打錯字，才會發生這種情況。

但還是有其他原因會導致 AD FS 伺服器所處理的要求數量超出預期，例如：可快取使用者認證的應用程式和到期的認證，或嘗試以一系列的常見密碼登入帳戶的惡意使用者。 這兩個範例都是可能導致要求激增的正當理由。

Azure AD Connect Health for ADFS 會提供一份報告，內容有關因為使用者名稱或密碼無效而登入嘗試失敗的前 50 個使用者。 處理伺服器陣列中所有 AD FS 伺服器所產生的稽核事件，即可達成此報告。

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/report1a.png)

您可以在這份報告中輕鬆取得下列資訊︰

* 過去 30 天內使用者名稱/密碼錯誤的失敗要求總數
* 每天由於使用者名稱/密碼不正確而登入失敗的平均使用者人數。

按一下此組件即可前往可提供其他詳細資料的主要報告刀鋒視窗。 此刀鋒視窗包含一個提供趨勢資訊的圖形，以便建立有關使用者名稱或密碼錯誤之要求的基準。 此外，還提供過去一週內前 50 個使用者清單及其嘗試失敗次數。 注意過去一週內的前 50 個使用者可能有助於找出不正確密碼爆增情形。  

此圖形可提供以下資訊：

* 每天由於使用者名稱/密碼不正確而登入失敗的總數。
* 每天登入失敗的唯一使用者總數。
* 最後一個要求的用戶端 IP 位址

![Azure AD Connect Health 入口網站](./media/how-to-connect-health-adfs/report3a.png)

此報表會提供下列資訊：

| 報告項目 | Description |
| --- | --- |
| 使用者識別碼 |顯示所使用的使用者識別碼。 這個值是使用者所輸入的內容，在某些情況下是所使用的錯誤使用者識別碼。 |
| 嘗試失敗 |顯示該特定使用者識別碼的嘗試失敗總數。 此資料表是依據最多失敗嘗試次數以遞減順序排序。 |
| 上次失敗 |顯示上次發生失敗時的時間戳記。 |
| 上次失敗 IP |顯示最後一個不正確要求的用戶端 IP 位址。 如果您在此值中看到多個 IP 位址，表示其中可能同時包含轉送用戶端 IP 與使用者上次嘗試的要求 IP。  |

> [!NOTE]
> 此報告會每隔 12 小時以該段時間內收集的新資訊自動進行更新。 因此，報告中不包含過去 12 小時內的登入嘗試。

## <a name="related-links"></a>相關連結
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 代理程式安裝](how-to-connect-health-agent-install.md)
* [有風險的 IP 報告](how-to-connect-health-adfs-risky-ip.md)

