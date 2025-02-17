```markdown
---
title: 引用键（Citation Keys）
weight: 3
aliases:
  - /Citation-Keys
  - /citation-keys
tags:
  - 引用键
---

## 为你的条目生成引用键

标准 Zotero 导出的 BibTeX 引用键总是在导出时生成，并使用一种通常能确保唯一性的算法。然而，对于严谨的 LaTeX 用户来说，"通常" 可能会带来以下问题：

* 如果生成了非唯一的键，则哪个键会被后缀一个区分字符是基本上不可预测的。
* 这些键 *始终* 是自动生成的，因此如果你更正了作者姓名或标题中的拼写错误，引用键也会发生变化。
* 你在导出之前无法看到引用键。

对于 LaTeX 作者来说，引用键有其独立的意义，与条目数据是分开的，尽管人们通常会选择与条目数据相关的命名方案。由于引用键是连接你的参考文献的 *关键* 数据，你应该能够完全控制它们。BBT 提供了这一控制能力：

* **稳定的引用键，无冲突**。BBT 生成的引用键会考虑到你文献库中的其他现有键，并以确定性的方式生成，不管你导出的是文献库的哪部分，或者导出的顺序如何。
* **BBT 尽量减少引用键的变化，并允许你固定引用键** 为任意你选择的值。
* **从 JabRef 风格的模式生成引用键**。

此外，你还可以：

* **将 LaTeX 引用键拖放到你的 LaTeX 编辑器** 中。
* **在条目列表视图中显示你的引用键**。

## 设置自定义、固定的引用键

默认情况下，BBT 从条目信息生成引用键，这些键在你编辑条目时可能会发生变化。这种键称为 `动态` 键。而 `固定` 键在条目列表视图和条目详情中会以图钉（📌）标记，以区别于动态键。

你可以通过在条目的 `extra` 字段中单独一行添加 `Citation Key: <你的引用键>` 来固定引用键（BBT 称之为 `pinning`）。你还可以通过选择一个或多个条目，右键点击并选择 `Generate BibTeX key` 来生成固定的引用键，这将把当前的引用键添加到 `extra` 字段，从而固定它。

## 拖放/快捷键方式插入引用

你可以将引用拖放到 LaTeX/Markdown/Orgmode 编辑器中，它会自动添加正确的 `\cite{citekey}`、`[@citekey]` 或 `[[zotero://select...][@citekey]]` 格式的引用。对于 LaTeX，你可以在 [偏好设置]({{% ref "installation/preferences" %}}) 中配置 `cite` 命令（不包含前导的反斜杠 `\`）。

此功能需要一次性设置：

1. 在 BBT 的 `Citation keys` 偏好设置中选择 **Quick Copy** 格式。
2. 在 Zotero 偏好设置的 `导出` 选项卡下，默认输出格式选择 **Better BibTeX Quick Copy: [你刚刚选择的格式]**。

## 通过 [Report Customizer](https://github.com/retorquere/zotero-report-customizer) 查找重复键

该插件会生成 BibTeX 注释，指示引用键是否冲突以及与哪个条目冲突。BBT 集成了 [Zotero: Report Customizer](https://github.com/retorquere/zotero-report-customizer)，可以在 Zotero 报告中显示 BibTeX 键及其冲突情况。

## 可配置的引用键生成器

BBT 还实现了一个引用键生成器，用于为那些没有显式设置引用键的条目生成引用键。格式化模式语言原本遵循 [JabRef 键格式化语法](https://help.jabref.org/en/BibtexKeyPatterns)，但现在已改为类似 JavaScript 的格式。你可以在 Better BibTeX 偏好设置中设置你的生成器模式（可通过 Zotero 偏好设置或插件管理面板中的 `Better BibTeX Preferences` 按钮进入）。

### 引用键生成规则

Better BibTeX 允许从四种数据类型构造引用键：

1. **函数（functions）**：基于条目生成文本，例如 `shorttitle`。这些函数对大小写不敏感，但必须以小写字母开头。
2. **字段访问（field access）**：直接从 Zotero 条目字段获取文本。这些字段对大小写不敏感，但必须以大写字母开头。
3. **过滤器（filters）**：对函数或字段返回的文本进行处理，例如 `(auth + title || year)`。过滤器对大小写不敏感，可以链接使用，每个过滤器作用于前一个的输出。
4. **原始字符串**：用单引号或双引号括起来的文本。

支持以下三种方式构造子公式：

1. **组合（composition）**：`(auth + title)`
2. **备选项（alternates）**：`(auth || title)`（使用第一个返回非空文本的值，如 `auth` 为空则使用 `title`）
3. **三元运算（ternaries）**：`(auth ? year : title)`（如果 `auth` 非空，则使用 `year`，否则使用 `title`）

示例：

```text
auth.lower + shorttitle(3,3) + year
```

解释：

1. **`auth.lower`**：第一个作者的姓氏，转换为小写。
2. **`shorttitle(3,3)`**：标题的前三个单词，并对其中的前三个单词应用大写规则。
3. **`year`**：出版年份。
4. **字母后缀（a, b, c, ...）**：如果存在重复键，会自动添加字母后缀（可以更改为 Zotero 风格的数字后缀）。

**注意**：更改模式后，仅影响之后新建或编辑的条目，已有条目的引用键不会自动更新。如果要应用新模式，请选中条目，右键点击并选择 `刷新（Refresh）`，这不会影响已固定的引用键。

你还可以使用 `;` 或 `|` 设定多个模式，第一个返回非空字符串的模式将被应用。例如：

```text
extra('tex.shortauthor').transliterate.clean.lower.len + year; auth.lower + year
```

这将使用 `tex.shortauthor`（如果存在），否则使用 `auth.lower + year`。

**示例应用**：

```text
(title ? title : auth).lower + year
```

等价于：

```text
(title || auth).lower + year
```

**旧格式支持**：旧格式仍然可用，但会自动转换为新格式。以下是函数和过滤器的完整列表：

#### 函数

{{< citekey-formatters/functions >}}

**注意**：
- `auth...` 相关函数如果没有作者信息，会回退到编辑者信息。
- 以前 `clean` 过滤器是自动应用的，**现在不再自动应用**。如果要处理 CJK（中日韩）字符（如 `capitalize`），需要手动添加 `transliterate`，例如：
  ```text
  authEtal2.transliterate.capitalize + year + shorttitle(3,3)
  ```

#### 直接访问字段

{{< citekey-formatters/fields >}}

#### 过滤器

{{< citekey-formatters/filters >}}

**使用注意**：
- `condense`、`skipwords`、`capitalize` 和 `select` 依赖空格进行单词处理。大多数函数会去除空格，因此建议直接使用上面提供的字段获取未经处理的值。

```
