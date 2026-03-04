# JSPS 特別研究員（DC）申請書 Typst テンプレート（LaTeX 版 1:1 移植）

このディレクトリは、[DC 申請書 LaTeX テンプレート](https://osksn2.hep.sci.osaka-u.ac.jp/~taku/kakenhiLaTeX/) を Typst に移植したものです。
レイアウトと挙動（固定ページ数・表頭 PDF の貼り付け・超過時エラーなど）を可能な限り 1:1 で再現しています。

## 編集するファイル
- `dc.typ`（単一ファイル。本文はここに記入）

## コンパイル
このディレクトリで実行してください（Typst の project root 制約のため）。

```bash
typst compile dc.typ dc.pdf
```

## 仕様（LaTeX 版と同等の挙動）
- 用紙：A4
- 余白：左右 17.4mm、上下 20mm
- 4 つの欄の固定ページ数：1 / 2 / 1 / 2（合計 6 ページ）
- 各欄の 1 ページ目に `subject_headers/dc_header_01..04.pdf` を全幅で自動挿入
- 本文が上限ページ数を超えた場合：Typst コンパイルが `panic` してエラー
- 本文が不足した場合：規定ページ数まで自動で空白ページを補完
- 画面上のページ番号表示：`– ３ –` から開始（中央）。
- 「（…の続き）」ヘッダ：本書式の固定ページ構成に合わせ、物理ページ 3 / 6 のみに表示

## 画像アセット（表頭・注意書き）
- `subject_headers/` 配下の PDF は、元 LaTeX テンプレートが同梱している **公式の表頭／注意書き PDF** をそのまま利用します。
- 年度更新等で差し替える場合は、`subject_headers/` の PDF を同名のまま置換してください。

## 注意書き（説明ボックス）
テンプレートには以下の補助関数があります：
- `JSPSInstructions()`
- `SelfReviewInstructions()`

これらは **赤字の注意 + 公式説明 PDF** を本文中に表示します。
提出用 PDF を作る際は、通常これらの呼び出しを削除（またはコメントアウト）してください。

## Acknowledgement
本 Typst 版は、以下の LaTeX テンプレート（配布物）を元に移植しました。

- **科研費LaTeX / KakenhiLaTeX**（JSPS 関連申請書類の LaTeX テンプレート群）
- 作成：**山中 卓（Taku Yamanaka, 大阪大学）**
- 参考：配布物に同梱の `A_README.pdf` および `pieces/kakenhi7.tex` の記載
- Web: http://osksn2.hep.sci.osaka-u.ac.jp/~taku/kakenhiLaTeX/

ライセンス／利用条件は、元配布物に記載の条件に従ってください（`pieces/kakenhi7.tex` 末尾に Fair License の記載があります）。

## 免責
本テンプレートは非公式の移植版です。提出前に、生成された PDF が最新の様式要件を満たしているか必ず確認してください。
