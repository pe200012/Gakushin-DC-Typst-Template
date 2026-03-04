# Design Log: DC（日本学術振興会 特別研究員 DC）LaTeX 模板 → Typst 1:1 复刻

日期：2026-03-04

## Background
现有模板位于 `dc-latex-ref/dc_utf_single/`，通过 LaTeX 宏（`pieces/kakenhi7.tex` + `fancyhdr`）实现：
- A4 固定边距（左右约 17.4mm、上下约 20mm）
- 每个栏目第一页插入官方表头条带（`subject_headers/dc_header_01..04.pdf`，使用 CropBox/XBB 裁切）
- 续页显示“（…の続き）”
- 页脚固定格式：中心 `– ３ –`（全角数字），右侧“申請者登録名”下划线与姓名
- 栏目固定页数（1/2/1/2），不足自动补空白页；超出时报错

用户目标：迁移到 Typst，并尽量做到版式与行为 1:1。

## Problem
Typst 与 LaTeX 的分页/页眉页脚机制不同：
- Typst 的页眉/页脚在 margin 区域内布局，难以精确复刻 LaTeX 在页边界的绝对坐标布局
- 续页“（…の続き）”在 LaTeX 中由 `fancyhdr` 动态生成；Typst 需要可控、稳定的实现
- 需要严格的“固定页数 + 超页报错 + 自动补页”逻辑

## Questions and Answers
### Q1: 是否允许继续复用现有 `subject_headers/*.pdf` 资产？
A1: 允许（用户确认）。

### Q2: Typst 入口文件组织方式？
A2: 单文件 `dc.typ`（用户确认）。

### Q3: 续页页眉如何实现？
A3: 由于本表单页数固定且栏目顺序固定，续页只会出现在物理页 3 与 6（对应显示页 5 与 8）。采用按物理页号在 `page.background` 中条件渲染，保证行为稳定。

## Design
### 1) 资产布局
在 `dc-typst/subject_headers/` 放置：
- `dc_header_01..04.pdf`（栏目第一页表头条带）
- `inst_general.pdf`、`inst_self_review.pdf`（可选说明框）

### 2) 页面设置
`#set page(paper: "a4", margin: (left/right 17.4mm, top/bottom 20mm), background: context { ... })`

用 `page.background` + `place(top + left, dx, dy)` 在绝对坐标绘制：
- 页脚（中心 `– <全角页码> –`、右侧栏位与姓名、下划线）
- 续页页眉（仅物理页 3 与 6）

页码显示：不修改 Typst 的 `counter(page)`，而是显示 `counter(page) + 2`，使显示从 3 开始并与 header PDF 内页码一致。

### 3) 栏目宏（subject）
提供 `subject(id, name, max_pages, last: bool)[body]`：
- 在栏目起始插入 `dc_header_##.pdf`（全宽，水平左移 17.4mm 对齐页面左边界）
- 记录栏目起始页与结束页，计算占用页数
- 若超出 `max_pages`：`panic("…は N ページ以内で…")`
- 若不足：插入 `#pagebreak()` 补足空白页
- 栏目末：`#pagebreak()`（非最后栏目），最后栏目用 `#pagebreak(weak: true)` 避免文末空白页

### 4) 可选说明框
实现 `JSPSInstructions()` / `SelfReviewInstructions()`：
- 红字提示“请删除/注释该行”
- 全宽插入对应说明 PDF

## Implementation Plan
1. 复制 `dc_utf_single/subject_headers/*.pdf` 到 `dc-typst/subject_headers/`
2. 编写 `dc-typst/dc.typ`：
   - 全局页面 set rule
   - 数字全角化函数
   - 栏目函数（分页检查、补页）
   - 背景层绘制页脚与续页页眉
3. 写 `dc-typst/README.md`：编译命令、用户需要修改的变量、注意事项
4. 运行 `typst compile dc.typ`，检查：
   - 页数为 6
   - 表头条带位置正确
   - 续页页眉在物理页 3/6 出现
   - 页脚坐标与 LaTeX 参考一致（通过 `pdftotext -bbox` 验证关键坐标）

## Examples
- 用户仅需编辑 `dc.typ` 内四个栏目内容。
- 若不想显示说明框，删除/注释 `#JSPSInstructions()` / `#SelfReviewInstructions()` 调用。

## Trade-offs
- 续页页眉采用按页号渲染：在本表单固定页数/固定栏目顺序前提下最稳定；若未来栏目页数或顺序改变，需要同步更新页号条件。
- 页脚使用 `background` 绝对坐标绘制：可 1:1 对齐，但会绕开 Typst 的语义页脚系统。

## Implementation Results
- 新增目录：`dc-typst/`
- 复制资产：`dc-typst/subject_headers/*`（来自 `dc_utf_single/subject_headers/`）
- 生成模板入口：`dc-typst/dc.typ`
  - 表头条带：`move(dx: -17.4mm)[#image(..., width: 210mm)]` + `v(-0.9mm)`（用于匹配 LaTeX 输出中表头后留白）
  - 页脚/续页页眉：在 `#set page(background: context { ... })` 中用 `place(top + left, dx, dy)` 绝对定位
  - 页码显示：使用 `counter(page) + 2`（不重置 counter，避免 background 读到旧值）
  - 固定页数与超页检查：采用 `here()` 捕获栏目起止 location，并在 `context { counter(page).at(location) }` 中计算页差；超页用 `panic` 报错；不足用 `pagebreak()` 补页
- 生成说明：`dc-typst/README.md`
- 验证：`typst compile dc.typ dc.pdf` 输出 6 页；续页页眉仅出现在物理页 3/6；超页测试可触发 `panic`。
