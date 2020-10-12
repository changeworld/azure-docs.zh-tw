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
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89536377"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Azure Marketplace 映像的安全性建議

您的映射必須符合這些安全性設定建議。 這將有助於針對 Azure Marketplace 中的協力廠商解決方案映像維護高等級的安全性。

提交之前，請一律在您的映射上執行安全性弱點偵測。 如果您在自己發佈的映射中偵測到安全性弱點，則必須及時通知客戶弱點和修正方式。

## <a name="open-source-based-images"></a>開啟以來源為基礎的映像

| 類別 | 勾選 |
| -------- | ----- |
| 安全性                                                     | 安裝適用于 Linux 發行版本的所有最新安全性修補程式。                                                                                                                                                                                                              |
| 安全性                                                     | 遵循業界指導方針來保護特定 Linux 散發套件的 VM 映射。                                                                                                                                                                                     |
| 安全性                                                     | 只利用需要的 Windows 伺服器角色、功能、服務和網路連接埠來維持最少的使用量，藉以限制受攻擊面。                                                                                                                                               |
| 安全性                                                     | 掃描原始程式碼和產生的 VM 映像以偵測惡意程式碼。                                                                                                                                                                                                                                   |
| 安全性                                                     | VHD 映射只包含必要的已鎖定帳戶，且該帳戶沒有允許互動式登入的預設密碼;沒有後門。                                                                                                                                           |
| 安全性                                                     | 除非應用程式功能依賴防火牆規則（例如防火牆設備），否則請停用防火牆規則。                                                                                                                                                                             |
| 安全性                                                     | 從 VHD 映射中移除所有機密資訊，例如測試 SSH 金鑰、已知的主機檔案、記錄檔和不必要的憑證。                                                                                                                                       |
| 安全性                                                     | 避免使用 LVM。                                                                                                                                                                                                                                            |
| 安全性                                                     | 包含最新版本的必要程式庫： </br> - OpenSSL v1.0 或更新版本 </br> - Python 2.5 或更新版本 (強烈建議使用 Python 2.6+) </br> - Python pyasn1 套件 (如果尚未安裝) </br> - d.OpenSSL v 1.0 或更新版本                                                                |
| 安全性                                                     | 清除 Bash/Shell 歷程記錄專案。                                                                                                                                                                                                                                             |
| 網路功能                                                   | 預設包含 SSH 伺服器。 使用下列選項，將 SSH 保持運作至 sshd config： ClientAliveInterval 180。                                                                                                                                                        |
| 網路功能                                                   | 從映射中移除任何自訂網路設定。 刪除 resolv.conf.d： `rm /etc/resolv.conf` 。                                                                                                                                                                                |
| 部署                                                   | 安裝最新的 Azure Linux 代理程式。</br> -使用 RPM 或 Deb 套件進行安裝。  </br> - 您也可以使用手動安裝程序，但建議使用且慣用安裝程式套件。 </br> - 如果是從 GitHub 存放庫手動安裝代理程式，請先將 `waagent` 檔案複製到 `/usr/sbin` 並 (以 root 身分) 執行： </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>代理程式設定檔將會放在 `/etc/waagent.conf`。 |
| 部署                                                   | 確定 Azure 支援可以在需要時提供序列主控台輸出給我們的合作夥伴，並為從雲端儲存體掛接的 OS 磁片提供足夠的超時時間。 將下列參數新增至映射核心開機行： `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` 。 |
| 部署                                                   | OS 磁碟上沒有交換磁碟分割。 交換可透過 Linux 代理程式要求，以便在本機資源磁碟上建立。         |
| 部署                                                   | 為 OS 磁片建立單一根磁碟分割。      |
| 部署                                                   | 僅限 64 位元作業系統。                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>以 Windows Server 為基礎的映像

| 類別 | 勾選 |
|--------- | ----- |
| 安全性                                                         | 使用安全的 OS 基本映像。 針對以 Windows Server 為基礎之任何映像的來源使用的 VHD，必須來自透過 Microsoft Azure 提供的 Windows Server OS 映像。 |
| 安全性                                                         | 安裝所有最新的安全性更新。                                                                                                                                     |
| 安全性                                                         | 應用程式不應該依賴受限制的使用者名稱，例如系統管理員、根或系統管理員。                                                                |
| 安全性                                                         | 啟用作業系統硬碟和資料硬碟的 BitLocker 磁碟機加密。                                                             |
| 安全性                                                         | 只利用已啟用的必要 Windows Server 角色、功能、服務和網路連接埠來維持最少的使用量，以限制受攻擊面。                         |
| 安全性                                                         | 掃描原始程式碼和產生的 VM 映像以偵測惡意程式碼。                                                                                                                     |
| 安全性                                                         | 設定 Windows Server 映像安全性更新來自動更新。                                                                                                                |
| 安全性                                                         | VHD 映射只包含必要的已鎖定帳戶，且該帳戶沒有允許互動式登入的預設密碼;沒有後門。                             |
| 安全性                                                         | 除非應用程式功能依賴防火牆規則（例如防火牆設備），否則請停用防火牆規則。                                                               |
| 安全性                                                         | 從 VHD 映射中移除所有機密資訊，包括主機檔案、記錄檔和不必要的憑證。                                              |
| 部署                                                       | 僅限 64 位元作業系統。                            |

即使您的組織沒有 Azure marketplace 中的映射，也請考慮檢查您的 Windows 和 Linux 映射設定是否符合這些建議。

