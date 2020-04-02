---
title: Azure Marketplace 映像的安全性建議 | Microsoft Docs
description: 本文提供 Market Place 中所含映像的建議
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548664"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Azure Marketplace 映像的安全性建議

映射必須滿足這些安全配置建議。 這將有助於針對 Azure Marketplace 中的協力廠商解決方案映像維護高等級的安全性。

在提交之前,始終在映射上運行安全漏洞檢測。 如果在自己的已發佈映射中檢測到安全漏洞,則必須及時通知客戶該漏洞以及如何更正該漏洞。

## <a name="open-source-based-images"></a>開啟以來源為基礎的映像

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **類別目錄**                                                 | **勾選**                                                                                                                                                                                                                                                                              |
| 安全性                                                     | 安裝 Linux 發行版的所有最新安全修補程式。                                                                                                                                                                                                              |
| 安全性                                                     | 遵循行業準則,為特定的 Linux 發行版保護 VM 映射。                                                                                                                                                                                     |
| 安全性                                                     | 只利用需要的 Windows 伺服器角色、功能、服務和網路連接埠來維持最少的使用量，藉以限制受攻擊面。                                                                                                                                               |
| 安全性                                                     | 掃描原始程式碼和產生的 VM 映像以偵測惡意程式碼。                                                                                                                                                                                                                                   |
| 安全性                                                     | VHD 映射僅包括必要的鎖定帳戶,這些帳戶沒有允許互動式登錄的默認密碼;沒有後門                                                                                                                                           |
| 安全性                                                     | 禁用防火牆規則,除非應用程式在功能上依賴於這些規則,例如防火牆設備。                                                                                                                                                                             |
| 安全性                                                     | 從 VHD 映像中刪除所有敏感資訊,例如測試 SSH 密鑰、已知主機檔、日誌檔和不必要的證書。                                                                                                                                       |
| 安全性                                                     | 避免使用 LVM。                                                                                                                                                                                                                                            |
| 安全性                                                     | 包含需要函式庫的最新版本: </br> - OpenSSL v1.0 或更新版本 </br> - Python 2.5 或更新版本 (強烈建議使用 Python 2.6+) </br> - Python pyasn1 套件 (如果尚未安裝) </br> - d.OpenSSL v 1.0 或更新版本                                                                |
| 安全性                                                     | 清除 Bash/Shell 歷史記錄條目。                                                                                                                                                                                                                                             |
| 網路功能                                                   | 默認情況下包括 SSH 伺服器。 將 SSH 保持活動狀態設定為 sshd 配置,並具有以下選項:用戶端 AliveInterval 180。                                                                                                                                                        |
| 網路功能                                                   | 從映射中刪除任何自定義網路配置。 刪除 resolv.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| 部署                                                   | 安裝最新的 Azure Linux 代理。</br> - 使用 RPM 或 Deb 套件進行安裝。  </br> - 您也可以使用手動安裝程序，但建議使用且慣用安裝程式套件。 </br> - 如果是從 GitHub 存放庫手動安裝代理程式，請先將 `waagent` 檔案複製到 `/usr/sbin` 並 (以 root 身分) 執行： </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>代理程式設定檔將會放在 `/etc/waagent.conf`。 |
| 部署                                                   | 確保 Azure 支援可以在需要時為我們的合作夥伴提供串列控制台輸出,並為從雲端儲存安裝 OS 磁碟提供足夠的超時。 將以下參數加入映像核心的主引開行`console=ttyS0 earlyprintk=ttyS0 rootdelay=300`: |
| 部署                                                   | OS 磁碟上沒有交換磁碟分割。 交換可透過 Linux 代理程式要求，以便在本機資源磁碟上建立。         |
| 部署                                                   | 為 OS 磁碟創建單個根分區。      |
| 部署                                                   | 僅限 64 位元作業系統。                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>以 Windows Server 為基礎的映像

|||
|-------------| -------------------------|
| **類別目錄**                                                     | **勾選**                                                                                                                                                                |
| 安全性                                                         | 使用安全的 OS 基本映像。 針對以 Windows Server 為基礎之任何映像的來源使用的 VHD，必須來自透過 Microsoft Azure 提供的 Windows Server OS 映像。 |
| 安全性                                                         | 安裝所有最新的安全性更新。                                                                                                                                     |
| 安全性                                                         | 應用程式不應依賴於受限的使用者名,如管理員、根或管理員。                                                                |
| 安全性                                                         | 為作業系統硬碟和數據硬碟啟用 BitLocker 驅動器加密。                                                             |
| 安全性                                                         | 只利用已啟用的必要 Windows Server 角色、功能、服務和網路連接埠來維持最少的使用量，以限制受攻擊面。                         |
| 安全性                                                         | 掃描原始程式碼和產生的 VM 映像以偵測惡意程式碼。                                                                                                                     |
| 安全性                                                         | 設定 Windows Server 映像安全性更新來自動更新。                                                                                                                |
| 安全性                                                         | VHD 映射僅包括必要的鎖定帳戶,這些帳戶沒有允許互動式登錄的默認密碼;沒有後門                             |
| 安全性                                                         | 禁用防火牆規則,除非應用程式在功能上依賴於這些規則,例如防火牆設備。                                                               |
| 安全性                                                         | 從 VHD 映像中刪除所有敏感資訊,包括 HOSTS 檔、日誌檔和不必要的證書。                                              |
| 部署                                                       | 僅限 64 位元作業系統。                            |

即使您的組織在 Azure 市場中沒有映射,請考慮根據這些建議檢查 Windows 和 Linux 映射配置。

## <a name="contacting-customers"></a>聯絡客戶

要識別客戶及其聯絡人電子郵件:

1.  在"雲合作夥伴門戶"中,在左側軌上,選擇 **"見解**"。
2.  在「**訂單和使用方式**」選項卡上,使用 **「開始日期**」和 **「結束日期」** 欄位在所需的日期範圍內查詢使用方式。 這顯示了每天用於產品/服務中的 Azure 訂閱。 匯出此數據。 
3.  同樣,在 **「客戶」** 選項卡上,查詢和匯出客戶群。
4.  將步驟 2 中的訂閱 ID 與步驟 3 中的訂閱 ID 匹配,以查找必要的客戶資訊。
