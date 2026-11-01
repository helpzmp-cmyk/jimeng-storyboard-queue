# 即梦分镜排队 Skill（半自动 v1）

给 AI 短剧写作用的 Cline Skill：把剧本拆成分镜头、把每镜写成**能直接粘进即梦（豆包）的成品提示词**，并用一本本地台账守住**每天 10 次免费额度**——每天最多放出 10 条，用满就收工，第二天自动续上。

生成动作始终由你手动完成：Skill 只交提示词、记账、排队，不碰即梦 App / 网页，不做任何自动化操作（防封号底线）。

## 包里有什么

| 文件 | 作用 |
| --- | --- |
| `SKILL.md` | Skill 本体：铁律、台账规范、4 个场景工作流、分镜规则 |
| `ledger.json` | 台账（状态文件）：今天用了几次、哪些镜头在排队、历史生成记录 |
| `README.md` | 本文件：安装与使用 |

## 安装

任选一种放置位置（目录名保持 `jimeng-storyboard-queue`）：

```bash
# 全局（所有工作区可用）
git clone git@github.com:helpzmp-cmyk/jimeng-storyboard-queue.git ~/.cline/skills/jimeng-storyboard-queue

# 或项目级（只在某个工作区可用）
git clone git@github.com:helpzmp-cmyk/jimeng-storyboard-queue.git <你的工作区>/.cline/skills/jimeng-storyboard-queue
```

如果你的 Cline 版本还没有 Skills 面板，也可以把仓库克隆到任意目录，然后直接说：**"读 ./SKILL.md，按里面的规则干活"**。

## 怎么用（四句话）

1. **贴剧本** → 自动拆镜、写提示词、入队，报告"新增几条 / 队列多长 / 要几天"
2. **"给我今天的"** → 放出 `10 − 今日已用` 条，每条一个代码块，逐条粘贴到即梦
3. **"生成了 5 条"** → 销账，更新 `今日已用 X/10`、完成总数、队列剩余
4. **队列空了** → 输出全集完成清单：每集几镜、共用几天、哪天生成了哪些

额度用完时，它只会说一句：**今天的 10 次用完了，剩下的明天继续。**

## 台账约定

- 唯一权威副本是**本目录下的 `ledger.json`**，复制 / 移动 Skill 目录时连它一起带走
- `status` 只有 `pending` / `today` / `done` 三种
- 跨日自动处理：读台账发现 `date` 不是今天 → `used_today` 归零、未确认的 `today` 退 `pending` 并排到队首
- `date` 按北京时间（即梦额度按北京 0 点重置），取值：`TZ=Asia/Shanghai date +%F`
- `history` 是追加型记账，用来出最后的"哪天生成了哪些"，除此之外不改台账结构

## 更新与备份

```bash
cd ~/.cline/skills/jimeng-storyboard-queue

# 更新前先备份台账（远端也可能改过 ledger.json）
cp ledger.json /tmp/ledger.backup.json
git stash push ledger.json      # 本地改过才需要
git pull --rebase
git stash pop                   # 有 stash 才需要；冲突就手工合并，保留你的真实进度
```

台账就是你的进度，任何更新都不要覆盖它；如果冲突，以**条目数更多、状态更靠后（done > today > pending）的那份**为准，手工核对一遍 `used_today`。

## 红线（写进 Skill 里，AI 不能绕）

- 每天最多放出 10 条，任何情况不超
- 不建议买积分 / 开会员 / 换付费通道赶进度
- 不操作即梦 App / 网页，不保存账号信息
- 不提前泄露未放出的提示词（那是绕过额度）

## 范围外（v1 不做）

不直接生成视频、不接火山 API、不做剪辑 / 配音 / 发布。只管到"提示词交到你手里并记好账"。
