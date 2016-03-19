主に単語で答えるゲームにおける汎用的な辞書形式
================================================================================
<a name="terminology">用語などについて</a>
--------------------------------------------------------------------------------
* <a name="filename-without-extension">「拡張子を除くファイル名」</a>とは、
  ファイル名について、先頭から最初に登場するフルストップ `.` の手前までの部分である。
* <a name="file-location">「ファイル所在」</a>は、次のいずれかである:
  + それが記述されているCSVファイルとともにアーカイブに格納されているファイルの名前。
  + [Webサービス識別子](#web-service-identifier)、ソリダス `/`、[妥当なファイル名](#valid-filename)の並び。
* <a name="web-service-identifier">「Webサービス識別子」</a>はファイルの所在場所を表す文字列であり、
  [ASCII数字][ascii-digits]、[ASCII英小文字][lowercase-ascii-letters]、ハイフンマイナス `-` のみを含む文字列、
  あるいは[ドメイン][domain]である。
* <a name="valid-filename">「妥当なファイル名」</a>とは、
  [拡張子を除くファイル名](#filename-without-extension)が次の規則に合致する文字列である。
  また、拡張子は、表[拡張子](#extension)で示すものに合致する。
  + [制御文字](#controls)、および `"` `*` `.` `/` `:` `<` `>` `?` `\` `|` を含んではならない。
  + 先頭、末尾が[空白文字](#whitespace)であってはならない。
    また、[空白文字](#whitespace)単体、および空文字列であってはならない。
  +  [ASCII文字大小無視][ascii-case-insensitive]で、次のいずれにも一致しない:
    `CON` `PRN` `AUX` `CLOCK$` `NUL` `COM1` `COM2` `COM3` `COM4` `COM5` `COM6` `COM7` `COM8` `COM9`
    `LPT1` `LPT2` `LPT3` `LPT4` `LPT5` `LPT6` `LPT7` `LPT8` `LPT9`
  + [NFC]適用後に、変化する文字列であってはならない。
* <a name="hint-string">「ヒントに利用される文字列」</a> は、文字数や先頭・末尾の文字・文字列を
  ゲームの参加者に開示する際に利用される。また、その文字数はお手付きの判定にも利用される。
* <a name="controls">「制御文字」</a>とは、[Unicode Other カテゴリ (\\p{C})][tr44] に属する文字である。
* <a name="whitespace">「空白文字」</a>とは、[Unicode Separator カテゴリ (\\p{Z})][tr44] に属する文字である。
* <a name="combining">「結合文字」</a>とは、[Unicode Mark カテゴリ (\\p{M})][tr44] に属する文字である。
* <a name="replaceable-katakana">「ひらがな化可能なカタカナ」</a>とは、`ァ` (U+30A1) 〜 `ヶ` (U+30F6) である。
* 文字列中の<a name="katakana-to-hiragana">「カタカナをひらがな化」</a>するときは、各文字に対して、
  それが[ひらがな化可能なカタカナ](#replaceable-katakana)であるなら、
  `ぁ` (U+3041) 〜 `ゖ` (U+3096) の対応する文字に置き換える。
* <a name="integer">「整数」</a>とは、正規表現 `/^(0|-?[1-9][0-9]*)$/` に一致する10進数である。
* <a name="real-number">「実数」</a>とは、
  正規表現 `/^((0|-?[1-9][0-9]*)|-?(0|[1-9][0-9]*)\.[0-9]*[1-9])$/` に一致する10進数である。

[ascii-digits]: http://www.hcn.zaq.ne.jp/___/WEB/HTML-infrastructure-ja.html#lowercase-ascii-letters
[lowercase-ascii-letters]: http://www.hcn.zaq.ne.jp/___/WEB/HTML-infrastructure-ja.html#lowercase-ascii-letters
[domain]: http://www.hcn.zaq.ne.jp/___/WEB/URL-ja.html#syntax-host-domain
[ascii-case-insensitive]: http://www.hcn.zaq.ne.jp/___/WEB/DOM4-ja.html#ascii-case-insensitive
[NFC]: https://wiki.suikawiki.org/n/NFC
[tr44]: http://www.unicode.org/reports/tr44/#GC_Values_Table

<a name="base">基底となるファイル形式</a>
--------------------------------------------------------------------------------
* ファイル形式は、[RFC4180]、[RFC7111]に準拠した[CSV]でなければならない。
  同仕様は[RFC4180、RFC7111の解釈](#rfc4180-7111)に沿って解釈する。
* 符号化方式は[UTF-8]でなければならない。
* カンマ `,`、または改行コード (CRLF) を含むフィールドは引用符 `"` で括らなければならない。
* フィールド名は空文字列であってはならない。
* レコード数、およびフィールド数に制限はないが、実装は受け入れるレコード数、フィールド数を制限してもよい。
* フィールド長に制限はないが、実装は受け入れるフィールド長を制限してもよい。

[RFC4180]: http://www.7key.jp/rfc/rfc4180.html
[RFC7111]: https://tools.ietf.org/html/rfc7111
[CSV]: https://ja.wikipedia.org/wiki/Comma-Separated_Values
[UTF-8]: https://ja.wikipedia.org/wiki/UTF-8

<a name="fields">各フィールドの構造</a>
--------------------------------------------------------------------------------
フィールドの値が空文字列である場合、対応するフィールド名のフィールドは、
該当レコードには出現しないものとして扱わなければならない。

名称が違うフィールド同士の位置 (順序) 関係に意味はない。
同名フィールド同士の位置関係は有意である場合があるため、同名フィールド同士の再出力時に位置関係を変更してはならない。

同名フィールドのうち、一部のフィールドが空文字列である場合、
同名フィールド同士のインデックス (位置関係) で、空文字列フィールドの位置は詰めて扱われなければならない。

* `text`
  + 1レコードに1個。
  + レコードに関連付けられる文字列。主に「正しい表記の答え」を表す。
  + `answer` フィールドが1個も存在しない場合、`answer` フィールドの規則に違反しない文字列でなければならない。
* `image`
  + 1レコードに0〜1個。
  + [ファイル所在](#file-location)を指定する。
  + レコードに関連付けられる画像ファイル名。
    主に「モザイク画像当てクイズにおける画像」「`description` の一部となる画像」を表す。
* `audio`
  + 1レコードに0〜1個。
  + [ファイル所在](#file-location)を指定する。
  + レコードに関連付けられる音声ファイル名。主に「イントロクイズ」「`description` の一部となる音声」を表す。
* `video`
  + 1レコードに0〜1個。
  + [ファイル所在](#file-location)を指定する。
  + レコードに関連付けられる動画ファイル名。主に「`description` の一部となる動画」を表す。
* `image-source` `audio-source` `video-source`
  + それぞれ1レコードに0〜1個。
    これらのフィールドが存在する場合、対応する `image` `audio` `video` フィールドが存在しなければならない。
  + [出所を記述するためのCommonMark](#source-commonmark)で記述する。
  + 主に「対応するフィールドで指定されたファイルが引用である」ことを表す。
  + フィールドの値は、対応するフィールドで指定されたファイルの出所でなければならない。
* `answer`
  + 1レコードに0個以上。
  + 主に「ゲームの参加者が答えとして入力する文字列」を表す。
  + 1個目のフィールドは[ヒントに利用される文字列](#hint-string)となる。
    1個目のフィールドは[正規表現文字列](#regexp)であってはならない。
  + このフィールドが1個も存在しない、かつ `type` フィールドに `selection` が指定されていない場合
    `text` フィールド値が1個目の `answer` フィールド値として利用される。
  + [NFKC]適用後に、変化する文字列であってはならない。
  + [制御文字](#controls)、[空白文字](#whitespace)、[結合文字](#combining)を含んではならない。
  + ゲーム中では[ASCII小文字化][ascii-lowercase]、[カタカナをひらがな化](#katakana-to-hiragana)、
    [同一視する文字](#equivalent)表の置換前列の文字を置換後列の文字に置き換えて扱わなければならない。
  + `type` フィールドに `selection` が指定されている場合、`option` フィールドのいずれかと同一の文字列でなければならない。 
  + 先頭、および末尾の文字がソリダス `/` である場合、<a name="regexp">正規表現文字列</a>として扱う。
    ソリダス `/` 単体のフィールドは正規表現文字列とみなさない。
    - 拡張正規表現として妥当な文字列でなければならない。
    - POSIX文字クラスなど、角括弧で括られた部分の内部で角括弧を用いる構文を使用してはならない。
    - [ASCII大文字][uppercase-ascii-letters]、[ひらがな化可能なカタカナ](#replaceable-katakana)を含んではならない。
    - スリダス `/` は文字クラス内を含め、すべてエスケープしなければならない。
    - 補助文字を使用してはならない。
    - [HTMLのpattern属性][pattern]のように、全体に一致するものとして扱われる。
  + [正規表現文字列](#regexp)は使用すべきでない。
  + 辞書自体のロケールに依って、さらに以下の規則を追加する。
    - ja
      - `〜` (U+301C)、`ぁ` (U+3041) 〜 `ゔ` (U+3094)、`ァ` (U+30A1) 〜 `ヴ` (U+30F4)、`ー` (U+30FC) のみを含むべきである。
* `description`
  + 1レコードに0〜1個。
  + [CommonMark]で記述する。
  + `text`、`image`、`audio`、`video` の解説。
* `weight`
  + 1レコードに0〜1個。
  + 0より大きい[実数](#real-number)を指定する。1を既定値とする出題率。
* `specifics`
  + 1レコードに0〜1個。
  + レコードに対する追加設定を[application/x-www-form-urlencoded]形式で記述する。
  + [specificsレコードの名前](#specifics)を参照。
* `question`
  + 1レコードに0〜1個。
  + 問題文。
* `option`
  + 1レコードに0個以上。
  + 選択肢。
  + `answer` フィールドの規則に違反しない文字列でなければならない。
  + `type` フィールドに `selection` が指定されている、かつ `answer` フィールドが1個も存在しない場合、
    該当レコードは並べ替え問題として扱われ、このフィールド値の順序が正解として利用される。
* `type`
  + 1レコードに0〜1個。
  + 並べ替え問題など、選択肢が表示されていなければ問題が成立しない場合に、値として `selection` を指定する。

[NFKC]: https://wiki.suikawiki.org/n/NFKC
[uppercase-ascii-letters]: http://www.hcn.zaq.ne.jp/___/WEB/HTML-infrastructure-ja.html#uppercase-ascii-letters
[ascii-lowercase]: http://www.hcn.zaq.ne.jp/___/WEB/URL-ja.html#ascii-lowercase
[application/x-www-form-urlencoded]: http://www.hcn.zaq.ne.jp/___/WEB/URL-ja.html#application/x-www-form-urlencoded
[pattern]: https://developer.mozilla.org/docs/Web/HTML/Element/Input#attr-pattern
[CommonMark]: http://spec.commonmark.org/0.22/
[url-absolute]: http://www.hcn.zaq.ne.jp/___/WEB/URL-ja.html#syntax-url-absolute
[url-absolute-with-fragment]: http://www.hcn.zaq.ne.jp/___/WEB/URL-ja.html#syntax-url-absolute-with-fragment

### <a name="meta-fields">メタフィールド</a>
メタフィールドは、レコードではなく辞書自体に関連付けられるフィールドであり、フィールド名は `@` で始まる。
メタフィールドは、ファイルの最初のレコードにのみ出現し、他のレコードに出現してはならない。

* `@title`
  + 辞書自体のタイトル。
  + このフィールドが存在しない場合、[拡張子を除くファイル名](#filename-without-extension)を辞書のタイトルとして利用する。
* `@summary`
  + [CommonMark]で記述する。
  + 辞書自体の説明文。
* `@regard`
  + 正規表現における文字クラスで指定する。つまり、角括弧 `[` `]` で括られた文字列を指定する。
  + コメントがどのような文字列のみで構成されていればお手付きと判断するかを決める。
  + 省略された場合の既定値は `[〜ぁ-ゔー]` である。

### <a name="restrict-commonmark">CommonMarkの制限</a>
* 全体をセクショニングコンテンツの直下に置かれなければならない。
  また、見出し要素が存在し、かつ最初の見出し要素よりも前にノードが存在すれば、
  最初の見出し以降をsection要素に内包しなければならない。
* 次の要素のみが含まれていなければならない。属性はグローバル属性を除いて、括弧内の属性のみ許可される。
  この規則は、HTMLへの変換が行われたコードに対して適用される:
  a (href), abbr, audio (src), b, bdi, bdo, blockquote (cite), br,
  caption, cite, code, col (span), colgroup (span), dd, del (cite, datetime), dfn, div, dl, dt, em,
  figcaption, figure, h1, h2, h3, h4, h5, h6, hr, i, img (alt, height, src, width), ins (cite, datetime), kbd, li,
  ol (reversed, start, type), p, pre, q (cite), rp, rt, ruby, s, samp, small, span, strong, sub, sup,
  table, tbody, td (colspan, rowspan), tfoot, th (abbr, colspan, rowspan, scope), thead, time (datetime),
  tr, u, ul, var, video (height, src, width), wbr
  - 次のグローバル属性が許可される: dir, lang, title, translate
  - href属性、cite属性の値は、[絶対URL][url-absolute]、または[素片付き絶対URL][url-absolute-with-fragment]
	でなければならない。また、スキームは `https`、または `http` でなければならない。
  - src属性の値は、[ファイル所在](#file-location)でなければならない。

### <a name="source-commonmark">出所を記述するためのCommonMark</a>
* セクショニングコンテンツの直下に置かれなくてもよい。
* 次の要素のみが含まれていなければならない。属性はグローバル属性を除いて、括弧内の属性のみ許可される。
  この規則は、HTMLへの変換が行われたコードに対して適用される:
  a (href), b, bdi, bdo (dir), br, cite, i, p, rp, rt, ruby, sub, sup, time (datetime), u, wbr
  - 次のグローバル属性が許可される: dir, lang, title, translate
  - href属性の値は、[絶対URL][url-absolute]、または[素片付き絶対URL][url-absolute-with-fragment]
	でなければならない。また、スキームは `https`、または `http` でなければならない。

### <a name="specifics">specificsフィールドの名前</a>
名前が違う組同士の順序に意味はない。
同名の組同士の順序は有意である場合があるため、同名の組同士の順序を変更してはならない。

| 名前               | 1レコード中の数 | 値                                |                                                                |
|--------------------|-----------------|-----------------------------------|----------------------------------------------------------------|
| no-pixelization    | 0〜1個。        | 空文字列。                        | 指定されていれば、画像のモザイク処理 (ピクセル化) を行わない。 |
| magnification      | 0〜1個。        | 0より大きい[実数](#real-number)。 | 画像の拡大率。                                                 |
| last-magnification | 0〜1個。        | 0より大きい[実数](#real-number)。 | この名前の組を指定する場合は、magnificationの指定も必須。解答時間が残り0秒になったときの、画像の拡大率。解答時間が減るにつれて拡大率を変動させたい場合に指定。 |
| start              | 0〜1個。        | 0以上の[実数](#real-number)。     | 音声、動画ファイルの再生開始位置。秒数で指定。                 |
| repeat             | 0〜1個。        | 1以上の[整数](#integer)。         | 音声、動画ファイルを再生回数。                                 |
| length             | 0〜1個。        | 0より大きい[実数](#real-number)。 | 音声、動画ファイルを再生開始位置から何秒間再生するか。         |
| speed              | 0〜1個。        | 0より大きい[実数](#real-number)。 | 音声、動画ファイルの再生速度。何倍速とするか。                 |
| volume             | 0〜1個。        | 0より大きい[実数](#real-number)。 | 音声、動画ファイルの音量。基準を1として何倍とするか。          |
| require-all-right  | 0〜1個。        | 空文字列。                        | 選択問題で複数選択する必要がある場合に指定。                   |
| score              | 0〜1個。        | 1以上の[整数](#integer)。         | 該当レコードの問題の得点。                                     |
| last-score         | 0〜1個。        | 1以上の[整数](#integer)。         | この名前の組を指定する場合は、scoreの指定も必須。解答時間が残り0秒になったときの、問題の得点。解答時間が減るにつれて得点を変動させたい場合に指定。 |
| no-random          | 0〜1個。        | 空文字列。                        | 指定されていれば、選択問題で選択肢をシャッフルしない。         |
| bonus              | 0個〜該当レコードの `answer` フィールドの数。 | [整数](#integer)。 | 対応する `answer` フィールドの値を回答した場合の上乗せ点数。 |

### <a name="header">ヘッダ行</a>
* [画像・音声・動画ファイルを含む場合のファイル形式](#with-image-audio-video)中のCSVファイルは、
  ヘッダ行が存在しなければならない。
* headerパラメータを含むMIMEタイプが提供されない場合、
  [画像・音声・動画ファイルを含む場合のファイル形式](#with-image-audio-video)中のCSVファイルであるか
  もしくは1番目の行が `text` と一致するフィールドを含むならヘッダ行が存在するとみなし、
  そうでなければヘッダ行が存在しないとみなさなければならない。
* ヘッダ行が存在しない場合、レコード中の最初のフィールドを `text` フィールド、
  2個目以降を `answer` フィールドして扱わなければならない。

<a name="with-image-audio-video">画像・音声・動画ファイルを含む場合のファイル形式</a>
--------------------------------------------------------------------------------
* ファイル形式はZIPでなければならない。圧縮を行う場合は、Deflateアルゴリズムを使用しなければならない。
* アーカイブにパスワードを設定してはならない。
* アーカイブ中には[基底となるファイル形式](#base)で規定するCSVファイルが1つ、およびこのセクションで規定する
  画像・動画・音声ファイルが1つ以上含まれていなければならない。それ以外のファイル、およびフォルダを含めてはならない。
  これらのファイルの拡張子は、表[拡張子](#extension)で示すものでなければならない。
* アーカイブ中のCSVファイルの名前は `dictionary.csv` でなければならない。
* アーカイブ中のファイルの[拡張子を除くファイル名](#filename-without-extension)は、次の規則に合致しなければならない。
  + [ASCII数字][ascii-digits]、[ASCII英小文字][lowercase-ascii-letters]、ハイフンマイナス `-`、ローライン `_` のみを含む。
  + 先頭の文字がハイフンマイナスではない。
  + 次のいずれにも一致しない:
    `con` `prn` `aux` `nul` `com1` `com2` `com3` `com4` `com5` `com6` `com7` `com8` `com9`
    `lpt1` `lpt2` `lpt3` `lpt4` `lpt5` `lpt6` `lpt7` `lpt8` `lpt9`
  + 長さは1〜26文字。
* アーカイブ全体の圧縮後の容量は2GiB以下、非圧縮容量は4GiB以下でなければならない。
  また、圧縮後の容量は512MiB以下にするべきである。実装は受け入れる容量を制限してもよい。
* アーカイブ中のファイル数は10000個以下でなければならない。また、2000個以下にするべきである。
  実装は受け入れるファイル数を制限してもよい。

### <a name="extension">拡張子</a>
| ファイル形式            | 拡張子              |
|-------------------------|---------------------|
| CSV                     | `csv`               |
| PNG                     | `png`               |
| JFIF                    | `jpg` または `jpeg` |
| SVG                     | `svg`               |
| 音声ファイルとしてのMP4 | `mp4` または `m4a`  |
| MP3                     | `mp3`               |
| 動画ファイルとしてのMP4 | `mp4`               |

### <a name="image">画像ファイルの形式</a>
* 画像ファイルの形式は、PNG、JFIF、SVG のいずれかでなければならない。
* ファイルの容量は1MiB以下でなければならない。また、100KiB以下にするべきである。実装は受け入れる容量を制限してもよい。
* 画像の幅、および高さは、それぞれ1000px以下にするべきである。実装は受け入れる幅、高さを制限してもよい。

#### <a name="restrict-svg">SVGの制限</a>
* すべての要素はSVG名前空間 `http://www.w3.org/2000/svg` に属していなければならない。
* 属性は名前空間に属さないか、XLink名前空間 `http://www.w3.org/1999/xlink` に属していなければならない。
* 符号化方式は[UTF-8]でなければならない。
* 外部参照を含んではならない。外部参照とは、IRI参照が許容される要素とプロパティにおける文書外のURLの指定、
  XMLにおけるxml-stylesheet処理命令、CSSにおける\<url>型の使用
  (および\<url>型の代わりに\<string>型による表現も許容される文脈における\<string>型の使用) である。
* スクリプトを使用してはならない。スクリプトの使用とは、script要素、またはイベント属性を含むことである。
* アニメーションさせてはならない。アニメーションさせるとは、アニメーション要素、
  またはCSSアニメーション、セレクタにおける動的な疑似クラスを使用することである。

### <a name="audio">音声ファイルの形式</a>
* 音声ファイルの形式は、MP4 (AAC)、MP3のいずれかでなければならない。
* ファイルの容量は16MiB以下でなければならない。また、1MiB以下にするべきである。実装は受け入れる容量を制限してもよい。

### <a name="video">動画ファイルの形式</a>
* 動画ファイルの形式は、MP4 (H.264+AAC) でなければならない。
* ファイルの容量は16MiB以下でなければならない。また、1MiB以下にするべきである。実装は受け入れる容量を制限してもよい。
* 画像の幅、および高さは、それぞれ1000px以下にするべきである。実装は受け入れる幅、高さを制限してもよい。

<a name="implementations">実装に対する要件</a>
--------------------------------------------------------------------------------
* 受け入れるフィールド長や容量などを制限する場合、それを明示しなければならない。
  + 容量は[バイト][byte]数で表さなければならず、[SI接頭辞][si-prefixes]を使用してはならない。
    必要があれば、[2進接頭辞][binary-prefix]を使用する。
  + 実装側で設定した制限を超えた部分を除去した場合、
    除去が行われた辞書データを基に再出力された辞書ファイルは、元の辞書ファイルとは異なる辞書として扱うべきである。
* 「ゲームの参加者が入力した文字列」についてお手付きや正誤の判定を行う場合、
  [NFKC]を適用した後、[ASCII小文字化][ascii-lowercase]、[カタカナをひらがな化](#katakana-to-hiragana)、
  [同一視する文字](#equivalent)表の置換前列の文字を置換後列の文字に置き換えて扱わなければならない。
* 辞書ファイルを再出力する際、未知のフィールド名のフィールドを除去してはならない。
  また、同名フィールドの出現数が制限されている場合も、制限を超えた部分のフィールドを除去してはならない。
* 辞書ファイルを再出力する際、specificsフィールドの未知の名前の組を除去してはならない。
  また、同名の組の出現数が制限されている場合も、制限を超えた部分の組を除去してはならない。

[byte]: http://www.hcn.zaq.ne.jp/___/WEB/Encoding-ja.html#byte
[si-prefixes]: https://ja.wikipedia.org/wiki/SI%E6%8E%A5%E9%A0%AD%E8%BE%9E
[binary-prefix]: https://ja.wikipedia.org/wiki/2%E9%80%B2%E6%8E%A5%E9%A0%AD%E8%BE%9E

### <a name="equivalent">同一視する文字</a>
| 置換前        | 置換後        |
|---------------|---------------|
| `~` (U+007E)  | `〜` (U+301C) |
| `'` (U+0027)  | `’` (U+2019) |
| `"` (U+0022)  | `”` (U+201D) |
| `“` (U+201C) | `”` (U+201D) |

<a name="rfc4180-7111">[RFC4180]、[RFC7111]の解釈</a>
--------------------------------------------------------------------------------
RFC4180の章 [2. Definition of the CSV Format] の矛盾する点について、以下のように解釈する。

* 「6. Fields containing line breaks (CRLF), double quotes, and commas _should_ be enclosed in double-quotes.」は、
  「The ABNF grammar」の `non-escaped = *TEXTDATA` を優先し、 _should_ ではなく _must_ とする。
* 「The ABNF grammar」の規則について、以下のように変更する。これらの変更を適用した「The ABNF grammar」を後述する。
  - `file = [header CRLF] record *(CRLF record) [CRLF]` は、ファイル末尾の改行と空文字列フィールドを区別できないため、
    `file = [header CRLF] *(record CRLF) (empty-record CRLF / non-empty-record [CRLF])` とし、
    `non-empty-record = (non-empty-field / field 1*(COMMA field))` `empty-record = empty-field`
    `non-empty-field = (escaped / non-empty-non-escaped)` `empty-field = empty-non-escaped`
    `non-empty-non-escaped =  1*TEXTDATA` `empty-non-escaped = 0TEXTDATA` を追加する。
  - `escaped = DQUOTE *(TEXTDATA / COMMA / CR / LF / 2DQUOTE) DQUOTE` は、
   「6. Fields containing line breaks (CRLF), double quotes, and commas should be enclosed in double-quotes.」が
    CRLF改行のみを想定していると解釈し、`escaped = DQUOTE *(TEXTDATA / COMMA / CRLF / 2DQUOTE) DQUOTE` とする。
  - `TEXTDATA =  %x20-21 / %x23-2B / %x2D-7E` は、RFC7111「[5.1. The text/csv media type]」の
    「Any charset defined by IANA for the "text" tree may be used in conjunction with the "charset" parameter.」から
    [ASCII符号位置][ascii-code-point]以外の文字も扱えると考えられるため、
    `TEXTDATA =  %x20-21 / %x23-2B / %x2D-7E / %xA0-10FFFD` とする。

```
file = [header CRLF] *(record CRLF) (empty-record CRLF / non-empty-record [CRLF])

header = name *(COMMA name)

record = field *(COMMA field)

non-empty-record = (non-empty-field / field 1*(COMMA field))

empty-record = empty-field

name = field

field = (escaped / non-escaped)

non-empty-field = (escaped / non-empty-non-escaped)

empty-field = empty-non-escaped

escaped = DQUOTE *(TEXTDATA / COMMA / CRLF / 2DQUOTE) DQUOTE

non-escaped = *TEXTDATA

non-empty-non-escaped =  1*TEXTDATA

empty-non-escaped = 0TEXTDATA

COMMA = %x2C

CR = %x0D ;as per section 6.1 of RFC 2234 [2]

DQUOTE =  %x22 ;as per section 6.1 of RFC 2234 [2]

LF = %x0A ;as per section 6.1 of RFC 2234 [2]

CRLF = CR LF ;as per section 6.1 of RFC 2234 [2]

TEXTDATA =  %x20-21 / %x23-2B / %x2D-7E / %xA0-10FFFD
```

[2. Definition of the CSV Format]: https://tools.ietf.org/html/rfc4180#section-2
[5.1. The text/csv media type]: https://tools.ietf.org/html/rfc7111#section-5.1
[ascii-code-point]: http://www.hcn.zaq.ne.jp/___/WEB/Encoding-ja.html#ascii-code-point

<a name="example">辞書ファイルの例</a>
--------------------------------------------------------------------------------
### 簡単な形

```csv
たいよう
ちきゅう
カロン
```

| text     |
|----------|
| たいよう |
| ちきゅう |
| カロン   |

### 画像や解説などを含む形

```csv
text,image,answer,answer,description,@title,@summary
太陽,sun.png,たいよう,おひさま,恒星。,天体,恒星、惑星、衛星などのリスト。
地球,earth.png,ちきゅう,,惑星。,,
カロン,charon.png,,,"冥王星の衛星。

> カロンは1978年6月22日にアメリカの天文学者ジェームズ・クリスティーによって発見された。
> その後、冥王星が冥府の王プルートーの名に因むことから、
> この衛星はギリシア神話の冥府の川・アケローンの渡し守カローンにちなんで「カロン」と命名された。
> なおクリスティーは当初から一貫してCharonの「char」を
> 妻シャーリーン（Charlene） のニックネーム「シャー（Char）」と同じように発音していたため、
> これが英語圏で定着して「シャーロン」と呼ばれるようになった。
引用元: [カロン (衛星) - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%AB%E3%83%AD%E3%83%B3_(%E8%A1%9B%E6%98%9F))",,
```

| text   | image      | answer   | answer   | description | @title | @summary                       |
|--------|------------|----------|----------|-------------|--------|--------------------------------|
| 太陽   | sun.png    | たいよう | おひさま | 恒星。      | 天体   | 恒星、惑星、衛星などのリスト。 | 
| 地球   | earth.png  | ちきゅう |          | 惑星。      |        |                                |
| カロン | charon.png |          |          | "冥王星の衛星。<br><br>> カロンは1978年6月22日にアメリカの天文学者ジェームズ・クリスティーによって発見された。<br>> その後、冥王星が冥府の王プルートーの名に因むことから、<br>> この衛星はギリシア神話の冥府の川・アケローンの渡し守カローンにちなんで「カロン」と命名された。<br>> なおクリスティーは当初から一貫してCharonの「char」を<br>> 妻シャーリーン（Charlene） のニックネーム「シャー（Char）」と同じように発音していたため、<br>> これが英語圏で定着して「シャーロン」と呼ばれるようになった。<br>引用元: \[カロン (衛星) - Wikipedia](https\://ja.wikipedia.org/wiki/%E3%82%AB%E3%83%AD%E3%83%B3_(%E8%A1%9B%E6%98%9F))" | |

### 音声ファイルを含む形

```csv
text,audio,specifics,answer,answer,@title
四季,four-seasons.mp4,start=50&length=3,しき,はる,クラシック音楽
魔王,erlking.mp4,,まおう,,,
ラデツキー行進曲,radetzky-march.mp4,,らでつきーこうしんきょく,,,
```

| text             | audio              | specifics         | answer                   | answer | @title         |
|------------------|--------------------|-------------------|--------------------------|--------|----------------|
| 四季             | four-seasons.mp4   | start=50&length=3 | しき                     | はる   | クラシック音楽 |
| 魔王             | erlking.mp4        |                   | まおう                   |        |                | 
| ラデツキー行進曲 | radetzky-march.mp4 |                   | らでつきーこうしんきょく |        |                |

### お題の出題頻度の設定 (重み付け)

```csv
text,weight,description,@title
よめ,3,このお題は他のお題の3倍出題されやすい。,ポケモン
ピカチュウ,,,
カイリュー,,,
ヤドラン,,,
ミュウ,0.5,このお題は他のお題より出題されにくい。,
```

| text       | weight | description                             | @title   |
|------------|--------|-----------------------------------------|----------|
| よめ       | 3      | このお題は他のお題の3倍出題されやすい。 | ポケモン |
| ピカチュウ |        |                                         |          |
| カイリュー |        |                                         |          |
| ヤドラン   |        |                                         |          |
| ミュウ     | 0.5    | このお題は他のお題より出題されにくい。  |          |

### 選択・並べ替え問題

以降、CSVを省略して表としての例のみ示す。

| text                             | image   | question                                                     | option | option | option | option   | answer   | answer | specifics          | type      | description                                        | @title             |
|----------------------------------|---------|--------------------------------------------------------------|--------|--------|--------|----------|----------|--------|--------------------|-----------|----------------------------------------------------|--------------------|
| 太陽                             | sun.png |                                                              | 太陽   | 地球   | カロン |          | たいよう |        |                    |           | 最初の選択肢 (ここでは「太陽」) を答えとして扱う。 | 選択・並べ替え問題 |
| 林檎                             |         | 仲間外れはどれでしょう                                       | リンゴ | ゴリラ | ラクダ | ダチョウ | リンゴ   |        |                    | selection | 選択肢を表示しなければ問題が成立しない場合。       |                    |
| 林檎かパン                       |         | 食べ物はどれでしょう (答えが複数ある場合はどれが1つだけ選択) | リンゴ | ゴリラ | ラッパ | パン     | リンゴ   | パン   |                    | selection |                                                    |                    |
| 食べ物                           |         | 同じ種類のものを選びましょう                                 | リンゴ | ゴリラ | ラッパ | パン     | リンゴ   | パン   | require-all-right= | selection |                                                    |                    |
| リンゴ→ゴリラ→ラクダ→ダチョウ |         | しりとりが成立するように並べ替えてください                   | リンゴ | ゴリラ | ラクダ | ダチョウ |          |        |                    | selection | answerが存在しなければ並べ替え問題として扱う。     |                    |

ゲームの種類や難易度設定などに応じて、選択肢を表示したりしなかったりする問題 (上の「太陽」の例) が作成可能である。

### ひらがな以外で答える辞書・正規表現を含む辞書

| text        | answer   | answer         | @title | @regard             |
|-------------|----------|----------------|--------|---------------------|
| title       | たいとる |                | 英単語 | [a-z〜ぁ-ゔー]      |
| description |          |                |        |                     |
| text        | text     | /て[くき]すと/ |        |                     |

ひらがな以外で答える辞書の場合、お手付き判定のため、
以上の例の `@regard` フィールドのように、`answer` フィールドに登場しうる文字の指定が必要になる。

<a name="contribution">参加・協力</a>
--------------------------------------------------------------------------------
本仕様について、意見や要望、不具合などがあれば、Issue、または Pull Request を利用していただきたい。

<a name="licence">ライセンス</a>
--------------------------------------------------------------------------------
[Creative Commons — 表示 4.0 国際 — CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.ja)
