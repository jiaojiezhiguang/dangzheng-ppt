# 工具链、渲染与验收

## 运行时（本机固定路径，不要另装）

| 用途 | 路径 / 用法 |
|---|---|
| Python（python-pptx 1.0.2 / Pillow / lxml） | `~/.dsh/dsh-runtimes/dsh-primary-runtime/dependencies/python/bin/python3` |
| pptx → PDF | `NODE=/Applications/DeepSeek Harness.app/Contents/Resources/runtime/primary-runtime/dependencies/node/bin/node`；`LOCLI=/Applications/DeepSeek Harness.app/Contents/Resources/app.asar.unpacked/dsh/node_modules/@deepseek-ai/libreoffice-kit/lib/cli.js`；`"$NODE" "$LOCLI" convert --input x.pptx --output y.pdf` |
| PDF → PNG | `~/.cache/codex-runtimes/codex-primary-runtime/dependencies/bin/override/pdftoppm -png -r 90 [-f 起页 -l 止页] y.pdf 输出前缀` |
| 包结构与页数自检 | `python3 "/Applications/DeepSeek Harness.app/Contents/Resources/runtime/office-skills/scripts/check_office.py" 文件.pptx --out /tmp/c.json --count N`，期望 `verdict: pass` |

用 python-pptx，不要用 Presentations skill 默认的 `@oai/artifact-tool`：这套稿子的历史脚本、装饰规范和验收流程都建立在 python-pptx 上，换路线等于全部重写，只剩风险。

## 渲染核对

- `render --input x.pptx` 会返回**空白页**，必须先 `convert` 成 PDF 再渲染 PDF。
- LibreOffice 没有微软雅黑，`convert` 会报 `missingFonts:["微软雅黑"]`，**这是正常的**：只能判断版式，判断不了字体精确效果，最终要在 Windows/PowerPoint 里看。
- 逐页看图不可省。

## 验收清单

1. 页数 == 期望
2. 每张图：显示宽高比 == 源图宽高比（0 变形）
3. 每张图：不越界（版心 0.70–12.63，上 1.19，下 6.70）
4. 行内间距取值集合 == `{0.15}`
5. 旧装饰残留 == 0
6. run 级字体取值集合 == `{微软雅黑}`；主题 `majorFont`/`minorFont` 的 latin/ea/cs 同样是微软雅黑
7. 与源版**逐形状比对**：差异条数应该等于预期改动数

## 踩过的坑

1. **边遍历 `slide.shapes` 边把元素移到 `spTree` 末尾，同一个 shape 会被匹配两次**（第 5 页正文曾被写了两遍）。要改元素位置就先取好目标，或先快照列表再改。
2. **同一位置常有底层纯色形状**（红底框与文字框叠放）。按坐标找文本框时会先命中没有文字的那个，赋值静默失败。按坐标找文本框必须跳过空文字的。
3. python-pptx 新增形状会盖在既有形状之上。先加底色块再加文字，文字会被遮住：加完底色把文字框元素移到 `spTree` 末尾提到最上层；反过来要给图片铺底衬，就把底衬插到 `spTree` 索引 2 压到最底层。
4. 图片的白色相框要在重排**之前**建立映射，重排后再贴合，否则框会留在旧位置露白块。
5. 形状几何用 `Emu(x).inches`；判断"同一行"用 `round(top, 1)` 分组。
6. 插页会让后面所有页页码错位。插完要顺延页码——这一步会改到其他页，属于**必须的连带改动**，交付时要主动说明。
7. 删幻灯片要同时 `drop_rel` 掉它的 rId，否则幻灯片部件仍留在包里；删图片形状也要顺手 `drop_rel` 掉图片关系，否则图片部件会一直躺在文件里（27MB 的图能压到 98KB 就是这么来的）。
8. 只裁顶部状态栏时，**把状态栏下面那行返回键/更多按钮一起裁掉**——留着两个手机按钮比不裁更难看。裁图片前先确认用户没说过"这张不能裁"。
