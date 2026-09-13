## 单行基础卡片（Single-line Basic）

提示语（正面）与答案（背面）用 `::` 分隔（可在设置中修改分隔符）。

`问题写在前面::答案写在这里！`

复习时显示为：

卡片正面：问题写在前面

卡片背面：答案写在这里！

## 单行双向卡片（Single-line Bidirectional）

由一行卡片文本生成**两张**卡片。

两部分用 `:::` 分隔（可在设置中修改分隔符）。

例如：

`信息 1:::信息 2`

卡片 1：正面 = 信息 1，背面 = 信息 2

卡片 2：正面 = 信息 2，背面 = 信息 1

这两张卡片互为**兄弟卡片**。关于"兄弟卡片延后至次日复习"的排程选项，参见[兄弟卡片](https://stephenmwangi.com/obsidian-spaced-repetition/zh/flashcards/flashcards-overview/#sibling-cards)与[复习选项](https://stephenmwangi.com/obsidian-spaced-repetition/zh/user-options/#flashcard-review)。

---

## 多行基础卡片（Multi-line Basic）

卡片正面与背面用 `?` 分隔（可在设置中修改分隔符）。

```
根据定义，
"多行"的提示语
可以占多行
?
背面
也可以占多行
```

复习时显示为：

卡片正面：

> 根据定义，
> "多行"的提示语
> 可以占多行

卡片背面：

> 背面
> 也可以占多行

只要两侧内容"紧贴" `?`（即 `?` 之前和之后都没有空行），正反两面都可以跨越多行。

如果需要在卡片内包含空行，参见[含空行的卡片](https://stephenmwangi.com/obsidian-spaced-repetition/zh/flashcards/cards-with-blank-lines/)。

---

## 多行双向卡片（Multi-line Bidirectional）

由一段卡片文本生成**两张**卡片。

两部分用 `??` 分隔（可在设置中修改分隔符）。

例如：

```
信息 1A
信息 1B
信息 1C
??
信息 2A
信息 2B
```

只要两侧内容"紧贴" `??`（前后无空行），正反两面都可以跨越多行；如需包含空行，参见上文"含空行的卡片"。

卡片 1：正面 = 信息 1A / 1B / 1C，背面 = 信息 2A / 2B

卡片 2：正面 = 信息 2A / 2B，背面 = 信息 1A / 1B / 1C

这两张卡片互为**兄弟卡片**，可配合"兄弟卡片延后至次日复习"的排程选项使用（链接见上文）。

