---
title: 內部部署 Azure AD 密碼保護常見問題集
description: 查看內部部署 Active Directory Domain Services 環境中 Azure AD 密碼保護的常見問題
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d67dbc0eedba8cc32c188636032d96b31f45adf
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717773"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD 內部部署的密碼保護常見問題

本節提供有關 Azure AD 密碼保護的許多常見問題的解答。

## <a name="general-questions"></a>一般問題

**問：使用者要如何選取安全密碼，應提供哪些指導方針？**

下列連結是目前 Microsoft 中有關本主題的指引：

[Microsoft 密碼指引](https://www.microsoft.com/research/publication/password-guidance)

**問：非公用雲端是否支援內部部署 Azure AD 密碼保護？**

否 - 僅公用雲端支援內部部署 Azure AD 密碼保護。 尚未宣告非公用雲端可支援的日期。

Azure AD 入口網站可讓您修改內部部署特定的「Windows Server Active Directory 的密碼保護」設定，即使在非公用雲端中也一樣。這類變更將會保存，但永遠不會生效。 使用非公用雲端認證時，不支援註冊內部部署 proxy 代理程式或樹系，且任何這類的註冊嘗試一律會失敗。

**問：如何將 Azure AD 的密碼保護權益套用至我的內部部署使用者的子集？**

不支援。 部署並啟用之後，Azure AD 密碼保護會一視同仁，所有使用者都會受惠於相同的安全性優點。

**問：密碼變更和密碼設定 (或重設) 之間有何差異？**

密碼變更是使用者在證明您知道舊密碼之後，選擇新密碼的變更。 例如，密碼變更是當使用者登入 Windows 時，系統會提示您選擇新密碼的情況。

密碼設定 (有時稱為密碼重設) 是當系統管理員以新密碼取代帳戶的密碼時，例如使用 Active Directory 消費者和電腦管理工具。 這項作業需要高層級的許可權 (通常是網域系統管理員) ，而執行此作業的人員通常不知道舊密碼。 技術支援人員通常會執行密碼設定，例如，當您協助使用者忘記其密碼時。 當您第一次使用密碼建立全新的使用者帳戶時，您也會看到密碼設定事件。

無論密碼變更或設定是否已完成，密碼驗證原則的行為都相同。 Azure AD 密碼保護 DC 代理程式服務會記錄不同的事件，以通知您密碼變更或設定作業是否已完成。  請參閱 [Azure AD 密碼保護監視和記錄](./howto-password-ban-bad-on-premises-monitor.md)。

**問：當您嘗試使用 Active Directory 消費者和電腦管理嵌入式管理單元來設定弱式密碼時，為什麼會記錄重複的密碼拒絕事件？**

Active Directory 消費者和電腦管理嵌入式管理單元會先嘗試使用 Kerberos 通訊協定設定新密碼。 失敗時，嵌入式管理單元會使用舊版 (SAM RPC) 通訊協定進行第二次嘗試設定密碼 (不重要) 所使用的特定通訊協定。 如果 Azure AD 密碼保護將新密碼視為弱密碼，此嵌入式管理單元的行為會導致記錄兩組密碼重設拒絕事件。

**問：為什麼會以空白的使用者名稱記錄 Azure AD 密碼保護密碼驗證事件？**

Active Directory 支援測試密碼的能力，以查看它是否符合網域目前的密碼複雜性需求，例如使用 [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api。 以這種方式驗證密碼時，測試也包含以密碼篩選器為基礎的產品（例如 Azure AD 密碼保護）進行驗證，但傳遞給指定之密碼篩選 dll 的使用者名稱將會是空的。 在此案例中，Azure AD 密碼保護仍會使用目前作用中的密碼原則來驗證密碼，並且會發出事件記錄檔訊息來取得結果，但事件記錄檔訊息將會有空白的使用者名稱欄位。

**問：是否支援與其他密碼篩選器型產品並存安裝 Azure AD 密碼保護？**

是。 核心 Windows 功能之一就是支援多個已註冊的密碼篩選 dll，因此並不限於 Azure AD 密碼保護。 所有註冊的密碼篩選 dll 必須先同意才能接受密碼。

**問：如何在不使用 Azure 的情況下，于 Active Directory 環境中部署和設定 Azure AD 密碼保護？**

不支援。 Azure AD 密碼保護是 Azure 功能，可延伸至內部部署 Active Directory 環境。

**問：如何在 Active Directory 層級修改原則的內容？**

不支援。 您只能使用 Azure AD 入口網站來管理原則。 另請參閱上一個問題。

**問：為什麼 sysvol 複寫需要 DFSR？**

FRS (DFSR 之前的技術) 有許多已知問題，而且在更新版本的 Windows Server Active Directory 中完全不支援。 在 FRS 設定的網域上，完全不會執行任何 Azure AD 密碼保護測試。

如需詳細資訊，請參閱下列文章：

[將 sysvol 複寫移轉至 DFSR 的案例](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr) \(英文\)

[FRS 終章將至](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) \(英文\)

如果您的網域尚未使用 DFSR，您必須先將它遷移為使用 DFSR，才能安裝 Azure AD 密碼保護。 如需詳細資訊，請參閱下列連結：

[SYSVOL 複寫遷移指南： FRS 至 DFS 複寫](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD 的密碼保護 DC 代理程式軟體目前會安裝在仍使用 FRS 進行 sysvol 複寫的網域中的網域控制站上，但軟體在此環境中將無法正常運作。 其他負面副作用包括無法複寫的個別檔案，而且 sysvol 還原程式似乎成功，但無法以無訊息方式複寫所有檔案。 您應該儘快遷移網域以使用 DFSR，這兩者適用于 DFSR 的固有優點，同時也會將 Azure AD 密碼保護的部署解除封鎖。 軟體的未來版本將會在以仍在使用 FRS 的網域中執行時自動停用。

**問：功能在網域 sysvol 共用上需要多少磁碟空間？**

精確的空間使用量會因為一些因素而有所不同，例如 Microsoft 全域禁用清單和每個租用戶自訂清單中禁用的權杖數量和長度，以及加密的額外負荷。 這些清單的內容很可能在未來繼續增加。 有鑑於此，我們合理預期此功能在網域 sysvol 共用上至少需要五 (5) MB 的空間。

**問：為什麼需要重新開機才能安裝或升級 DC 代理程式軟體？**

這項需求是核心 Windows 行為所造成。

**問：是否有任何方法可將 DC 代理程式設定為使用特定 Proxy 伺服器？**

否。 由於 Proxy 伺服器是無狀態的，因此使用哪一個特定 Proxy 伺服器並不重要。

**問：是否可以與其他服務（如 Azure AD Connect）並存部署 Azure AD 的密碼保護 Proxy 服務？**

是。 Azure AD 密碼保護 Proxy 服務與 Azure AD Connect 之間永遠不會產生直接衝突。

可惜的是，在 Azure AD 密碼保護 Proxy 軟體所安裝的 Microsoft Azure AD Connect 代理程式更新程式服務版本與 [Azure Active Directory 應用程式 Proxy](../manage-apps/application-proxy.md) software 所安裝的服務版本之間，發現不相容。 這項不相容可能會導致代理程式更新程式服務無法與 Azure 連線，以進行軟體更新。 不建議您在同一部電腦上安裝 Azure AD 的密碼保護 Proxy 和 Azure Active Directory 應用程式 Proxy。

**問： DC 代理程式和 proxy 應該在何種順序中安裝和註冊？**

支援任何順序的 Proxy 代理程式安裝、DC 代理程式安裝、樹系註冊，以及 Proxy 註冊。

**問：我是否應該在意網域控制站上的效能點擊，才能部署此功能？**

在狀況良好的現有 Active Directory 部署中，Azure AD 密碼保護 DC 代理程式服務應不會大幅影響網域控制站效能。

對大部分的 Active Directory 部署而言，任何指定網域控制站上的密碼變更作業都是整體工作負載的一小部分。 例如，假設某個 Active Directory 網域有 10000 個使用者帳戶，而 MaxPasswordAge 原則設為 30 天。 平均而言，此網域每天會看到 10000/30=~333 個密碼變更作業，甚至對單一網域控制站而言，這都是很小的作業量。 考慮到可能最糟糕的情況：假設單一 DC 上的這 ~333 個密碼變更作業需要在一小時左右完成。 例如，許多員工都在星期一早上進行工作時，就可能會發生此情況。 即使在該情況下，我們仍然可看到每分鐘有 ~333/60 分鐘 = 6 個密碼變更作業，這依然不是龐大的負載。

不過，如果您目前的網域控制站已在效能受限的層級上執行 (例如 CPU、磁碟空間和磁碟 I/O 等已達到極限)，建議您先新增其他網域控制站或擴展可用磁碟空間，然後再部署這項功能。 另請參閱上面有關 Sysvol 磁碟空間使用量的問題。

**問：我想要在網域中的少數 Dc 上測試 Azure AD 密碼保護。是否可以強制使用者密碼變更使用這些特定的 Dc？**

否。 使用者變更其密碼時，Windows 用戶端 OS 會控制要使用哪一個網域控制站。 網域控制站會根據各種因素而選取，例如 Active Directory 網站和子網指派、環境特定網路設定等。Azure AD 密碼保護無法控制這些因素，也無法影響選取哪些網域控制站來變更使用者的密碼。

可部分達成此目標的方法之一是，在指定 Active Directory 站台中的所有網域控制站上部署 Azure AD 密碼保護。 此方法合理地將指派至該站台的 Windows 用戶端涵蓋在一個範圍內，因此也涵蓋登入這些用戶端並變更其密碼的使用者。

**問：如果我只在網域主控站 (PDC) 上安裝 Azure AD 的密碼保護 DC 代理程式服務，網域中的所有其他網域控制站也會受到保護嗎？**

否。 當非 PDC 網域控制站上的使用者密碼變更時，純文字密碼永遠不會傳送到 PDC (這是常見的錯誤觀念)。 當指定 DC 接受新密碼後，此 DC 會使用該密碼來建立該密碼的各種驗證通訊協定特有雜湊，然後在目錄中保存這些雜湊。 純文字密碼不會保存。 已更新的雜湊接著會複寫到 PDC。 在某些情況下，使用者密碼可能會直接在 PDC 上變更，這也是取決於各種因素，例如網路拓樸和 Active Directory 站台的設計。 (請參閱上一個問題。)

總之，在 PDC 上部署 Azure AD 密碼保護 DC 代理程式服務時，就必須達到此功能在網域間的 100% 安全性涵蓋範圍。 只在 PDC 上部署此功能並不會使網域中其他 DC 享有 Azure AD 密碼保護安全性的優勢。

**問：為什麼即使在內部部署 Active Directory 環境中安裝代理程式之後，自訂智慧鎖定仍無法運作？**

只有 Azure AD 才支援自訂智慧鎖定。 在 Azure AD 入口網站中對自訂智慧鎖定設定的變更不會影響內部部署 Active Directory 環境，即使已安裝代理程式也一樣。

**問：是否有 System Center Operations Manager 管理元件可用來 Azure AD 密碼保護？**

否。

**問：即使我已將原則設定為處於 Audit 模式，為什麼 Azure AD 仍會拒絕弱式密碼？**

只有內部部署 Active Directory 環境支援 Audit 模式。 Azure AD 在評估密碼時，隱含地一律為「強制」模式。

**問：當 Azure AD 密碼保護拒絕密碼時，我的使用者會看到傳統的 Windows 錯誤訊息。是否可以自訂這個錯誤訊息，讓使用者知道究竟發生什麼事？**

否。 網域控制站拒絕密碼時，使用者所看到的錯誤訊息是由用戶端電腦所控制，而不是由網域控制站控制。 這種行為會在預設的 Active Directory 密碼原則或以密碼篩選器為基礎的解決方案（例如 Azure AD 密碼保護）拒絕密碼時發生。

## <a name="additional-content"></a>其他內容

下列連結並不屬於核心的 Azure AD 密碼保護文件，但可能是適用於此功能的其他資訊來源。

[Azure AD 的密碼保護現在已正式推出！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[電子郵件網路釣魚防護指南–第15部分：在內部部署環境中執行 Microsoft Azure AD 的密碼保護服務 (！ ) ](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD 密碼保護和智慧鎖定現在已開放公開預覽！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft 頂級\統一支援訓練

如果您想要深入了解 Azure AD 密碼保護，並將其部署在您的環境中，可利用提供給頂級或統一支援合約客戶的 Microsoft 主動式服務。 此服務稱為 Azure Active Directory：密碼保護。 如需詳細資訊，請連絡技術支援專案經理。

## <a name="next-steps"></a>後續步驟

如果在這裡找不到您內部部署 Azure AD 密碼保護問題的解答，請在下面的意見項目提交，感謝您！

[部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)