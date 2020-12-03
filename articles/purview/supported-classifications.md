---
title: 支援的分類清單
description: 此頁面會列出 Azure 範疇中支援的系統分類。
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 7458b027add8be86d9491c674c2f1a0bc9fbc68c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552055"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure 範疇中支援的分類

本文列出 Azure 範疇 (Preview) 中支援和定義的系統分類。

## <a name="defined-system-classifications"></a>定義的系統分類

Azure 範疇會依 [RegEx](https://wikipedia.org/wiki/Regular_expression) 和 [Bloom 篩選準則](https://wikipedia.org/wiki/Bloom_filter)來分類資料。 下列清單說明 Azure 範疇定義系統分類的格式、模式和關鍵字。

每個分類名稱的前面都會加上 MICROSOFT。

## <a name="bloom-filter-classifications"></a>Bloom 篩選分類

## <a name="city-country-and-place"></a>城市、國家/地區和地點

您可以使用最適合用來準備資料的資料集，來準備城市、國家/地區和位置篩選。

## <a name="person"></a>個人

已使用下列兩個資料集來準備 Person bloom 濾波器。

- [2010美國人口普查資料的姓氏 (162-K 個專案) ](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [受歡迎的 Baby 名稱 (從 SSN) ，使用所有年份 1880-2019 (98-K 個專案) ](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx 分類

## <a name="aba-routing"></a>ABA 路由

### <a name="format"></a>格式

九位數，可能是格式化或未格式化的模式

### <a name="pattern"></a>模式

格式 化：

- 開頭為0、1、2、3、6、7或8的四位數
- 連字號
- 四位數
- 連字號
- 未格式化的數位：以0、1、2、3、6、7或8開頭的九個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>阿根廷國家身分識別 (DNI) 號碼

### <a name="format"></a>格式

八位數（含或不含句點）

### <a name="pattern"></a>模式

八位數：

- 兩位數
- 選用期限
- 三位數
- 選用期限
- 三位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>澳大利亞銀行帳戶號碼

### <a name="format"></a>格式

6到10位數（含或不含銀行狀態分支號碼）

### <a name="pattern"></a>模式

帳戶號碼為6到10位數。

澳大利亞銀行狀態分支號碼：

- 三位數
- 連字號
- 三位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>澳大利亞駕駛的授權號碼

### <a name="format"></a>格式
九個字母和數位

### <a name="pattern"></a>模式
九個字母和數位：

- 兩位數或字母 (不區分大小寫) 
- 兩位數
- 五位數或字母 (不區分大小寫) 

或

- 一到兩個選用字母 (不區分大小寫) 
- 四到九位數

或

- 九個數字或字母 (不區分大小寫) 

### <a name="keywords"></a>關鍵字

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australia-medical-account-number"></a>澳大利亞醫療帳戶號碼

### <a name="format"></a>格式

10-11 位數

### <a name="pattern"></a>模式

10-11 位數：

- 第一個數位位於2-6 範圍內
- 第九位數是檢查數位
- 第十個位數是問題數位
- 第十個數字 (選擇性) 是個別數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_australia_medical_account_number"></a>Keyword_Australia_Medical_Account_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>澳大利亞 passport 號碼

### <a name="format"></a>格式

後面接著七位數的字母

### <a name="pattern"></a>模式

字母 (不區分大小寫) 後面接著七位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_passport"></a>關鍵字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>關鍵字 \_ 澳大利亞 \_ passport \_ 號碼

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>澳大利亞稅檔案編號

### <a name="format"></a>格式

八至九位數

### <a name="pattern"></a>模式

八至九位數通常以空格顯示，如下所示：

- 三位數
- 選擇性的空間
- 三位數
- 選擇性的空間
- 兩到三位數，其中最後一個數位是檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_australia_tax_file_number"></a>關鍵字 \_ 澳大利亞 \_ 稅 \_ 檔案 \_ 編號

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>比利時全國電話號碼

### <a name="format"></a>格式

11位數加上選擇性分隔符號

### <a name="pattern"></a>模式

11位數加上分隔符號：

- 以 YY 格式表示的六位數和兩個選用句點。出生日期的 MM.DD
- 從點、虛線、空格的選擇性分隔符號
- 男性的三個連續數位 (奇數，即使是女性多) 
- 從點、虛線、空格的選擇性分隔符號
- 兩個檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_belgium_national_number"></a>關鍵字 \_ 比利時 \_ 全國 \_ 數位

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>巴西 CPF 號碼

### <a name="format"></a>格式

包含複選數位並可格式化或未格式化的11位數

### <a name="pattern"></a>模式

格式 化：

- 三位數
- 句號
- 三位數
- 句號
- 三位數
- 連字號
- 兩位數，也就是複選數位

未格式化：

- 11位數，其中最後兩個數字為複選數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_brazil_cpf"></a>關鍵字 \_ 巴西 \_ cpf

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>巴西法律實體編號 (CNPJ) 

### <a name="format"></a>格式

14位數，包括註冊號碼、分支號碼和檢查數位，加上分隔符號

### <a name="pattern"></a>模式

14位數，加上分隔符號：

- 兩位數
- 句號
- 三位數
- 句號
- 三位數 (這些前八位數是註冊號碼) 
- 斜線
- 四位數的分支編號
- 連字號
- 兩位數，也就是複選數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_brazil_cnpj"></a>關鍵字 \_ 巴西 \_ cnpj

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>巴西國家識別卡 (RG) 

### <a name="format"></a>格式

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>模式

:::no-loc text="Registro Geral (old format):":::

- 兩位數
- 句號
- 三位數
- 句號
- 三位數
- 連字號
- 一個數位，也就是複選數位

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 位數
- 連字號
- 一個數位，也就是複選數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_brazil_rg"></a>關鍵字 \_ 巴西 \_ rg

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>加拿大銀行帳戶號碼

### <a name="format"></a>格式

7或12位數

### <a name="pattern"></a>模式

加拿大銀行帳戶號碼為7或12位數。

加拿大銀行帳戶傳輸號碼為：

- 五位數
- 連字號
- 三位數或
- &quot;0 0&quot;
- 八位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_canada_bank_account_number"></a>關鍵字 \_ 加拿大 \_ 銀行 \_ 帳戶 \_ 號碼

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>加拿大駕駛的授權號碼

### <a name="format"></a>格式

依省份變化

### <a name="pattern"></a>模式

涵蓋 Calgary、英屬哥倫比亞、曼尼托巴省、New 伯倫瑞克省、紐芬蘭/拉布拉多省、新斯科 Scotia、安大略省、Prince Edward 島、魁北克和薩斯的各種模式

### <a name="keywords"></a>關鍵字

#### <a name="keyword_province_name_drivers_license_name"></a>關鍵字 \_ [省份 \_ 名稱] \_ 驅動程式 \_ 授權 \_ 名稱

- 省份縮寫，例如 AB
- 省份名稱，例如 Calgary

#### <a name="keyword_canada_drivers_license"></a>關鍵字 \_ 加拿大 \_ 驅動程式 \_ 授權

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>加拿大健全狀況服務編號

### <a name="format"></a>格式

10 位數

### <a name="pattern"></a>模式

10 位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_canada_health_service_number"></a>關鍵字 \_ 加拿大 \_ 健全狀況 \_ 服務 \_ 編號

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>加拿大 passport 號碼

### <a name="format"></a>格式

兩個大寫字母後接六位數

### <a name="pattern"></a>模式

兩個大寫字母後接六位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_canada_passport_number"></a>關鍵字 \_ 加拿大 \_ passport \_ 號碼

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>關鍵字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>加拿大個人健康識別號碼 (PHIN) 

### <a name="format"></a>格式

九位數

### <a name="pattern"></a>模式

九位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_canada_phin"></a>關鍵字 \_ 加拿大 \_ phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>關鍵字 \_ 加拿大 \_ 省份

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>加拿大社會保險編號

### <a name="format"></a>格式

具有選擇性連字號或空格的九位數

### <a name="pattern"></a>模式

格式 化：

- 三位數
- 連字號或空格
- 三位數
- 連字號或空格
- 三位數

未格式化：九位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_sin"></a>關鍵字 \_ sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>關鍵字 \_ sin \_ 協同作業

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>智利身分識別卡號碼

### <a name="format"></a>格式

七到八位數加上分隔符號 a 檢查數位或字母

### <a name="pattern"></a>模式

七到八位數加上分隔符號：

- 一到兩位數
- 選用期限
- 三位數
- 選用期限
- 三位數
- 虛線
- 一個數位或字母 (不區分大小寫) 這是一個檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_chile_id_card"></a>關鍵字 \_ 智利 \_ 識別碼 \_ 卡片

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>中國居民身分識別卡 (PRC) 號碼

### <a name="format"></a>格式

18位數

### <a name="pattern"></a>模式

18位數：

- 六位數，也就是位址代碼
- 八位數，格式為 YYYYMMDD，也就是出生日期
- 三位數，也就是訂單碼
- 一個數位，也就是複選數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_china_resident_id"></a>關鍵字 \_ 中國 \_ 居民 \_ 識別碼

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>信用卡號碼

### <a name="format"></a>格式

可以格式化或未格式化的14至16位數 (dddddddddddddddd) ，而且必須通過 Luhn 測試。

### <a name="pattern"></a>模式

複雜且健全的模式，可偵測全球所有主要品牌的卡片，包括簽證、MasterCard、探索卡、JCB、美國 Express、禮品卡和 diner 卡。

### <a name="keywords"></a>關鍵字

#### <a name="keyword_cc_verification"></a>關鍵字 \_ cc \_ 驗證

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>關鍵字 \_ 抄送 \_ 名稱

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>克羅地亞駕駛的授權號碼

此敏感性資訊類型實體僅適用于歐盟驅動程式的授權號碼敏感性資訊類型。

### <a name="format"></a>格式

八位數，不含空格和分隔符號

### <a name="pattern"></a>模式

八位數

### <a name="keywords"></a>關鍵字

#### <a name="keywords_eu_drivers_license_number"></a>關鍵字 \_ 歐盟 \_ 駕駛的 \_ 授權 \_ 號碼

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>關鍵字 \_ 克羅地亞 \_ eu \_ driver 的 \_ 授權 \_ 號碼

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>克羅地亞身分識別卡號碼

此敏感性資訊類型實體包含在歐盟國家識別身分的機密資訊類型中，並以獨立的敏感性資訊類型實體的形式提供。

### <a name="format"></a>格式

九位數

### <a name="pattern"></a>模式

九個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_croatia_id_card"></a>關鍵字 \_ 克羅地亞 \_ 識別碼 \_ 卡片

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>克羅地亞個人標識 (OIB) 號碼

### <a name="format"></a>格式

11位數

### <a name="pattern"></a>模式

11位數：

- 10 位數
- 最後一個數位是檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_croatia_oib_number"></a>關鍵字 \_ 克羅地亞 \_ oib \_ 號碼

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>丹麥個人識別碼

### <a name="format"></a>格式

包含連字號的10位數

### <a name="pattern"></a>模式

10位數：

- DDMMYY 格式的六位數，也就是出生日期
- 連字號
- 四位數，其中最後一個數位是檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_denmark_id"></a>關鍵字 \_ 丹麥 \_ 識別碼

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>EU 轉帳卡編號

### <a name="format"></a>格式

16位數

### <a name="pattern"></a>模式

複雜且健全的模式

### <a name="keywords"></a>關鍵字

#### <a name="keyword_eu_debit_card"></a>關鍵字 \_ eu \_ 轉帳 \_ 卡

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>關鍵字 \_ 卡 \_ 詞彙 \_ dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>關鍵字 \_ 卡 \_ 安全性 \_ 條款 \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>關鍵字 \_ 卡 \_ 到期 \_ 期限 \_ dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>歐盟駕駛的授權號碼

這些是歐盟驅動程式的授權號碼敏感性資訊類型中的實體。

- 奧地利
- 比利時
- 保加利亞
- 克羅埃西亞
- 賽普勒斯
- 捷克文
- 丹麥
- 愛沙尼亞
- 芬蘭
- 法國
- 德國
- 希臘
- 匈牙利
- 愛爾蘭
- 義大利
- 拉脫維亞
- 立陶宛
- 盧森堡
- 馬爾他
- 荷蘭
- 波蘭
- 葡萄牙
- 羅馬尼亞
- 斯洛伐克
- 斯洛維尼亞
- 西班牙
- 瑞典
- 英國。

## <a name="eu-national-identification-number"></a>歐盟國家/地區識別碼

這些是歐盟國家（地區）身分識別相關資訊類型中的實體。

- 奧地利
- 比利時
- 保加利亞
- 克羅埃西亞
- 賽普勒斯
- 捷克文
- 丹麥
- 愛沙尼亞
- 芬蘭
- 法國
- 德國
- 希臘
- 匈牙利
- 愛爾蘭
- 義大利
- 拉脫維亞
- 立陶宛
- 盧森堡
- 馬爾他
- 荷蘭
- 波蘭
- 葡萄牙
- 羅馬尼亞
- 斯洛伐克
- 斯洛維尼亞
- 西班牙
- 英國。

## <a name="eu-passport-number"></a>EU passport 號碼

這些是 EU passport number 機密資訊 typeThese 中的實體，是 EU passport 號碼組合中的實體。

- 奧地利
- 比利時
- 保加利亞
- 克羅埃西亞
- 賽普勒斯
- 捷克文
- 丹麥
- 愛沙尼亞
- 芬蘭
- 法國
- 德國
- 希臘
- 匈牙利
- 愛爾蘭
- 義大利
- 拉脫維亞
- 立陶宛
- 盧森堡
- 馬爾他
- 荷蘭
- 波蘭
- 葡萄牙
- 羅馬尼亞
- 斯洛伐克
- 斯洛維尼亞
- 西班牙
- 瑞典
- 英國。

## <a name="eu-social-security-number-or-equivalent-identification"></a>歐盟社會安全號碼或對等辨識

這些是位於歐盟社會安全號碼或對等身分識別相關資訊類型的實體。

- 奧地利
- 比利時
- 克羅埃西亞
- 捷克文
- 丹麥
- 芬蘭
- 法國
- 德國
- 希臘
- 匈牙利
- 葡萄牙
- 西班牙
- 瑞典

## <a name="eu-tax-identification-number"></a>歐盟稅務識別編號

這些實體是歐盟稅務識別編號的相關資訊類型。

- 奧地利
- 比利時
- 保加利亞
- 克羅埃西亞
- 賽普勒斯
- 捷克文
- 丹麥
- 愛沙尼亞
- 芬蘭
- 法國
- 德國
- 希臘
- 匈牙利
- 愛爾蘭
- 義大利
- 拉脫維亞
- 立陶宛
- 盧森堡
- 馬爾他
- 荷蘭
- 波蘭
- 葡萄牙
- 羅馬尼亞
- 斯洛伐克
- 斯洛維尼亞
- 西班牙
- 瑞典
- 英國。



## <a name="finland-national-id"></a>芬蘭國識別碼

### <a name="format"></a>格式

六個數字加上一個表示世紀的字元加上三個數字加上一個檢查數位

### <a name="pattern"></a>模式

模式必須包含下列各項：

- DDMMYY 格式的六位數，也就是出生日期
- 世紀標記 (是 '-'、' + ' 或 ' a ' ) 
- 三位數的個人識別碼
- 數位或字母 (不區分大小寫的) ，也就是複選數位

### <a name="keywords"></a>關鍵字

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>芬蘭 passport 號碼

這項敏感性資訊類型實體可在歐盟 Passport 數位機密資訊類型中使用，並可作為獨立的敏感性資訊類型實體提供使用。

### <a name="format"></a>格式

九個字母和數位的組合

### <a name="pattern"></a>模式

九個字母和數位的組合：

-  (不區分大小寫的兩個字母) 
- 七位數

### <a name="keywords"></a>關鍵字

#### <a name="keywords_eu_passport_number_common"></a>常用的關鍵字 \_ \_ \_ \_

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>關鍵字 \_ 芬蘭 \_ passport \_ 號碼

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>法國駕駛的授權號碼

此敏感性資訊類型實體可在歐盟驅動程式的「授權號碼」資訊類型中取得，而且可做為獨立的敏感性資訊類型實體。

### <a name="format"></a>格式

12 位數

### <a name="pattern"></a>模式

12位數的驗證，可折扣類似的模式，例如法文電話號碼

### <a name="keywords"></a>關鍵字

#### <a name="keyword_french_drivers_license"></a>關鍵字 \_ 法文 \_ 驅動程式 \_ 授權

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>法國國家（地區）識別碼卡 (CNI) 

### <a name="format"></a>格式

12 位數

### <a name="pattern"></a>模式

12 位數

### <a name="keywords"></a>關鍵字

#### <a name="keywords_france_eu_national_id_card"></a>\_法國 \_ eu 國家（地區） \_ \_ 識別碼 \_ 卡片的關鍵字

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>法國 passport 號碼

這項敏感性資訊類型實體可在歐盟 Passport 數位機密資訊類型中使用，並可作為獨立的敏感性資訊類型實體提供使用。

### <a name="format"></a>格式

九個數字和字母

### <a name="pattern"></a>模式

九個數字和字母：

- 兩位數
-  (不區分大小寫的兩個字母) 
- 五位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_passport"></a>關鍵字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>法國社會安全號碼 (INSEE) 或對等識別碼

此敏感性資訊類型實體包含在歐盟社會安全號碼和對等的識別碼敏感性資訊類型中，且可作為獨立的敏感性資訊類型實體。

### <a name="format"></a>格式

15 位數

### <a name="pattern"></a>模式

必須符合下列兩種模式的其中一種：

- 13位數後面接著空格，後面接著兩位數或
- 15個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_fr_insee"></a>關鍵字 \_ fr \_ insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>德國駕駛的授權號碼

此敏感性資訊類型實體包含在歐盟驅動程式的授權號碼機密資訊類型中，而且可做為獨立的敏感性資訊類型實體。

### <a name="format"></a>格式

11個數字和字母的組合

### <a name="pattern"></a>模式

11個數字和字母 (不區分大小寫) ：

- 數位或字母
- 兩位數
- 六個數字或字母
- 數位
- 數位或字母

### <a name="keywords"></a>關鍵字

#### <a name="keyword_german_drivers_license_number"></a>關鍵字 \_ 德文 \_ 驅動程式 \_ 授權 \_ 號碼

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>德國身分識別卡編號

### <a name="format"></a>格式

自2010年11月1日起：九個字母和數位

自1987年4月1日到31日2010：10位數

### <a name="pattern"></a>模式

自2010年11月1日起：

- 一個字母 (不區分大小寫) 
- 八位數

自1987年4月1日到31日2010：

- 10 位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_germany_id_card"></a>關鍵字 \_ 德國 \_ 識別碼 \_ 卡片

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>德國 passport 號碼

此敏感性資訊類型實體包含在 EU Passport 數位機密資訊類型中，並以獨立的敏感性資訊類型實體的形式提供。

### <a name="format"></a>格式

10位數或字母

### <a name="pattern"></a>模式

模式必須包含下列各項：

- 第一個字元是此集合中的數位或字母， (C、F、G、H、J、K) 
- 三位數
- 這組的五位數或字母 (C、-H、J N、P、R、T、V Z) 
- 數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_german_passport"></a>關鍵字 \_ 德文 \_ passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>希臘國家識別碼卡片

### <a name="format"></a>格式

7-8 字母和數位的組合加上虛線

### <a name="pattern"></a>模式

 (舊格式的七個字母和數位) ：

- 一個字母 (希臘文字母的任何字母) 
- 虛線
- 六位數

 (新格式的八個字母和數位) ：

- 兩個字母，其大寫字元會同時出現在希臘文和拉丁字母 (ABEZHIKMNOPTYX) 
- 虛線
- 六位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_greece_id_card"></a>關鍵字 \_ 希臘 \_ 識別碼 \_ 卡片

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>香港特別行政區身分識別卡 (HKID) 號碼

### <a name="format"></a>格式

8-9 字母和數位的組合加上最後一個字元周圍的選擇性括弧

### <a name="pattern"></a>模式

8-9 字母的組合：

- 1-2 字母 (不區分大小寫) 
- 六位數
- 最後一個字元 (任何數位或字母 A) ，也就是檢查位數，並選擇性地以括弧括住。

### <a name="keywords"></a>關鍵字

#### <a name="keyword_hong_kong_id_card"></a>關鍵字 \_ 香港 \_ 特別行政區 \_ 識別碼 \_ 卡

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP 位址

### <a name="format"></a>格式

#### <a name="ipv4"></a>IPv4

複雜模式， (期間格式化的帳戶) 和未格式化 (不) IPv4 位址的版本

#### <a name="ipv6"></a>IPv6：

格式化 IPv6 數位的複雜模式 (其中包含冒號) 

### <a name="pattern"></a>模式

### <a name="keywords"></a>關鍵字

#### <a name="keyword_ipaddress"></a>關鍵字 \_ ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>印度的永久帳戶號碼 (平移) 

### <a name="format"></a>格式

10個字母或數位

### <a name="pattern"></a>模式

10個字母或數位：

- 三個字母 (不區分大小寫) 
- C、P、H、F、A、T、B、L、J、G 中的字母 (不區分大小寫) 
- 信件
- 四個數字
- 字母 (不區分大小寫) 

### <a name="keywords"></a>關鍵字

#### <a name="keyword_india_permanent_account_number"></a>關鍵字 \_ 印度 \_ 永久 \_ 帳戶 \_ 號碼

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>印度唯一識別 (Aadhaar) 號碼

### <a name="format"></a>格式

包含選擇性空格或連字號的12位數

### <a name="pattern"></a>模式

12位數：

- 數位，不是0或1
- 三位數
- 選擇性的空格或虛線
- 四個數字
- 選擇性的空格或虛線
- 最後一個數位，也就是檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_india_aadhar"></a>\_印度 \_ aadhar 關鍵字

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>印尼身分識別卡 (KTP) 號碼

### <a name="format"></a>格式

包含選用句號的16位數

### <a name="pattern"></a>模式

16位數：

- 兩位數省份的代碼
- 句點 (選擇性) 
- 兩位數的 regency 或城市代碼
- 兩位數的 subdistrict 碼
- 句點 (選擇性) 
- DDMMYY 格式的六位數，也就是出生日期
- 句點 (選擇性) 
- 四個數字

### <a name="keywords"></a>關鍵字

#### <a name="keyword_indonesia_id_card"></a>關鍵字 \_ 印尼 \_ 識別碼 \_ 卡片

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>國際銀行帳戶號碼 (IBAN) 

### <a name="format"></a>格式

國家/地區代碼 (兩個字母) 加上 (兩個數字) 加上 :::no-loc text="bban"::: 數位 (最多30個字元) 

### <a name="pattern"></a>模式

模式必須包含下列各項：

- 兩個字母的國家/地區代碼
- 兩個檢查數位 (後面接著選擇性的空間) 
- 1-7 有四個字母或數位的群組 (可以空格分隔) 
- 1-3 字母或數位

每個國家/地區的格式稍有不同。 IBAN 敏感性資訊類型涵蓋這些60國家/地區：

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>關鍵字

無

## <a name="ireland-personal-public-service-pps-number"></a>愛爾蘭個人公用服務 (PPS) 號碼

### <a name="format"></a>格式

舊格式 (到2012年12月31日) ：

- 七位數後接1-2 字母

新格式 (1 2013 年1月和之後) ：

- 七位數，後面接著兩個字母

### <a name="pattern"></a>模式

舊格式 (到2012年12月31日) ：

- 七位數
- 一到兩個字母 (不區分大小寫) 

新格式 (1 2013 年1月和之後) ：

- 七位數
- 字母 (不區分大小寫的) ，也就是字母複選數位
- 範圍 A-I 或 W 中的選擇性字母 &quot;&quot;

### <a name="keywords"></a>關鍵字

#### <a name="keywords_ireland_eu_national_id_card"></a>\_愛爾蘭 \_ 歐盟國家（地區） \_ \_ 識別碼 \_ 卡片的關鍵字

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>以色列銀行帳戶號碼

### <a name="format"></a>格式

13位數

### <a name="pattern"></a>模式

格式 化：

- 兩位數
- 虛線
- 三位數
- 虛線
- 八位數

未格式化：

- 13個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_israel_bank_account_number"></a>關鍵字 \_ 以色列 \_ 銀行 \_ 帳戶 \_ 號碼

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>以色列國家識別碼

### <a name="format"></a>格式

九位數

### <a name="pattern"></a>模式

九個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_israel_national_id"></a>關鍵字 \_ 以色列 \_ 國家/地區 \_ 識別碼

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>義大利駕駛的授權號碼

此敏感性資訊類型實體包含在歐盟驅動程式的授權號碼機密資訊類型中，而且可做為獨立的敏感性資訊類型實體。

### <a name="format"></a>格式

10個字母和數位的組合

### <a name="pattern"></a>模式

10個字母和數位的組合：

- 一個字母 (不區分大小寫) 
- 字母 &quot; A &quot; 或 &quot; V &quot; (不區分大小寫) 
- 七位數
- 一個字母 (不區分大小寫) 

### <a name="keywords"></a>關鍵字

#### <a name="keyword_italy_drivers_license_number"></a>關鍵字 \_ 義大利 \_ 驅動程式 \_ 授權 \_ 號碼

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>日本銀行帳戶號碼

### <a name="format"></a>格式

七位數或八位數

### <a name="pattern"></a>模式

銀行帳戶號碼：

- 七位數或八位數
- 銀行帳戶分支碼：
- 四位數
-  (選擇性) 的空格或虛線
- 三位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_jp_bank_account"></a>關鍵字 \_ jp \_ 銀行 \_ 帳戶

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>關鍵字 \_ jp \_ 銀行 \_ 分支 \_ 碼

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>日本駕駛的授權號碼

### <a name="format"></a>格式

12 位數

### <a name="pattern"></a>模式

12個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_jp_drivers_license_number"></a>關鍵字 \_ jp \_ 驅動程式 \_ 授權 \_ 號碼

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>日本 passport 號碼

### <a name="format"></a>格式

後面接著七位數的兩個字母

### <a name="pattern"></a>模式

兩個字母 (不區分大小寫) 後接七位數

#### <a name="keyword_jp_passport"></a>關鍵字 \_ jp \_ passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>日本居住卡編號

### <a name="format"></a>格式

12個字母和數位

### <a name="pattern"></a>模式

12個字母和數位：

-  (不區分大小寫的兩個字母) 
- 八位數
-  (不區分大小寫的兩個字母) 

### <a name="keywords"></a>關鍵字

#### <a name="keyword_jp_residence_card_number"></a>關鍵字 \_ jp 的 \_ 居住 \_ 卡 \_ 號

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>日本居民註冊號碼

### <a name="format"></a>格式

11位數

### <a name="pattern"></a>模式

11個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_jp_resident_registration_number"></a>關鍵字 \_ jp \_ 居民 \_ 註冊 \_ 號碼

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>日本社會保險號碼 (SIN) 

### <a name="format"></a>格式

7-12 位數

### <a name="pattern"></a>模式

7-12 位數：

- 四位數
- 連字號 (選擇性) 
- 六位數或
- 7-12 連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_jp_sin"></a>關鍵字 \_ jp \_ sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>馬來西亞辨識卡編號

### <a name="format"></a>格式

包含選用連字號的12位數

### <a name="pattern"></a>模式

12位數：

- YYMMDD 格式的六位數，也就是出生日期
- 虛線 (選擇性) 
- 兩個字母的生日代碼
- 虛線 (選擇性) 
- 三個亂數字
- 一位數的性別代碼

### <a name="keywords"></a>關鍵字

#### <a name="keyword_malaysia_id_card_number"></a>關鍵字 \_ 馬來西亞 \_ 識別碼 \_ 卡片 \_ 編號

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>荷蘭公民服務 (BSN) 號碼

### <a name="format"></a>格式

八個包含選擇性空格的9個數字

### <a name="pattern"></a>模式

八-9 位數：

- 三位數
-  (選擇性的空間) 
- 三位數
-  (選擇性的空間) 
- 雙三位數

### <a name="keywords"></a>關鍵字

#### <a name="keywords_netherlands_eu_national_id_card"></a>關鍵字 \_ 荷蘭 \_ eu \_ 國家（地區） \_ 識別碼 \_ 卡片

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>新的紐約的健康情況號碼

### <a name="format"></a>格式

三個字母、一個空格 (選擇性的) 和四個數字

### <a name="pattern"></a>模式

- 三個字母 (不區分大小寫) ，但 ' I ' 和 ' O ' 除外
-  (選擇性的空間) 
- 四位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_nz_terms"></a>關鍵字 \_ nz \_ 詞彙

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>挪威識別編號

### <a name="format"></a>格式

11位數

### <a name="pattern"></a>模式

11位數：

- DDMMYY 格式的六位數，也就是出生日期
- 三位數的個別數位
- 兩個檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_norway_id_number"></a>關鍵字 \_ 挪威 \_ id \_ 號碼

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>菲律賓統一的多重用途識別碼

### <a name="format"></a>格式

以連字號分隔的12位數

### <a name="pattern"></a>模式

12位數：

- 四位數
- 連字號
- 七位數
- 連字號
- 一個數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_philippines_id"></a>關鍵字 \_ 菲律賓 \_ 識別碼

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>波蘭身分識別卡

### <a name="format"></a>格式

三個字母和六位數

### <a name="pattern"></a>模式

三個字母 (不區分大小寫) 後接六位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_poland_national_id_passport_number"></a>關鍵字 \_ 波蘭 \_ 國家 \_ 識別碼 \_ passport \_ 號碼

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>波蘭國家識別碼 (PESEL) 

### <a name="format"></a>格式

11位數

### <a name="pattern"></a>模式

- 以 YYMMDD 格式代表出生日期的6位數
- 4位數
- 1個檢查數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_pesel_identification_number"></a>關鍵字 \_ pesel \_ 識別碼 \_

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>波蘭 passport 號碼

此敏感性資訊類型實體包含在 EU Passport 數位機密資訊類型中，並以獨立的敏感性資訊類型實體的形式提供。

### <a name="format"></a>格式

兩個字母和七位數

### <a name="pattern"></a>模式

兩個字母 (不區分大小寫) 後接七位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_poland_national_id_passport_number"></a>關鍵字 \_ 波蘭 \_ 國家 \_ 識別碼 \_ passport \_ 號碼

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>葡萄牙公民卡片編號

### <a name="format"></a>格式

八位數

### <a name="pattern"></a>模式

八位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_portugal_citizen_card"></a>關鍵字 \_ 葡萄牙 \_ 公民 \_ 卡片

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>葡萄牙駕駛的授權號碼

此敏感性資訊類型實體僅適用于歐盟驅動程式的授權號碼敏感性資訊類型。

### <a name="format"></a>格式

兩個模式-具有特殊字元的兩個字母后接5-8 位數

### <a name="pattern"></a>模式

模式1：以特殊字元開頭為5/6 的兩個字母：

- 兩個字母 (不區分大小寫) 
- 連字號
- 五或六位數
- 空格
- 一個數位

模式2：以特殊字元加上包含6/8 位數的一個字母：

- 一個字母 (不區分大小寫) 
- 連字號
- 六位數或八位數
- 空格
- 一個數位

### <a name="keywords"></a>關鍵字

#### <a name="keywords_eu_drivers_license_number"></a>關鍵字 \_ 歐盟 \_ 駕駛的 \_ 授權 \_ 號碼

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>關鍵字 \_ 葡萄牙 \_ 歐盟 \_ 駕駛的 \_ 授權 \_ 號碼

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>沙烏地阿拉伯國識別碼

### <a name="format"></a>格式

10 位數

### <a name="pattern"></a>模式

10個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_saudi_arabia_national_id"></a>關鍵字 \_ 沙烏地阿拉伯 \_ \_ 國家/地區 \_ 識別碼

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>新加坡國家註冊身分識別卡 (NRIC) 號碼

### <a name="format"></a>格式

九個字母和數位

### <a name="pattern"></a>模式

- 九個字母和數位：
- 字母 &quot; F &quot; 、 &quot; G &quot; 、 &quot; S &quot; 或 &quot; T &quot; (不區分大小寫) 
- 七位數
- 字母複選數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_singapore_nric"></a>關鍵字 \_ 新加坡 \_ nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>南非識別碼

### <a name="format"></a>格式

可能包含空格的13位數

### <a name="pattern"></a>模式

13位數：

- YYMMDD 格式的六位數，也就是出生日期
- 四位數
- 單一位數的公民指標
- 數位 &quot; 8 &quot; 或 &quot; 9&quot;
- 一個數位，也就是總和檢查碼數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_south_africa_identification_number"></a>關鍵字 \_ 南非 \_ \_ 識別碼 \_

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>韓國居民註冊號碼

### <a name="format"></a>格式

包含連字號的13位數

### <a name="pattern"></a>模式

13位數：

- YYMMDD 格式的六位數，也就是出生日期
- 連字號
- 由世紀和性別決定的一個數位
- 四位數的生日區功能變數代碼
- 一個數位，用來區分先前數位相同的人員
- 檢查數位。

### <a name="keywords"></a>關鍵字

#### <a name="keyword_south_korea_resident_number"></a>關鍵字 \_ 南 \_ 韓國 \_ 居民 \_ 號碼

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>西班牙社會安全號碼 (SSN) 

此敏感性資訊類型實體包含在歐盟社會安全號碼或對等的識別碼敏感性資訊類型中，且可做為獨立的敏感性資訊類型實體。

### <a name="format"></a>格式

11-12 位數

### <a name="pattern"></a>模式

11-12 位數：

- 兩位數
- 斜線 (選用) 
- 七到八位數
- 斜線 (選用) 
- 兩位數

### <a name="keywords"></a>關鍵字

無

## <a name="sweden-national-id"></a>瑞典國識別碼

### <a name="format"></a>格式

10或12位數和選擇性分隔符號

### <a name="pattern"></a>模式

10或12位數和選擇性分隔符號：

-  (選擇性的兩位數) 
- 日期格式的六位數 YYMMDD
- &quot; - &quot; 或 &quot; + &quot; (選擇性) 的分隔符號
- 四位數

### <a name="keywords"></a>關鍵字

#### <a name="keywords_swedish_national_identifier"></a>關鍵字 \_ 瑞典文的 \_ 國家/地區 \_ 識別碼

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>瑞典 passport 號碼

此敏感性資訊類型實體包含在 EU Passport 數位機密資訊類型中，並以獨立的敏感性資訊類型實體的形式提供。

### <a name="format"></a>格式

八位數

### <a name="pattern"></a>模式

八個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_sweden_passport"></a>關鍵字 \_ 瑞典 \_ passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>關鍵字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT 程式碼

### <a name="format"></a>格式

四個字母后接5-31 個字母或數位

### <a name="pattern"></a>模式

四個字母后接5-31 個字母或數位：

- 四個字母的 bank 程式碼 (不區分大小寫) 
- 選擇性的空間
- 4-28 (基本銀行帳戶號碼的字母或數位 (BBAN) # A3
- 選擇性的空間
- 一到三個字母或數位 (其餘的 BBAN) 

### <a name="keywords"></a>關鍵字

#### <a name="keyword_swift"></a>關鍵字 \_ swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>臺灣國辨識編號

### <a name="format"></a>格式

一個字母 (英文) 後面接著九個數字

### <a name="pattern"></a>模式

一個字母 (英文) 後面接著九位數：

- 英文 (一個字母，不區分大小寫) 
- 數位 &quot; 1 &quot; 或 &quot; 2&quot;
- 八位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_taiwan_national_id"></a>關鍵字 \_ 臺灣 \_ 國家 \_ 識別碼

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>臺灣 passport 號碼

### <a name="format"></a>格式

- 生物識別 passport 號碼：九位數
- 非生物特徵辨識 passport 號碼：九位數

### <a name="pattern"></a>模式

生物識別 passport 號碼：

- 字元 &quot; 3&quot;
- 八位數

非生物特徵辨識 passport 號碼：

- 九位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_taiwan_passport"></a>關鍵字 \_ 臺灣 \_ passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>臺灣居民憑證 (ARC/TARC) 號碼

### <a name="format"></a>格式

10個字母和數位

### <a name="pattern"></a>模式

10個字母和數位：

-  (不區分大小寫的兩個字母) 
- 八位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_taiwan_resident_certificate"></a>關鍵字 \_ 臺灣 \_ 居民 \_ 憑證

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>英國。 驅動程式的授權號碼

此敏感性資訊類型實體包含在歐盟驅動程式的授權號碼機密資訊類型中，而且可做為獨立的敏感性資訊類型實體。

### <a name="format"></a>格式

18個字母和數位的組合（以指定的格式表示）

### <a name="pattern"></a>模式

18個字母和數位：

- 五個字母 (不區分大小寫) 或數位 &quot; 9 &quot; 來取代字母
- 一個數位
- 日期格式的五位數 MMDDY 如果是「出生日期」 (第七個字元，則會遞增50（如果驅動程式是女性），也就是51至62，而不是01至 12) 
- 兩個字母 (不區分大小寫) 或數位 &quot; 9 &quot; 來取代字母
- 五位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_uk_drivers_license"></a>關鍵字 \_ uk \_ 驅動程式 \_ 授權

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>英國。 選舉的滾動編號

### <a name="format"></a>格式

後面接著1-4 位數的兩個字母

### <a name="pattern"></a>模式

兩個字母 (不區分大小寫) 後面接著1-4 數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_uk_electoral"></a>關鍵字 \_ uk \_ 選舉

- 委員會提名
- 提名表單
- 選舉註冊
- 選舉匯總

## <a name="uk-national-health-service-number"></a>英國。 國家/地區健全狀況服務編號

### <a name="format"></a>格式

以空格分隔的10-17 位數

### <a name="pattern"></a>模式

10-17 位數：

- 三位數或10位數
- 空格
- 三位數
- 空格
- 四位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_uk_nhs_number"></a>關鍵字 \_ uk \_ nhs \_ 號碼

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>關鍵字 \_ uk \_ nhs \_ 數位1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>關鍵字 \_ uk \_ nhs \_ number \_ dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>英國。 全國保險號碼 (NINO) 

此敏感性資訊類型實體包含在歐盟國家識別身分的機密資訊類型中，並以獨立的敏感性資訊類型實體的形式提供。

### <a name="format"></a>格式

以空格或連字號分隔的七個字元或九個字元

### <a name="pattern"></a>模式

有兩種可能的模式：

- 兩個字母 (有效 NINOs 只使用這個前置詞中的特定字元，此模式會進行驗證;不區分大小寫) 
- 六位數
- ' A '、' B '、' C ' 或 ' d ' (例如前置詞，在後置字元中只允許某些字元;不區分大小寫) 

或

- 兩個字母
- 空格或虛線
- 兩位數
- 空格或虛線
- 兩位數
- 空格或虛線
- 兩位數
- 空格或虛線
- ' A '、' B '、' C ' 或 ' d '


### <a name="keywords"></a>關鍵字

#### <a name="keyword_uk_nino"></a>關鍵字 \_ uk \_ nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>英國。 唯一的納稅人參考編號

此敏感性資訊類型僅可用於：

- 資料遺失防護原則
- 通訊合規性原則
- 資訊治理
- 記錄管理
- Microsoft cloud app security

### <a name="format"></a>格式

10位數，不含空格和分隔符號

### <a name="pattern"></a>模式

10 位數

### <a name="keywords"></a>關鍵字

#### <a name="keywords_uk_eu_tax_file_number"></a>關鍵字 \_ uk \_ eu \_ 稅 \_ 檔案 \_ 編號

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>美國銀行帳戶號碼

### <a name="format"></a>格式

6-17 位數

### <a name="pattern"></a>模式

6-17 連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_usa_bank_account"></a>關鍵字 \_ usa \_ 銀行 \_ 帳戶

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>美國駕駛的授權號碼

### <a name="format"></a>格式

取決於狀態

### <a name="pattern"></a>模式

取決於狀態--例如紐約：

- 九個格式化的數位，例如 ddd 將會相符。
- 九個數字（例如 ddddddddd）將不相符。

### <a name="keywords"></a>關鍵字

#### <a name="keyword_us_drivers_license_abbreviations"></a>關鍵字 \_ us \_ 驅動程式 \_ 授權 \_ 縮寫

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>關鍵字 \_ us \_ 驅動程式 \_ 授權

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>關鍵字 \_ [state \_ name] \_ 驅動程式 \_ 授權 \_ 名稱

- 州縮寫 (例如， &quot; NY &quot;) 
- 狀態名稱 (例如 &quot; 紐約 &quot;) 

## <a name="us-individual-taxpayer-identification-number-itin"></a>美國個別的納稅人識別碼 (ITIN) 

### <a name="format"></a>格式

九個數字，以 &quot; 9 開始 &quot; ，並包含 &quot; 7 &quot; 或 &quot; 8 &quot; 作為第四位數，並選擇性地使用空格或連字號來格式化

### <a name="pattern"></a>模式

格式 化：

- 數位 &quot; 9&quot;
- 兩位數
- 空格或虛線
- &quot;7 &quot; 或 &quot; 8&quot;
- 數位
- 空格或虛線
- 四位數

帶

- 數位 &quot; 9&quot;
- 兩位數
- &quot;7 &quot; 或 &quot; 8&quot;
- 五位數

### <a name="keywords"></a>關鍵字

#### <a name="keyword_itin"></a>關鍵字 \_ itin

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>美國社會安全號碼 (SSN) 

### <a name="format"></a>格式

九位數，可能是格式化或未格式化的模式

>[!Note]
> 如果在2011年前發出，SSN 有強式格式設定，其中數位的某些部分必須落在特定範圍內才能有效 (但沒有任何總和檢查碼) 。
>

### <a name="pattern"></a>模式

四個函式會以四種不同的模式尋找 Ssn：

- Func \_ ssn 會尋找具有2011版強式格式設定的 ssn，格式為虛線或空格 (ddd-dd-DDDD 或 ddd dd dddd) 
- Func \_ \_ 未格式化的 ssn 會尋找具有預先2011強格式的 ssn，且未格式化為九個連續的數位 (ddddddddd) 
- Func \_ 隨機化 \_ 格式化 \_ 的 ssn 會尋找以連字號或空格 (ddd-DD-dddd 或 ddd dd dddd 格式化的2011後 ssn) 
- Func \_ 隨機化 \_ \_ 未格式化的 ssn 會尋找未格式化為九個連續數位 (ddddddddd 的2011後 ssn) 

### <a name="keywords"></a>關鍵字

#### <a name="keyword_ssn"></a>關鍵字 \_ ssn

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>美國/英國 passport 號碼

英國 passport number 敏感性資訊類型實體可用於 EU Passport 數位敏感性資訊類型，而且可做為獨立的敏感性資訊類型實體。

### <a name="format"></a>格式

九位數

### <a name="pattern"></a>模式

九個連續數位

### <a name="keywords"></a>關鍵字

#### <a name="keyword_passport"></a>關鍵字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
