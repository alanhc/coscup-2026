# COSCUP 2026 演講大綱（工作用，勿發布到 docs/）

**議程**:韌體開發與 Agentic AI:讓 AI 跑 Build,你來解難題
**時間**:2026/8/8 14:20–14:50(30 min)· TR515 · Open Source Firmware track · 中階
**素材尺度**:只用 AOSP/Pixel 公開案例,公司經驗抽象成通則
**定位**:邊界設計方法論(不是 tool demo、不是個人採用之旅)

---

## 一句話定位

> 別人講「agent 能幫我做什麼」;我講「在真實的多專案 firmware 開發裡,人跟 agent 的邊界該畫在哪、責任怎麼不蒸發」。

同軌 / 同類 talk(如 Yocto Summit 的 Cursor-assisted 開發)是 journey + demo 導向。
差異化三支柱:chip vendor 多專案現場、可命名的 framework、規模化時的責任設計。

---

## 結構(30 min)

### 1. 開場鉤子(3 min)

- 從大家都會講的話開場,然後推翻:「大家都在講 agent 幫你寫 code。但 firmware 的瓶頸從來不是寫 code,是 `repo sync → build → flash → verify` 這條迴圈。」
- 秀 Pixel 8 的 7 步 pipeline(getprop → 查 tag → 下載 vendor blob → repo sync → 解壓授權 → lunch + m → flashall)。
- 現場提問:哪幾步需要人腦?→ 只有 3 步(版本對不齊、編譯錯誤、硬體異常)。
- 立論:**問題不是 AI 能做什麼,是邊界畫在哪。**

### 2. 核心方法論(12 min)

**先掛真的理論名詞(這是 framework 的地基,不是我發明的):**

- **Human-in-the-loop (HITL)** — 人保留在自動化決策迴圈的關鍵節點,既有概念。
- **Function allocation(功能分配)** — human factors 幾十年的老問題:一個系統裡哪些功能交給機器、哪些留給人。
- **Levels of Automation (LoA)** — Sheridan & Verplank(1978)提出 10 級自動化量表:從全手動(L1)到全自主(L10),中間是「電腦提示、人決定」。[REF-1]
- **Types and Levels of Automation** — Parasuraman, Sheridan & Wickens(2000,IEEE Trans. SMC)把它一般化:自動化可套用在四個階段(資訊擷取 → 資訊分析 → 決策與行動選擇 → 行動執行),每個階段可獨立選擇自動化程度。這篇的核心主張正好就是我這場的題目:**「決定哪些功能該自動化、自動化到什麼程度」需要一個客觀依據。**[REF-2]

**我的貢獻:把上面這套抽象理論,操作化成 firmware pipeline 能用的三條判斷線**
(誠實措辭:「這不是論文定式,是我從實務歸納、用來決定每個 task 該落在 LoA 哪一級的準則」)

1. **可逆性**:不可逆操作(bootloader unlock、fastboot flashall)→ 壓到低自動化級,人確認。
2. **可驗證性**:有客觀驗證標準的(SHA-256、build pass/fail、CI)→ 拉到高自動化級,全交給 AI。
3. **上下文判斷**:需要 context 的(找不到對應 tag、非預期編譯錯誤、硬體異常)→ 停在「行動選擇」階段交給人。

> 一句話串起來:我做的是「在 Parasuraman 的四階段模型上,用可逆性/可驗證性/上下文判斷,替 firmware build pipeline 決定每一步的自動化級別」。這才是誠實又有力的定位——不是引用別人的框架,是在既有理論上提出自己的分類。

**把它接到「當代」名詞——一條時間線,讓你顯得站在最前沿(強烈建議放一張 slide):**

- **1978 — Levels of Automation**(Sheridan & Verplank):自動化該分級。[REF-1]
- **2000 — Types & Levels of Automation**(Parasuraman et al.):分階段 × 分級,並要有客觀依據決定自動化到哪。[REF-2]
- **2024 — Agentic Design Patterns**(Andrew Ng):Reflection / Tool Use / Planning / Multi-agent。其中 **Reflection** 正是我 pipeline 的核心——agent 跑 build/測試、讀錯誤訊息、自己迭代修正,不必每步等人下 prompt。[REF-3]
- **2026 — Loop Engineering**(業界新興詞):不是把 agent 當一次性生成器,而是把工作設計成「trigger → 有可驗證的 goal → 執行 → 驗證 → 決定下一步」的迴圈;重點從 prompt engineering 移到「工具、上下文、驗證、停止條件、**以及人怎麼介入**」。[REF-4]

**這條線的殺傷力在哪:** loop engineering 官方定義列的五要素,最後一條就是「the way humans intervene(人如何介入)」——但業界文章大多只把它當一句話帶過。**我這場整場就是在把那句話展開成一個可操作的準則。** 換句話說:別人告訴你「要設計 human intervention」,我告訴你「在 firmware pipeline 裡,用可逆性/可驗證性/上下文判斷這三條線決定該在哪介入」。你不是在追熱詞,你是在補熱詞留下的那個洞。

> 上台措辭:「Andrew Ng 的 Reflection pattern 讓 agent 能自己迭代,loop engineering 告訴我們要設計 human intervention——但『在哪介入』沒人講清楚。這正是我今天要補的。」

實例走一遍 [aosp-build skill](https://github.com/alanhc/aosp-build):

- 輸入只有一個 Build ID(如 `BP4A.251205.006`),skill 跑完整條 pipeline。
- 展示可預期失敗的處理:網路中斷 retry、repo sync 降並行。
- 展示邊界觸發:skill 在 flashall 前暫停通知。
- **Demo 用預錄影片**,不現場跑。梗:「build 要 1–3 小時——這正是為什麼要交給 agent。」

### 3. 角色轉變與規模化(8 min)

- 執行者 → 監督者:一個人同時持有 3 條 pipeline 的狀態,只處理例外。
- 放量化數據(待補):等待時間回收、exception 介入率、並行專案數。
- 團隊層級的一擊:**責任不會消失,會滑到按 approve 的人手上。**
  - Stripe Minions 案例:每週 1300+ 個零人寫 PR,仍全數人審後才 merge。
  - 結論:規模化的關鍵不是模型多強,是驗證關卡多紮實。
- (時間夠再帶一句四道牆:可觀測性、問責、取代焦慮、review 變成責任錨點 → 指向 blog 深讀。)

### 4. 結尾(4 min + QA 3 min)

- 一張 slide:三準則總結 + repo QR code + 英文 blog 連結。
- 金句收尾:**Let AI handle the builds. You solve the hard parts.**

---

## 與 journey 型 talk 的差異(backup slide 用,不一定放)

| | Journey 型(如 Cursor+Yocto talk) | 這場 |
|---|---|---|
| 主軸 | 我怎麼從 noob 學會用 agent | 邊界該怎麼設計 |
| 證據 | anecdote、demo | framework + 數據 + 開源 repo |
| 情境 | 個人 / 小公司 | 多專案並行的 firmware 開發現場 |
| 終點 | 個人生產力 | 團隊規模化時的責任設計 |

---

## 墊腳石行動清單(演講外)

- [ ] 投影片全英文(口頭中文)
- [ ] 講前:打磨 aosp-build README 到面試官會細看的水準(badge、架構圖、Design Decisions 段落)
- [ ] HackMD 英文版擴充成 companion blog,slide 直接引用
- [ ] 講後 48 小時內:英文 LinkedIn post + slides 連結
- [ ] 錄影 / 請朋友側拍,剪 highlight
- [ ] 量化數據先蒐集:每條 pipeline 省下的 engineer-hours、介入次數 / 總 run 數

---

## 參考文獻(真實出處,已查證)

- **[REF-1]** Sheridan, T. B., & Verplank, W. L. (1978). *Human and Computer Control of Undersea Teleoperators.* MIT Man-Machine Systems Laboratory. — 最早的 10 級自動化量表(Levels of Automation)。
- **[REF-2]** Parasuraman, R., Sheridan, T. B., & Wickens, C. D. (2000). *A Model for Types and Levels of Human Interaction with Automation.* IEEE Transactions on Systems, Man, and Cybernetics—Part A, 30(3), 286–297. https://doi.org/10.1109/3468.844354 — 四階段 × 自動化程度的決策框架。
- **[REF-3]** Ng, A. (2024). *Four AI Agentic Workflow Design Patterns* — Reflection, Tool Use, Planning, Multi-Agent Collaboration. DeepLearning.AI / The Batch。https://www.deeplearning.ai/the-batch/ (亦見其 2024/03 LinkedIn/X 貼文)。
- **[REF-4]** Loop Engineering(2026 業界新興概念,無單一學術出處):agentic loop = trigger + 可驗證 goal + 執行 + 驗證 + 停止條件 + human intervention。參考:IBM《What Is Loop Engineering?》https://www.ibm.com/think/topics/loop-engineering ;Data Science Dojo《From ReAct to Loop Engineering》。
- HITL / Human-in-the-loop:控制論、自動化與 ML 通用概念,無單一經典出處。
- 責任滑向 reviewer、oversight burden、Stripe Minions 等:見 til 文章〈為什麼公司推不動 Agentic AI〉的參考資料清單(arXiv 論文與 Stripe 公開案例)。
