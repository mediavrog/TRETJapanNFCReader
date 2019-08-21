![](TRETJapanNFCReader.png)

# TRETJapanNFCReader
日本のNFCカード向けリーダーライブラリ（iOS 9.3 以降、NFCカードの読み取りは iOS 13.0 以降）

Suica、PASMOなどの交通系ICカード、運転免許証の読み取り

日本語・英語に対応  
Japanese & English Support!

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/treastrain/TRETJapanNFCReader/blob/master/LICENSE)
![Swift: 5.0](https://img.shields.io/badge/Swift-5.0-orange.svg)
![Platform: iOS](https://img.shields.io/badge/platform-ios-lightgrey.svg)

関連するページ
- [treastrain/ios13-felica-reader: Sample project to read FeliCa on iOS 13 and later - GitHub](https://github.com/treastrain/ios13-felica-reader)
- [iOS 13 で FeliCa (Suica) にアクセス | notes from E](https://notes.tret.jp/ios13-felica-reading/)

## 対応予定の NFC カード
### NFC-B (Type-B)
- [x] 運転免許証
- 警察庁交通局運転免許課による「運転免許証及び運転免許証作成システム等仕様書（仕様書バージョン番号:008）」に対応
- 共通データ要素（MF/EF01）、暗証番号(PIN)設定（MF/EF02）の読み取り、暗証番号1による認証、記載事項(本籍除く)（DF1/EF01）まで実装済み

### NFC-F (Type-F)
- [x] 0003: 交通系ICカード (Suica, ICOCA, Kitaca, PASMO, TOICA, manaca, PiTaPa, SUGOCA, nimoca, はやかけん, りゅーと, SAPICA, odeca, くまモンのIC CARD, icsca, IruCa, PASPY, ...etc.)
    - IDm と System Code の表示
    - 残高の読み取りと表示
- [ ] FE00: 楽天Edy
- [ ] FE00: nanaco
- [ ] FE00: WAON
- [ ] FE00: 大学生協プリペイドカード（大学 学生証）

## 対応 OS
- iOS 9.3 以降

※ NFCカードの読み取りは iOS 13.0 以降で対応するデバイスで使用可能。
※ iOS 13.0 beta 1 では NFC-B (Type-B) を正常に読み書きできない事象を確認。iOS 13.0 beta 2 で解消。

## 使い方
### 全 NFC カード共通
1. プロジェクトの TARGET から開発している iOS Application を選び、Signing & Capabilities で Near Field Communication Tag Reading を有効にする（Near Field Communication Tag Reader Session Formats が entitlements ファイルに含まれている必要がある）。
2. Near Field Communication Tag Reader Session Formats の中に "NFC tag-specific data protocol (TAG)" が含まれていることを確認する。
3. 開発している iOS Application の Info.plist に "Privacy - NFC Scan Usage Description (NFCReaderUsageDescription)" を追加する。

### NFC-B (Type-B)
#### 運転免許証の場合
1. 運転免許証を読み取るには、開発している iOS Application の Info.plist に "ISO7816 application identifiers for NFC Tag Reader Session (com.apple.developer.nfc.readersession.iso7816.select-identifiers)" を追加する。ISO7816 application identifiers for NFC Tag Reader Session には以下を含める必要がある。
- Item 0: A0000002310100000000000000000000
- Item 1: A0000002310200000000000000000000
- Item 2: A0000002480300000000000000000000

2. ライブラリをインポートし、`DriversLicenseReader` を初期化した後でスキャンを開始する。
```swift
import UIKit
import TRETJapanNFCReader
class ViewController: UIViewController, DriversLicenseReaderSessionDelegate {

    var reader: DriversLicenseReader!

    override func viewDidLoad() {
        super.viewDidLoad()
        self.reader = DriversLicenseReader(self)
        self.reader.get(items: DriversLicenseCardItems.allCases, pin1: "暗証番号1")
    }

    func driversLicenseReaderSession(didRead driversLicenseCard: DriversLicenseCard) {
        // driversLicenseCard に読み取った運転免許証の情報が格納されている
    }
}
```

### NFC-F (Type-F)
- FeliCa を読み取るには、開発している iOS Application の Info.plist に "ISO18092 system codes for NFC Tag Reader Session (com.apple.developer.nfc.readersession.felica.systemcodes)" を追加する。ワイルドカードは使用できない。

#### 交通系ICカードの場合
- 0003
```swift
import UIKit
import TRETJapanNFCReader
class ViewController: UIViewController, TransitICReaderSessionDelegate {

    var reader: TransitICReader!

    override func viewDidLoad() {
        super.viewDidLoad()
        self.reader = TransitICReader(self)
        self.reader.get(items: TransitICCardItems.allCases)
    }

    func transitICReaderSession(didRead transitICCard: TransitICCard) {
        // transitICCard に読み取った交通系ICカードの情報が格納されている
    }
}
```

#### 大学生協プリペイドカード（大学 学生証）の場合
- FE00

## 謝辞 Acknowledgments
### 運転免許証 `TRETJapanNFCReader/DriversLicense` 
- `JIS0208.TXT`
    - 2015 Unicode®, Inc. For terms of use, see [http://www.unicode.org/terms_of_use.html](http://www.unicode.org/terms_of_use.html)


各電子マネー、電子マネーサービス等の名称は一般に各社の商標、登録商標です。
