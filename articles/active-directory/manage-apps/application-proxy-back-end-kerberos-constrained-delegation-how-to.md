---
title: 針對 Kerberos 限制委派進行疑難排解-應用程式 Proxy
description: 針對應用程式 Proxy 的 Kerberos 限制委派設定進行疑難排解
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
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d086d816be17699989aafda144493d80837188b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760434"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>針對應用程式 Proxy 的 Kerberos 限制委派設定進行疑難排解

可用來達成對所發佈應用程式進行 SSO 的方法，可能會因應用程式而有所不同。 Azure Active Directory (Azure AD) 應用程式 Proxy 預設提供的一個選項是 Kerberos 限制委派 (KCD)。 您可以為使用者設定一個連接器，以對後端應用程式執行受限制的 Kerberos 驗證。

啟用 KCD 的程序相當簡單。 只需對支援 SSO 的各種元件和驗證流程有一般的了解即可。 但有時 KCD SSO 不會如預期般運作。 您需要有良好的資訊來源，以針對這些情況進行疑難排解。

本文提供單一的參考點，可協助針對一些最常見的問題進行疑難排解和自我修復。 本文也涵蓋較複雜實作問題的診斷。

本文會做出以下假設：

- 根據[開始使用應用程式 Proxy](application-proxy-add-on-premises-application.md) 進行的 Azure AD 應用程式 Proxy 部署及對非 KCD 應用程式的一般存取都如預期般運作。
- 所發佈的目標應用程式是以 Internet Information Services (IIS) 和 Microsoft 的 Kerberos 實作為基礎。
- 伺服器和應用程式主機位於單一 Azure Active Directory 網域中。 如需有關跨網域和樹系案例的詳細資訊，請參閱 [KCD 白皮書](https://aka.ms/KCDPaper) \(英文\)。
- 對象應用程式會在已啟用預先驗證功能的 Azure 租用戶中發佈。 使用者應該透過表單型驗證向 Azure 進行驗證。 本文未涵蓋豐富型用戶端驗證案例。 未來可能會新增這些案例。

## <a name="prerequisites"></a>必要條件

Azure AD 應用程式 Proxy 可以部署到許多類型的基礎結構或環境。 架構會隨組織而有所不同。 KCD 相關問題的最常見原因不是環境。 大多數問題都是簡單的設定錯誤或一般錯誤所造成。

基於這個理由，最好在開始進行疑難排解之前，先確定您已符合[搭配應用程式 Proxy 使用 KCD SSO](application-proxy-configure-single-sign-on-with-kcd.md) 中的所有先決條件。 請注意有關在 2012R2 上設定 Kerberos 限制委派的一節。 此程序會採用不同的方法在舊版 Windows 上設定 KCD。 此外，也請留意下列考量：

- 網域成員伺服器開啟與特定網域控制站 (DC) 的安全通道對話並非罕見的情況。 接著，伺服器可能會在任何指定時間移至另一個對話。 因此，連接器主機並不受限於只能與特定本機網站 DC 進行通訊。
- 跨網域案例有賴於將連接器主機導向至 DC 的轉介，而這些 DC 可能位於本機網路周邊之外。 在這些情況下，將流量也向前傳送至代表其他個別網域的 DC 也一樣重要。 如果不這麼做，委派就會失敗。
- 請儘可能避免在連接器主機與 DC 之間放置任何作用中的 IPS 或 IDS 裝置。 這些裝置有時候會過度侵入，並干擾核心 RPC 流量。

請在簡單的案例中測試委派。 您所引進的變數越多，可能要應用的問題就越多。 為了節省時間，請將您的測試限制在單一連接器。 請在問題解決後，再新增額外的連接器。

有些環境因素也可能造成問題。 若要避免這些因素，在測試時，請儘可能將架構簡化。 例如，內部防火牆 ACL 設定錯誤便相當常見。 如果可能，請將來自連接器的所有流量都直接傳送到 DC 和後端應用程式。

放置連接器的最佳位置是讓它盡可能接近其目標。 在測試時如果有內嵌的防火牆，會增加不必要的複雜性，而可能會導致您的調查時間變長。

什麼會顯現 KCD 問題？ 有數個代表 KCD SSO 失敗的常見指示。 最初的問題跡象會出現在瀏覽器。

![範例：不正確的 KCD 設定錯誤](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![範例：因為缺少許可權而導致授權失敗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

這兩個影像都顯示相同的徵狀：SSO 失敗。 使用者對應用程式的存取遭到拒絕。

## <a name="troubleshooting"></a>疑難排解

您將根據問題和所觀察到的徵狀進行疑難排解。 在您更進一步處理之前，請先瀏覽下列文章。 這些文章提供實用的疑難排解資訊：

- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)
- [Kerberos 錯誤與徵狀](application-proxy-troubleshoot.md#kerberos-errors)
- [在內部部署和雲端身分識別不相同時使用 SSO](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

如果您到此地步，即表示存在著主要問題。 若要開始，請將流程分成下列三個您可以進行疑難排解的階段。

### <a name="client-pre-authentication"></a>用戶端預先驗證

外部使用者透過瀏覽器向 Azure 進行驗證。 KCD SSO 必須要能夠向 Azure 進行預先驗證，才能運作。 如有任何問題，請針對這項能力進行測試並解決。 預先驗證階段與 KCD 或已發佈的應用程式無關。 透過例行性檢查來確認對象帳戶是否存在於 Azure 中，即可輕鬆修正任何不一致的情況。 此外，也請檢查該帳戶是否被停用或封鎖。 瀏覽器中的錯誤回應即已提供足夠的描述來說明原因。 如果您不確定，請查看其他 Microsoft 疑難排解文章來進行確認。

### <a name="delegation-service"></a>委派服務

從 Kerberos 金鑰發佈中心 (KCD) 為使用者取得 Kerberos 服務票證的 Azure Proxy 連接器。

用戶端與 Azure 前端之間的外部通訊與 KCD 沒有關係。 這些通訊只是用來確保 KCD 能夠運作。 Azure Proxy 服務會獲得用來取得 Kerberos 票證的有效使用者識別碼。 如果沒有這個識別碼，就無法使用 KCD 並且會失敗。

如先前所述，瀏覽器錯誤訊息會提供有關失敗原因的一些良好線索。 請務必記下回應中的活動識別碼和時間戳記。 此資訊將可協助您將行為與 Azure Proxy 事件記錄檔中的實際事件相互關聯。

![範例：不正確的 KCD 設定錯誤](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

在事件記錄檔中看到的對應項目會顯示為事件 13019 或 12027。 您可以在 [應用程式及服務記錄]** [Microsoft]** &gt; ** [AadApplicationProxy]** &gt; ** [連接器]** &gt; ** [管理]** &gt; **** 中，找到連接器事件記錄。

![應用程式 Proxy 事件記錄檔中的事件 13019](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![應用程式 Proxy 事件記錄檔中的事件 12027](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. 在您的內部 DNS 中針對應用程式位址使用 **A** 記錄，而非 **CName**。
1. 重新確認連接器主機已獲授與權限，可委派給所指定目標帳戶的 SPN。 重新確認已選取 [使用任何驗證通訊協定]****。 如需詳細資訊，請參閱 [SSO 設定文章](application-proxy-configure-single-sign-on-with-kcd.md)。
1. 確認 Azure AD 中只有一個 SPN 執行個體存在。 從命令提示字元對任何網域成員主機發出 `setspn -x`。
1. 確認已強制執行可限制[簽發的 Kerberos 權杖大小上限](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)的網域原則。 此原則會在發現權杖太大時，阻止連接器取得權杖。

下一個可取得問題相關較細節詳細資料的最佳步驟，就是會擷取連接器主機與網域 KDC 間交換的網路追蹤。 如需詳細資訊，請參閱[深入探討疑難排解文件](https://aka.ms/proxytshootpaper) \(英文\)。

如果票證功能看起來沒問題，您就會在記錄中看到陳述驗證失敗原因的事件，說明失敗原因是應用程式傳回 401。 此事件指出目標應用程式已拒絕您的票證。 前往下一個階段。

### <a name="target-application"></a>目標應用程式

連接器所提供 Kerberos 票證的取用者。 在這個階段，連接器應該已經將 Kerberos 服務票證傳送給後端。 此票證是第一個應用程式要求中的標頭。

1. 藉由使用入口網站中所定義的應用程式內部 URL，確認可從連接器主機上的瀏覽器直接存取應用程式。 然後您就可以成功登入。 您可以在連接器的 [疑難排解]**** 頁面上找到詳細資料。
1. 在仍位於連接器主機上的情況下，確認瀏覽器與應用程式之間的驗證使用 Kerberos。 請採取下列其中一個動作：
1. 在 Internet Explorer 中執行 DevTools (**F12**)，或從連接器主機使用 [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/)。 使用內部 URL 來移至應用程式。 檢查在應用程式回應中傳回的所提供 WWW 授權標頭，以確保有 negotiate 或 Kerberos 存在。

   - 下一個在瀏覽器回應中傳回給應用程式的 Kerberos Blob 會以 **YII** 作為開頭。 這些字母可讓您知道 Kerberos 正在執行。 另一方面，Microsoft NT LAN Manager (NTLM) 則一律會以 **TlRMTVNTUAAB** 作為開頭，它會在從 Base64 解碼的情況下讀取「NTLM 安全性支援提供者」(NTLMSSP)。 如果您在 Blob 的開頭看到 **TlRMTVNTUAAB**，即表示無法使用 Kerberos。 如果沒有看到 **TlRMTVNTUAAB**，則表示可能可以使用 Kerberos。

      > [!NOTE]
      > 如果您使用 Fiddler，此方法會要求您在 IIS 中將應用程式設定上的延伸保護暫時停用。

      ![瀏覽器網路檢查視窗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - 此圖中的 Blob 不是以 **TIRMTVNTUAAB** 作為開頭。 因此，在此範例中可以使用 Kerberos，且 Kerberos Blob 不是以 **YII** 作為開頭。

1. 暫時從 IIS 網站上的提供者清單移除 NTLM。 直接從連接器主機上的 Internet Explorer 存取應用程式。 NTLM 已經不在提供者清單中。 您只能使用 Kerberos 來存取應用程式。 如果存取失敗，表示應用程式的設定可能有問題。 Kerberos 驗證未正常運作。

   - 如果無法使用 Kerberos，請檢查 IIS 中的應用程式驗證設定。 請確定 [Negotiate]**** 列在最上方，而 NTLM 緊接在其下方。 如果您看到 [非 Negotiate]****、[Kerberos 或 Negotiate]**** 或 [PKU2U]****，則請只在 Kerberos 可正常運作的情況下，才繼續操作。

     ![Windows 驗證提供者](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - 在 Kerberos 和 NTLM 位於適當位置之後，在入口網站中暫時停用應用程式的預先驗證。 請嘗試使用外部 URL 從網際網路存取它。 系統會提示您進行驗證。 您可以使用上一個步驟中的相同帳戶來進行驗證。 如果不行，即表示問題出在後端應用程式，而非 KCD。
   - 在入口網站中重新啟用預先驗證。 請嘗試經由應用程式的外部 URL 來連線到應用程式，以透過 Azure 進行驗證。 如果 SSO 失敗，您就會在瀏覽器中看到禁止錯誤訊息，並在記錄檔中看到事件 13022：

     「因為後端伺服器以 HTTP 401 錯誤回應 Kerberos 驗證嘗試，Microsoft AAD 應用程式 Proxy 連接器便無法驗證使用者。」**

      ![顯示 HTTP 401 禁止錯誤](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - 檢查 IIS 應用程式。 請確定所設定的應用程式集區和 SPN 已設定為使用 Azure AD 中的相同帳戶。 依下圖所示的方式在 IIS 中瀏覽：

      ![IIS 應用程式組態視窗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      在知道身分識別之後，請確定此帳戶已搭配所提及的 SPN 進行設定。 例如 `setspn –q http/spn.wacketywack.com`。 在命令提示字元中輸入下列文字︰

      ![顯示 SetSPN 命令視窗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - 檢查入口網站中針對應用程式的設定定義的 SPN。 針對目標 Azure AD 帳戶所設定的 SPN，請確定應用程式的應用程式集區使用相同的 SPN。

      ![Azure 入口網站中的 SPN 設定](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - 移至 IIS 並選取應用程式的 [設定編輯器]**** 選項。 瀏覽至 **system.webServer/security/authentication/windowsAuthentication**。 確定 **UseAppPoolCredentials** 值為 **True**。

      ![IIS 設定應用程式集區認證選項](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      將此值變更為 **True**。 請藉由執行下列命令，從後端伺服器中移除所有已快取的 Kerberos 票證：

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

如需詳細資訊，請參閱[清除所有工作階段的 Kerberos 用戶端票證快取](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)。

如果將 Kernel 模式保持啟用，將可改善 Kerberos 作業的效能。 但這也會造成要藉由使用電腦帳戶將所要求服務的票證解密。 這個帳戶也稱為本機系統。 當應用程式裝載在伺服器陣列中的多部伺服器上時，請將此值設定為 **True** 來中斷 KCD。

- 作為額外的檢查，請一併停用 [擴充]**** 保護。 在某些情況下，當已在特定設定中啟用 KCD 時，[擴充]**** 保護會中斷 KCD。 在這些情況下，應用程式是發佈成預設網站的子資料夾。 此應用程式僅設定為適用於匿名驗證。 所有對話方塊都會變成灰色，這意謂著子物件不會繼承任何作用中設定。 建議您儘可能進行測試，但請記得將此值還原成 [已啟用]****。

  這項額外檢查可讓您回歸正軌來使用已發佈的應用程式。 您可以運轉其他也已設定要委派的連接器。 如需詳細資訊，請閱讀更深入的技術逐步解說：[針對 Azure AD 應用程式 Proxy 進行疑難排解](https://aka.ms/proxytshootpaper) \(英文\)。

如果您仍然無法有所進展，Microsoft 支援服務將可提供協助。 請直接在入口網站內建立支援票證。 工程師將會與您連絡。

## <a name="other-scenarios"></a>其他案例

- Azure 應用程式 Proxy 會在將要求傳送至應用程式之前要求 Kerberos 票證。 有些協力廠商應用程式不喜歡這種驗證方法。 這些應用程式預期發生的是更傳統的交涉。 第一個要求是匿名要求，這可讓應用程式透過 401 回應其支援的驗證類型。
- 雙躍點驗證通常適用於具有後端與前端且兩者都需要驗證的分層應用程式，例如 SQL Reporting Services。 若要設定多重躍點案例，請參閱支援文章： [Kerberos 限制委派在多重躍點案例中可能需要通訊協定轉換](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul)。

## <a name="next-steps"></a>後續步驟

[在受控網域上建立 KCD](../../active-directory-domain-services/deploy-kcd.md)。
