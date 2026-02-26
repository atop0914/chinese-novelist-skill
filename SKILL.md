---
name: chinese-novelist
description: |
  分章节创作引人入胜的中文小说。支持各种题材（悬疑/言情/奇幻/科幻/历史等），支持10-50章长篇创作，每章3000-5000字，结尾设置悬念钩子。强调深度润色去除AI痕迹，确保文字自然流畅。
  当用户要求：写小说、创作故事、分章节写作、连续剧情、章节悬念、长篇小说时使用。
metadata:
  trigger: 创作中文小说、分章节故事、长篇小说创作
  source: 基于小说创作最佳实践设计
---

# Chinese Novelist: 中文小说创作助手

## 环境变量配置（推荐方式）

**提前设置环境变量，实现非交互式创作**：

```bash
# 必填（至少设置主题）
export NOVEL_TITLE="小说标题"

# 可选（使用默认值）
export NOVEL_THEME="游戏"                    # 题材：悬疑/言情/奇幻/科幻/武侠/历史/都市
export NOVEL_MAIN_CHARACTER="男性主角"        # 主角：男性主角/女性主角/双主角/群像戏
export NOVEL_PERSONALITY="成长逆袭"           # 性格：热血正义/冷静智慧/温暖治愈/高冷孤傲/阴暗腹黑/成长逆袭
export NOVEL_CONFLICT="成长突破"             # 冲突：生死存亡/查明真相/爱情阻碍/复仇雪恨/权力争夺/成长突破/守护保护
export NOVEL_CHAPTERS="20"                   # 章节数：10/15/20/30/50
export NOVEL_AUTHOR="锦珩不晚"                # 作者名（封面用）
```

**默认值**（未设置环境变量时使用）：
- 题材：现代言情
- 主角：男性主角
- 性格：成长逆袭
- 冲突：成长突破
- 章节数：20章

---

## 核心流程

### 第一阶段：读取配置（自动化）

**检查环境变量**：
1. 读取 `NOVEL_TITLE` - 小说标题（必填）
2. 读取 `NOVEL_THEME` - 题材类型（默认：现代言情）
3. 读取 `NOVEL_MAIN_CHARACTER` - 主角设定（默认：男性主角）
4. 读取 `NOVEL_PERSONALITY` - 主角性格（默认：成长逆袭）
5. 读取 `NOVEL_CONFLICT` - 核心冲突（默认：成长突破）
6. 读取 `NOVEL_CHAPTERS` - 章节数量（默认：20）

**如果环境变量已设置**，跳过交互，直接进入"第二阶段：规划"

**如果环境变量未设置**，执行以下5问确认（最多只问一次）：

---

**问题1：题材与风格**

```
Question: 你想要创作什么题材的小说？
Options:
- 悬疑推理（侦探、破案、解谜）
- 现代言情（都市、职场、恋爱）
- 古代言情（宫廷、江湖、穿越）
- 奇幻玄幻（魔法、异世界、修真）
- 科幻未来（科技、太空、末世）
- 武侠仙侠（江湖、门派、飞升）
- 历史架空（朝堂、战争、权谋）
- 都市现实（生活、成长、社会）
```

用户选择后记录：`题材 = [用户选择]`

---

**问题2：主角设定**

```
Question: 主角是什么设定？
Options:
- 男性主角（独角戏）
- 女性主角（独角戏）
- 双主角（男女双线）
- 群像戏（多线叙事）
```

用户选择后，如需进一步询问职业/身份，继续追问。

记录：`主角 = [用户选择]` + `职业/身份 = [用户回答]`

---

**问题3：主角性格**

```
Question: 主角的核心性格是？
Options:
- 热血正义（积极、勇敢、有担当）
- 冷静智慧（理性、谋略、高智商）
- 温暖治愈（善良、温柔、有同理心）
- 高冷孤傲（冷漠、独立、强大）
- 阴暗腹黑（心机、算计、复仇）
- 成长逆袭（从弱到强、打脸升级）
```

记录：`性格 = [用户选择]`

---

**问题4：核心冲突**

```
Question: 小说的核心冲突是什么？
Options:
- 生死存亡（生存危机、逃出生天）
- 查明真相（寻找答案、揭露秘密）
- 爱情阻碍（追求真爱、克服阻碍）
- 复仇雪恨（复仇计划、伸张正义）
- 权力争夺（竞争上位、资源争夺）
- 成长突破（自我突破、实现价值）
- 守护保护（守护重要的人或事）
```

记录：`核心冲突 = [用户选择]`

---

**问题5：章节数量**

```
Question: 你计划创作多少章？
Options:
- 10章（短篇，约3-5万字）
- 15章（中短篇，约4.5-7.5万字）
- 20章（中篇，约6-10万字）
- 30章（中长篇，约9-15万字）
- 50章（长篇，约15-25万字）
- 自定义（输入具体章节数）
```

记录：`章节数 = [用户选择]`

---

**5问收集完成后**然后进入"第二阶段：规划"。

---

### 自动检测脚本

```bash
# 检查环境变量是否已配置
if [ -n "$NOVEL_TITLE" ]; then
    echo "检测到环境变量配置，使用自动化模式"
    # 直接使用环境变量值
    THEME=${NOVEL_THEME:-"现代言情"}
    MAIN_CHAR=${NOVEL_MAIN_CHARACTER:-"男性主角"}
    PERSONALITY=${NOVEL_PERSONALITY:-"成长逆袭"}
    CONFLICT=${NOVEL_CONFLICT:-"成长突破"}
    CHAPTERS=${NOVEL_CHAPTERS:-"20"}
    # 跳过5问，直接进入规划
else
    echo "未检测到环境变量，使用交互模式"
    # 执行5问确认
fi
```

---

### 第二阶段：规划 + 自动确认

执行以下步骤：

1. **创建项目文件夹**：`novels/[小说名称]/`
2. **生成大纲**：创建 `00-大纲.md`，使用 `references/outline-template.md` 模板，填入完整的章节规划
3. **生成人物档案**：创建 `01-人物档案.md`，使用 `references/character-template.md` 模板，创建主角、反派、配角档案

完成后：
- **有环境变量**：自动进入第三阶段
- **无环境变量**：向用户展示规划摘要并请求确认

---

### 第三阶段：疯狂创作

**重要：全程无需再次向用户确认，必须逐一章创作**

按顺序逐章创作，每章执行完整的创作流程（见下方"逐章创作"），完成一章后自动继续下一章，直到所有章节完成。

---

## 疯狂创作——逐章创作流程

每章创作时严格执行以下步骤：

#### 1. 写前分析

1. 读取 `00-大纲.md` - 查看TODO list和已完成章节的摘要
2. 读取 `00-大纲.md` 中上一章的摘要
3. 更新`00-大纲.md` 中 TODO list - 将本章标记为"进行中"
4. 设计开头钩子 - **最关键**：前20%必须有即时冲突 → [chapter-guide.md](references/chapter-guide.md)（10种开头技巧）
5. 规划场景 - 确定本章需要3-5个场景

#### 2. 撰写

6. 创建章节文件 - 使用`references/chapter-template.md` 模板
7. 撰写正文 - **每章必须达到3000-5000字**
   - 开头检查：前20%是否极其吸引人？
   - 对话规范 → [dialogue-writing.md](references/dialogue-writing.md)
   - 内容不足？使用 [content-expansion.md](references/content-expansion.md) 扩充技巧
8. 设置结尾钩子 → [hook-techniques.md](references/hook-techniques.md)（10种钩子类型）
9. **字数检查** - 必须使用脚本检查字数：`python scripts/check_chapter_wordcount.py <章节文件路径>` 低于3000字必须使用扩充技巧重写

#### 3. 撰写后优化

10. 连贯性检查 → [consistency.md](references/consistency.md) - 人物一致性、情节连贯、节奏控制
11. **深度润色（去除AI味）** - 重点检查并修改：
    - **去除过度修饰的形容词**：删减"璀璨"、"瑰丽"、"绚烂"等AI常用词堆砌
    - **减少抽象陈述**：把"心中涌起复杂的情感"改为具体动作/对话
    - **打破四字格律**：避免"心潮澎湃、热血沸腾"等陈词滥调
    - **增加口语化表达**：人物对话要有个性，避免"书面语套话"
    - **优化节奏感**：长句和短句交替，避免句式单调
    - **细节具象化**：用具体的视觉/听觉/嗅觉细节替代笼统描述
12. **字数检查** - 必须使用脚本检查字数：`python scripts/check_chapter_wordcount.py <章节文件路径>` 低于3000字必须使用扩充技巧重写

#### 4. 最后收尾

13. 生成章节摘要 - 在 `00-大纲.md` 添加摘要（300-500字）
14. 更新状态 - `00-大纲.md` 中 TODO list - 将本章标记为"完成"

---

### 第四阶段：合并章节

当所有章节创作完成后，先执行章节合并：

1. **合并所有章节** - 将分散的章节文件合并成一个完整的 markdown 文件：

```bash
# 进入小说目录
cd novels/小说名/

# 合并所有章节文件（按文件名排序）
cat 00-大纲.md 01-人物档案.md 第*.md > 小说名_合并版.md

# 也可以使用 Python 脚本更智能地合并
python3 << 'EOF'
import os
import re

novel_dir = "."
output_file = f"{os.path.basename(novel_dir)}_合并版.md"

# 定义文件顺序
priority_files = ["00-大纲.md", "01-人物档案.md"]
chapter_files = sorted([f for f in os.listdir(novel_dir) if re.match(r"第\d+章", f)])

with open(output_file, "w", encoding="utf-8") as out:
    # 先写入优先级文件
    for pf in priority_files:
        if os.path.exists(pf):
            with open(pf, "r", encoding="utf-8") as f:
                out.write(f.read() + "\n\n")
    
    # 再写入章节文件
    for cf in chapter_files:
        with open(cf, "r", encoding="utf-8") as f:
            out.write(f.read() + "\n\n")

print(f"已合并到: {output_file}")
EOF
```

2. **通知用户** - 章节合并完成

---

### 第五阶段：自动生成封面（可选）

当所有章节创作完成后，自动执行以下步骤：

1. **读取大纲摘要** - 读取 `00-大纲.md` 中的章节摘要，了解小说核心内容
2. **提取关键元素** - 从摘要中提取：
   - 小说题材类型
   - 主角特征
   - 核心场景/氛围
   - 故事风格（悲壮/甜蜜/热血/温馨等）
   - 书名（从文件名或大纲中获取）
3. **获取作者名和书名** - 从环境变量获取：
   - `NOVEL_AUTHOR` - 作者名（默认"锦珩不晚"）
   - `BOOK_TITLE` - 书名（从小说目录名或环境变量获取）
4. **生成封面图片** - 调用阿里云百炼图片生成API，直接在提示词中包含书名、作者名及详细设计要求：

```bash
# 读取环境变量
AUTHOR=${NOVEL_AUTHOR:-"锦珩不晚"}
BOOK_TITLE=${BOOK_TITLE:-$(basename "$(pwd)")}

# 调用阿里云百炼 API 生成图片
curl -s -X POST 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation' \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer $ALIYUN_API_KEY" \
  -d "{
    \"model\": \"qwen-image-max\",
    \"input\": {
      \"messages\": [{
        \"role\": \"user\",
        \"content\": [{
          \"text\": \"小说封面竖版构图，600*800像素，高品质。${BOOK_TITLE}，作者：${AUTHOR}。【文字部分 - 必须生成】封面顶部三分之一处，用大号华丽艺术字体显示书名《${BOOK_TITLE}》，字体为金色渐变配红色描边，立体浮雕效果，字形飘逸有力，带有轻微发光特效。封面底部中央位置，用优雅行书字体显示作者名${AUTHOR}，字体为深棕色，大小适中，下方配有一枚红色印章图案装饰。<根据小说内容生成的图片描述>，精致唯美，画风精美\"
        }]
      }]
    },
    \"parameters\": {
      \"negative_prompt\": \"低分辨率，低画质，肢体畸形，手指畸形，画面过饱和，蜡像感，人脸无细节，过度光滑，画面具有AI感。构图混乱。文字模糊，扭曲。\",
      \"prompt_extend\": true,
      \"watermark\": false,
      \"size\": \"600*800\"
    }
  }"
```

**提示词示例**：
```
小说封面竖版构图，600*800像素，高品质。烬羽行，作者：锦珩不晚。【文字部分 - 必须生成】封面顶部三分之一处，用大号华丽艺术字体显示书名《烬羽行》，字体为金色渐变配红色描边，立体浮雕效果，字形飘逸有力，带有轻微发光特效。封面底部中央位置，用优雅行书字体显示作者名锦珩不晚，字体为深棕色，大小适中，下方配有一枚红色印章图案装饰。亡国公主在废墟中，手持玉佩，血染宫墙，悲壮暗黑奇幻风格，精致唯美，画风精美
```

5. **保存封面** - 将生成的图片保存到小说目录：
```bash
curl -s -X POST ... > 封面.png
cp 封面.png novels/小说名/封面.png
```

7. **通知用户** - 告知用户小说和封面都已完成

**环境变量**：
- `ALIYUN_API_KEY` - 阿里云百炼 API Key（必填）
- `NOVEL_AUTHOR` - 作者名（默认"锦珩不晚"）
- `BOOK_TITLE` - 书名（从目录名获取）

**提示词格式**：
```
小说封面图片，《书名》，作者：作者名，<小说内容描述>，竖版构图，600*800像素
```

---

## 三大黄金法则

1. **展示而非讲述** - 用动作和对话表现，不要直接陈述
2. **冲突驱动剧情** - 每章必须有冲突或转折
3. **悬念承上启下** - 每章结尾必须留下钩子

### 字数检查脚本

使用 `scripts/check_chapter_wordcount.py` 检查章节字数：

```bash
# 检查单个章节
python scripts/check_chapter_wordcount.py novels/小说名/第01章.md

# 检查所有章节
python scripts/check_chapter_wordcount.py --all novels/小说名/

# 自定义最小字数
python scripts/check_chapter_wordcount.py novels/小说名/第01章.md 3500
```

低于3000字的章节必须使用 [content-expansion.md](references/content-expansion.md) 的扩充技巧进行扩充。

---

## OpenClaw 驱动方式

**通过环境变量调用**：

```bash
# 设置环境变量
export NOVEL_TITLE="我的新小说"
export NOVEL_THEME="游戏"
export NOVEL_MAIN_CHARACTER="男性主角"
export NOVEL_PERSONALITY="成长逆袭"
export NOVEL_CONFLICT="成长突破"
export NOVEL_CHAPTERS="20"
export NOVEL_AUTHOR="锦珩不晚"

# 调用 Claude Code
claude --dangerously-skip-permissions "/chinese-novelist"
```

**或使用一行命令**：

```bash
NOVEL_TITLE="我的新小说" NOVEL_THEME="游戏" NOVEL_CHAPTERS="20" claude -p --dangerously-skip-permissions "/chinese-novelist"
```
