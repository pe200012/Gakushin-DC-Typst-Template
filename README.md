# DC（JSPS 特别研究员）Typst 模板（1:1 复刻版）

本目录是将 `dc-latex-ref/dc_utf_single/` 的 LaTeX 版式迁移到 Typst 的版本。

## 你需要编辑的文件
- `dc.typ`（单文件入口，直接写正文）

## 编译
在本目录执行：

```bash
typst compile dc.typ dc.pdf
```

## 重要行为（与 LaTeX 模板一致）
- 纸张：A4
- 边距：左右 17.4mm，上下 20mm
- 四个栏目固定页数：1 / 2 / 1 / 2（总 6 页）
- 每个栏目第一页会自动插入 `subject_headers/dc_header_01..04.pdf` 表头条带
- 若正文超出栏目允许页数：Typst 编译会 `panic` 并报错
- 若正文不足：自动补空白页至规定页数

## 资产
- `subject_headers/` 下的 PDF 来自官方表头/说明条带，模板直接引用。
- 如需更新到新年度表头，请替换该目录中的对应 PDF（并保持文件名不变）。

## 说明框（可选）
模板提供 `JSPSInstructions()` / `SelfReviewInstructions()`，用于显示红色提醒 + 官方说明 PDF。
提交前通常需要删除对应调用。
