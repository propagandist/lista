# 第三者のソフトウェア

`ghcr.io/propagandist/lista` のイメージに含まれる、株式会社 PROPAGANDIST 以外が権利を持つソフトウェアの一覧です。それぞれのライセンスに従います。[LICENSE](LICENSE) の制限は、ここに挙げたソフトウェアには掛かりません（LICENSE 第 5 条）。

この一覧は、公開する版のイメージから作ります（[docs/release-check.md](docs/release-check.md)）。★ 下書きは 0.9.1 の中身で作った。公開する版のイメージで作り直す

## OS と Java の実行環境

イメージは `eclipse-temurin:17-jre-noble`（Ubuntu 24.04 と Eclipse Temurin 17 の JRE）の上に作っています。

- **Ubuntu のパッケージ**: ライセンスは、イメージの `/usr/share/doc/<パッケージ>/copyright` にあります。ソースコードは Ubuntu のアーカイブ（`apt-get source`）から取得できます
- **Eclipse Temurin 17 の JRE**: GPL-2.0 with Classpath Exception です。ライセンスと同梱物の表示は `/opt/java/openjdk/legal/` にあります。ソースコードは Eclipse Adoptium（https://adoptium.net/）から取得できます

## Typst（月の記録の PDF）

イメージの `/usr/local/bin/typst` は、Typst の公式の配布物（0.15.1。`typst-x86_64-unknown-linux-musl` または `typst-aarch64-unknown-linux-musl`）を、そのまま入れたものです。Lista は Typst を別のプロセスとして呼びます。

- **Typst 本体**: Apache-2.0。ライセンスと NOTICE は、イメージの `/usr/share/doc/typst/LICENSE`・`NOTICE` にあります。ソースコードは https://github.com/typst/typst の `v0.15.1` です
- **埋め込みのフォント**（`typst-assets` 0.15.1）: ライセンスの本文は、イメージの `/usr/share/doc/typst/typst-assets-NOTICE` にあります。ソース（フォントのファイル）は https://github.com/typst/typst-assets の `v0.15.1` です
  - Libertinus Serif: SIL Open Font License 1.1
  - New Computer Modern（`NewCM10-Regular` を除く）・DejaVu Sans Mono: GUST Font License 1.0
  - `NewCM10-Regular`: GPL-3.0-or-later に Font Exception と Distribution Exception を付けたもの。改変せずに入れています
  - Foxit（PDFium）: BSD-3-Clause
  - Lista は、これらのフォントを使いません（同梱の BIZ UDPGothic だけを使います）
- **静的にリンクされたライブラリ**:
  - OpenSSL 3.4.0: Apache-2.0。ソースコードは https://www.openssl.org/source/ から取得できます
  - liblzma（XZ Utils 5.2 系。`lzma-sys` 0.1.20 が同梱）: パブリックドメイン。ソースコードは https://tukaani.org/xz/ から取得できます
  - musl libc: MIT
- **Rust の crate**（318 件）: 公式の配布物と同じ条件（`--features self-update,vendor-openssl`、`*-unknown-linux-musl`）で依存を解決し、バイナリにリンクされるものを挙げます。ビルドの時だけ使うものと proc-macro は除きます。ソースコードは crates.io の同じ名前と版から取得できます。ライセンスの欄は、各 crate の宣言のままです
  - MPL-2.0 の `option-ext` は、改変せずに使われています。ソースコードは https://crates.io/crates/option-ext の 0.2.0 です

#### MIT OR Apache-2.0（154 件）

`anstream 0.6.18`, `anstyle 1.0.10`, `anstyle-parse 0.2.6`, `anstyle-query 1.1.2`, `arrayvec 0.7.6`, `az 1.2.1`, `base64 0.22.1`, `biblatex 0.12.0`, `bitflags 2.11.1`, `bumpalo 3.20.2`, `cfg-if 1.0.0`, `chrono 0.4.39`, `chunked_transfer 1.5.0`, `citationberg 0.7.0`, `clap 4.5.28`, `clap_builder 4.5.27`, `clap_complete 4.5.44`, `clap_lex 0.7.4`, `cobs 0.3.0`, `color-print 0.3.7`, `colorchoice 1.0.3`, `comemo 0.5.1`, `crc32fast 1.5.0`, `crossbeam-deque 0.8.6`, `crossbeam-epoch 0.9.18`, `crossbeam-utils 0.8.21`, `data-url 0.3.1`, `deranged 0.3.11`, `dirs 6.0.0`, `dirs-sys 0.5.0`, `ecow 0.2.6`, `either 1.13.0`, `env_proxy 0.4.1`, `errno 0.3.10`, `euclid 0.22.14`, `fdeflate 0.3.7`, `filetime 0.2.28`, `flate2 1.1.2`, `font-types 0.11.3`, `foreign-types 0.3.2`, `foreign-types-shared 0.1.1`, `form_urlencoded 1.2.1`, `getrandom 0.2.15`, `getrandom 0.3.1`, `gif 0.14.2`, `glidesort 0.1.2`, `guillotiere 0.7.0`, `half 2.4.1`, `hashbrown 0.16.1`, `hashbrown 0.17.1`, `hayagriva 0.10.1`, `httpdate 1.0.3`, `hypher 0.1.5`, `iana-time-zone 0.1.61`, `idna 1.0.3`, `image 0.25.10`, `image-webp 0.2.4`, `is_terminal_polyfill 1.70.1`, `itoa 1.0.14`, `krilla 0.8.2`, `krilla-svg 0.8.1`, `libc 0.2.169`, `linked-hash-map 0.5.6`, `lock_api 0.4.12`, `log 0.4.29`, `lzma-sys 0.1.20`, `memmap2 0.9.5`, `native-tls 0.2.13`, `notify-types 2.0.0`, `num-bigint 0.4.6`, `num-conv 0.1.0`, `num-integer 0.1.46`, `num-traits 0.2.19`, `once_cell 1.20.2`, `openssl-probe 0.1.6`, `palette 0.7.6`, `parking_lot 0.12.3`, `parking_lot_core 0.9.10`, `pathdiff 0.2.3`, `pdf-writer 0.15.0`, `percent-encoding 2.3.2`, `png 0.18.1`, `polycool 0.4.0`, `postcard 1.1.3`, `powerfmt 0.2.0`, `ppv-lite86 0.2.20`, `psm 0.1.24`, `quick-error 2.0.1`, `rand 0.8.6`, `rand_chacha 0.3.1`, `rand_core 0.6.4`, `rayon 1.12.0`, `rayon-core 1.13.0`, `read-fonts 0.39.2`, `regex 1.11.1`, `regex-automata 0.4.9`, `regex-syntax 0.8.5`, `roxmltree 0.20.0`, `roxmltree 0.21.1`, `scopeguard 1.2.0`, `semver 1.0.25`, `serde 1.0.228`, `serde_core 1.0.228`, `serde_json 1.0.138`, `serde_path_to_error 0.1.20`, `serde_spanned 0.6.8`, `serde_yaml 0.9.34+deprecated`, `shell-escape 0.1.5`, `siphasher 1.0.1`, `skrifa 0.42.1`, `smallvec 1.15.1`, `stable_deref_trait 1.2.0`, `stacker 0.1.21`, `subsetter 0.2.6`, `tar 0.4.46`, `tempfile 3.16.0`, `terminal_size 0.4.1`, `thin-vec 0.2.18`, `thiserror 2.0.18`, `time 0.3.37`, `time-core 0.1.2`, `tiny_http 0.12.0`, `toml 0.8.19`, `toml_datetime 0.6.8`, `toml_edit 0.22.23`, `ttf-parser 0.25.1`, `two-face 0.4.3`, `typed-path 0.12.3`, `unic-langid 0.9.6`, `unic-langid-impl 0.9.6`, `unic-langid-macros 0.9.6`, `unicode-bidi 0.3.18`, `unicode-bidi-mirroring 0.4.0`, `unicode-ccc 0.4.0`, `unicode-math-class 0.1.0`, `unicode-normalization 0.1.24`, `unicode-properties 0.1.3`, `unicode-script 0.5.7`, `unicode-segmentation 1.12.0`, `unicode-vo 0.1.0`, `unicode-width 0.1.14`, `unscanny 0.1.0`, `ureq 2.12.1`, `url 2.5.4`, `wasmi 1.0.9`, `wasmi_collections 1.0.9`, `wasmi_core 1.0.9`, `wasmi_ir 1.0.9`, `weezl 0.1.12`, `write-fonts 0.48.1`, `xattr 1.4.0`, `xmp-writer 0.3.3`, `xz2 0.1.7`, `yaml-rust 0.4.5`

#### MIT（40 件）

`bincode 1.3.3`, `chinese-number 0.7.8`, `chinese-variant 1.1.5`, `color_quant 1.1.0`, `core_maths 0.1.1`, `enum-ordinalize 4.3.2`, `fancy-regex 0.16.2`, `float-cmp 0.9.0`, `fontconfig-parser 0.5.7`, `fontdb 0.23.0`, `fs_extra 1.3.0`, `imagesize 0.14.0`, `infer 0.19.0`, `is-docker 0.2.0`, `is-wsl 0.4.0`, `lipsum 0.9.1`, `mio 1.0.4`, `open 5.3.2`, `openssl-sys 0.9.116`, `phf 0.13.1`, `phf_shared 0.13.1`, `pico-args 0.5.0`, `plist 1.8.0`, `quick-xml 0.38.3`, `rgb 0.8.53`, `rust_decimal 1.36.0`, `rustybuzz 0.20.1`, `sigpipe 0.1.3`, `simd-adler32 0.3.7`, `slab 0.4.11`, `spin 0.9.8`, `strict-num 0.1.1`, `strsim 0.11.1`, `strum 0.27.2`, `syntect 5.3.0`, `typed-arena 2.0.2`, `unsafe-libyaml 0.2.11`, `winnow 0.7.1`, `xmlwriter 0.1.0`, `zip 8.0.0`

#### Apache-2.0 OR MIT（35 件）

`ascii 1.1.0`, `bit-set 0.8.0`, `bit-vec 0.8.0`, `color 0.3.3`, `equivalent 1.0.1`, `fastrand 2.3.0`, `fearless_simd 0.4.0`, `glifo 0.1.0`, `hayro 0.7.1`, `hayro-ccitt 0.3.0`, `hayro-cmap 0.1.0`, `hayro-interpret 0.7.0`, `hayro-jbig2 0.3.0`, `hayro-jpeg2000 0.3.5`, `hayro-postscript 0.1.0`, `hayro-svg 0.7.0`, `hayro-syntax 0.7.2`, `hayro-write 0.7.0`, `idna_adapter 1.2.2`, `indexmap 2.12.1`, `kurbo 0.13.1`, `linebender_resource_handle 0.1.1`, `peniko 0.6.1`, `portable-atomic 1.10.0`, `resvg 0.47.0`, `rustc-hash 2.1.1`, `simplecss 0.2.2`, `svgtypes 0.16.1`, `usvg 0.47.0`, `utf16_iter 1.0.5`, `utf8_iter 1.0.4`, `utf8parse 0.2.2`, `vello_common 0.0.8`, `vello_cpu 0.0.8`, `write16 1.0.0`

#### Apache-2.0（25 件）

`approx 0.5.1`, `ciborium 0.2.2`, `ciborium-io 0.2.2`, `ciborium-ll 0.2.2`, `codespan-reporting 0.11.1`, `codex 0.3.0`, `openssl 0.10.80`, `pixglyph 0.6.1`, `self-replace 1.5.0`, `typst 0.15.1`, `typst-assets 0.15.1`, `typst-bundle 0.15.1`, `typst-eval 0.15.1`, `typst-html 0.15.1`, `typst-kit 0.15.1`, `typst-layout 0.15.1`, `typst-library 0.15.1`, `typst-pdf 0.15.1`, `typst-realize 0.15.1`, `typst-render 0.15.1`, `typst-svg 0.15.1`, `typst-syntax 0.15.1`, `typst-timing 0.15.1`, `typst-utils 0.15.1`, `zopfli 0.8.3`

#### Unicode-3.0（22 件）

`icu_collator 2.2.0`, `icu_collator_data 2.2.0`, `icu_collections 2.2.0`, `icu_locale 2.2.0`, `icu_locale_core 2.2.0`, `icu_locale_data 2.2.0`, `icu_normalizer 2.2.0`, `icu_normalizer_data 2.2.0`, `icu_properties 2.2.0`, `icu_properties_data 2.2.0`, `icu_provider 2.2.0`, `icu_provider_blob 2.2.0`, `icu_segmenter 2.2.0`, `icu_segmenter_data 2.2.0`, `litemap 0.8.2`, `potential_utf 0.1.5`, `tinystr 0.8.3`, `writeable 0.6.3`, `yoke 0.8.2`, `zerofrom 0.1.8`, `zerotrie 0.2.4`, `zerovec 0.11.6`

#### Unlicense OR MIT（9 件）

`aho-corasick 1.1.3`, `byteorder 1.5.0`, `byteorder-lite 0.1.0`, `csv 1.3.1`, `csv-core 0.1.11`, `memchr 2.8.0`, `same-file 1.0.6`, `termcolor 1.4.1`, `walkdir 2.5.0`

#### Zlib（4 件）

`foldhash 0.2.0`, `libz-rs-sys 0.5.1`, `slotmap 1.0.7`, `zlib-rs 0.5.1`

#### Apache-2.0 WITH LLVM-exception OR Apache-2.0 OR MIT（3 件）

`linux-raw-sys 0.4.15`, `rustix 0.38.44`, `wasmparser 0.228.0`

#### BSD-2-Clause（3 件）

`arrayref 0.3.9`, `kamadak-exif 0.6.1`, `mutate_once 0.1.1`

#### BSD-3-Clause OR Apache-2.0（3 件）

`moxcms 0.8.1`, `pic-scale 0.7.9`, `pxfm 0.1.24`

#### MIT OR Apache-2.0 OR Zlib（3 件）

`tinyvec_macros 0.1.1`, `zune-core 0.5.1`, `zune-jpeg 0.5.15`

#### BSD-3-Clause（2 件）

`tiny-skia 0.12.0`, `tiny-skia-path 0.12.0`

#### ISC（2 件）

`inotify 0.11.0`, `inotify-sys 0.1.5`

#### Zlib OR Apache-2.0 OR MIT（2 件）

`bytemuck 1.25.0`, `tinyvec 1.8.1`

#### (MIT OR Apache-2.0) AND Unicode-3.0（1 件）

`unicode-ident 1.0.16`

#### 0BSD OR CC0-1.0（1 件）

`roman-numerals-rs 3.1.0`

#### 0BSD OR MIT OR Apache-2.0（1 件）

`adler2 2.0.0`

#### Apache-2.0  OR  MIT（1 件）

`fnv 1.0.7`

#### Apache-2.0 OR BSL-1.0（1 件）

`ryu 1.0.19`

#### BSD-2-Clause OR Apache-2.0 OR MIT（1 件）

`zerocopy 0.7.35`

#### CC0-1.0（1 件）

`notify 8.0.0`

#### MIT AND (MIT OR Apache-2.0)（1 件）

`libm 0.2.11`

#### MIT OR Apache-2.0 OR CC0-1.0（1 件）

`fast-srgb8 1.0.0`

#### MIT OR Zlib OR Apache-2.0（1 件）

`miniz_oxide 0.8.5`

#### MPL-2.0（1 件）

`option-ext 0.2.0`

## Java のライブラリ

イメージの `/opt/lista/app.jar` の中に、元の jar のまま入っています（`BOOT-INF/lib/`）。各 jar の `META-INF/` に、ライセンスと NOTICE があります。ソースコードは Maven Central の同じ座標の `-sources.jar` か、各プロジェクトのリポジトリから取得できます。

#### Apache-2.0（74 件）

- `com.fasterxml.jackson.core:jackson-annotations:2.21`
- `com.fasterxml.jackson.core:jackson-core:2.21.4`
- `com.fasterxml.jackson.core:jackson-databind:2.21.4`
- `com.fasterxml.jackson.datatype:jackson-datatype-jdk8:2.21.4`
- `com.fasterxml.jackson.datatype:jackson-datatype-jsr310:2.21.4`
- `com.fasterxml.jackson.module:jackson-module-kotlin:2.21.4`
- `com.fasterxml.jackson.module:jackson-module-parameter-names:2.21.4`
- `com.fasterxml:classmate:1.7.3`
- `com.github.stephenc.jcip:jcip-annotations:1.0-1`
- `com.github.victools:jsonschema-generator:4.38.0`
- `com.github.victools:jsonschema-module-jackson:4.38.0`
- `com.github.victools:jsonschema-module-swagger-2:4.38.0`
- `com.google.errorprone:error_prone_annotations:2.50.0`
- `com.nimbusds:content-type:2.2`
- `com.nimbusds:lang-tag:1.7`
- `com.nimbusds:nimbus-jose-jwt:9.37.4`
- `com.nimbusds:oauth2-oidc-sdk:9.43.6`
- `com.squareup.okhttp3:okhttp:4.12.0`
- `com.squareup.okio:okio-jvm:3.6.0`
- `com.zaxxer:HikariCP:6.3.3`
- `io.github.oshai:kotlin-logging-jvm:7.0.13`
- `io.micrometer:micrometer-commons:1.15.12`
- `io.micrometer:micrometer-core:1.15.12`
- `io.micrometer:micrometer-jakarta9:1.15.12`
- `io.micrometer:micrometer-observation:1.15.12`
- `io.r2dbc:r2dbc-spi:1.0.0.RELEASE`
- `io.swagger.core.v3:swagger-annotations:2.2.50`
- `jakarta.validation:jakarta.validation-api:3.0.2`
- `net.minidev:accessors-smart:2.5.2`
- `net.minidev:json-smart:2.5.2`
- `org.apache.tomcat.embed:tomcat-embed-core:10.1.55`
- `org.apache.tomcat.embed:tomcat-embed-el:10.1.55`
- `org.apache.tomcat.embed:tomcat-embed-websocket:10.1.55`
- `org.attoparser:attoparser:2.0.7.RELEASE`
- `org.flywaydb:flyway-core:11.20.3`
- `org.flywaydb:flyway-database-postgresql:11.20.3`
- `org.hibernate.validator:hibernate-validator:8.0.3.Final`
- `org.jboss.logging:jboss-logging:3.6.3.Final`
- `org.jetbrains.kotlin:kotlin-reflect:2.2.20`
- `org.jetbrains.kotlin:kotlin-stdlib-jdk7:2.2.20`
- `org.jetbrains.kotlin:kotlin-stdlib-jdk8:2.2.20`
- `org.jetbrains.kotlin:kotlin-stdlib:2.2.20`
- `org.jetbrains:annotations:13.0`
- `org.jooq:jooq:3.19.26`
- `org.springframework.boot:spring-boot-actuator-autoconfigure:3.5.16`
- `org.springframework.boot:spring-boot-actuator:3.5.16`
- `org.springframework.boot:spring-boot-autoconfigure:3.5.16`
- `org.springframework.boot:spring-boot-jarmode-tools:3.5.16`
- `org.springframework.boot:spring-boot:3.5.16`
- `org.springframework.data:spring-data-commons:3.5.13`
- `org.springframework.security:spring-security-config:6.5.11`
- `org.springframework.security:spring-security-core:6.5.11`
- `org.springframework.security:spring-security-crypto:6.5.11`
- `org.springframework.security:spring-security-oauth2-client:6.5.11`
- `org.springframework.security:spring-security-oauth2-core:6.5.11`
- `org.springframework.security:spring-security-oauth2-jose:6.5.11`
- `org.springframework.security:spring-security-web:6.5.11`
- `org.springframework.session:spring-session-core:3.5.7`
- `org.springframework.session:spring-session-jdbc:3.5.7`
- `org.springframework:spring-aop:6.2.19`
- `org.springframework:spring-beans:6.2.19`
- `org.springframework:spring-context:6.2.19`
- `org.springframework:spring-core:6.2.19`
- `org.springframework:spring-expression:6.2.19`
- `org.springframework:spring-jcl:6.2.19`
- `org.springframework:spring-jdbc:6.2.19`
- `org.springframework:spring-tx:6.2.19`
- `org.springframework:spring-web:6.2.19`
- `org.springframework:spring-webmvc:6.2.19`
- `org.thymeleaf.extras:thymeleaf-extras-springsecurity6:3.1.5.RELEASE`
- `org.thymeleaf:thymeleaf-spring6:3.1.5.RELEASE`
- `org.thymeleaf:thymeleaf:3.1.5.RELEASE`
- `org.unbescape:unbescape:1.1.6.RELEASE`
- `org.yaml:snakeyaml:2.4`

#### BSD-2-Clause（1 件）

- `org.postgresql:postgresql:42.7.14`

#### BSD-3-Clause（1 件）

- `org.ow2.asm:asm:9.7.1`

#### CC0-1.0（1 件）

- `org.latencyutils:LatencyUtils:2.0.3`

#### CC0-1.0 または BSD-2-Clause（二者択一）（1 件）

- `org.hdrhistogram:HdrHistogram:2.2.2`

#### EPL-2.0（1 件）

- `org.aspectj:aspectjweaver:1.9.25.1`

#### EPL-2.0 または GPL-2.0 with Classpath Exception（二者択一）（1 件）

- `jakarta.annotation:jakarta.annotation-api:2.1.1`

#### EPL-2.0 または LGPL-2.1（二者択一）（2 件）

- `ch.qos.logback:logback-classic:1.5.34`
- `ch.qos.logback:logback-core:1.5.34`

#### MIT（7 件）

- `com.anthropic:anthropic-java-client-okhttp:2.71.0`
- `com.anthropic:anthropic-java-core:2.71.0`
- `com.anthropic:anthropic-java:2.71.0`
- `com.standardwebhooks:standardwebhooks:1.2.0`
- `org.checkerframework:checker-qual:3.55.1`
- `org.slf4j:jul-to-slf4j:2.0.18`
- `org.slf4j:slf4j-api:2.0.18`

#### MIT-0（1 件）

- `org.reactivestreams:reactive-streams:1.0.4`

## フォント

- **BIZ UDPGothic**（Regular・Bold）: SIL Open Font License 1.1。月の記録の PDF の日本語に使います。ライセンスの本文は、イメージの `/opt/lista/app.jar` の中の `BOOT-INF/classes/fonts/OFL.txt` にあります

## イメージに含まれず、ブラウザが読むもの

次のものは、利用者のブラウザが CDN から読みます。イメージには含まれません（README「外へ送るもの」）。

- Bootstrap 5.3.0（MIT）
- Bootstrap Icons 1.11.3（MIT）
- flatpickr 4.6.13（MIT）
- Sortable 1.15.7（MIT）
- Office.js（Microsoft。Outlook アドインの画面だけ）
