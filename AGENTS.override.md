# Jin's to do — 学习计划项目手册

> 单文件 HTML 考试日历 + 学习追踪器，使用 GitHub Pages 部署。

## 线上地址

https://jinluo1993-ux.github.io/study-plan/

## 仓库

`jinluo1993-ux/study-plan` — GitHub Pages public repo

## 核心功能

| 功能 | 说明 |
|:----|:------|
| 📚 考试日历 | 3列月历网格，标注考试日(红)、报名截止(蓝)、今天(紫) |
| 📊 阶段进度条 | 4阶段（托业主攻/CFA一级/BEC高级/CPA），每段显示考试倒计时 |
| 📝 每日记录 | 选科目+时长 → 记录到 localStorage，自动同步云端 |
| ☁️ 跨设备同步 | 记录即推送 GitHub data.json，打开即拉取合并 |
| 🖼️ 弹窗 | 托业主攻 📋 → 4张备考图；CFA一级 📋 → 5张备考图 |

## 数据结构

### KEY_DATES — 考试/报名日期
```js
const KEY_DATES = [
  { date: '2026-08-23', label: '📝 托业考试(1期)', type: 'exam' },
  { date: '2026-10-25', label: '📝 托业考试(2期)', type: 'exam' },
  { date: '2026-11-11', label: '📝 CFA一级考试(1期)', type: 'exam' },
  { date: '2027-02-22', label: '📝 CFA一级考试(2期)', type: 'exam' },
  { date: '2027-05-22', label: '📝 BEC考试(预计)', type: 'exam' },
  { date: '2027-08-29', label: '📝 CPA考试', type: 'exam' },
  // + tipo: 'reg' for registration deadlines
];
```

### PHASES — 学习阶段
```js
const PHASES = [
  { label: '托业主攻', start: '2026-06-24', end: '2026-10-25', cls: 'p0' },
  { label: 'CFA一级',  start: '2026-10-26', end: '2027-02-28', cls: 'p1' },
  { label: 'BEC高级',  start: '2027-03-01', end: '2027-05-22', cls: 'p2' },
  { label: 'CPA',      start: '2027-05-23', end: '2027-08-29', cls: 'p3' },
];
```

### PROGRESS_TARGETS — 各科目标学时
```js
{ '托业TOEIC': 300, 'CFA一级': 350, 'BEC高级': 300, 'CPA': 1600 }
```

### 数据存储
- **localStorage** key: `exam_tracker_YYYY-MM-DD`, value: `[{subject, hours, ts}]`
- **data.json** (GitHub): `{ "2026-06-25": [{...}], ... }`
- **gh_token**: 存在 localStorage，用户首次打开弹窗输入

## 同步机制

1. **推送**: `addTrackerEntry()` / `deleteTrackerEntry()` 后自动调 `syncToCloud()`
2. **拉取**: 页面初始化调 `loadCloudData()`，读 data.json → 按日期+时间戳智能合并进 localStorage
3. **API**: GitHub Contents API (api.github.com)，读不用 token，写需 Bearer token
4. **编码**: 写用 `btoa(unescape(encodeURIComponent()))`，读用 `decodeURIComponent(escape(atob()))`

## 本地备份路径

```
#4Career/study-plan_v1.html  → v9.html（版本历史）
```

## 修改流程

1. `git clone https://github.com/jinluo1993-ux/study-plan.git`
2. 改 index.html
3. `git commit -m "描述"` → `git push origin main`
4. 本地备份 `cp index.html "#4Career/study-plan_vN.html"`

### 重要规则
- **每次改动单独 commit，不批处理**
- **每次 push 前备份本地版本**
- **阶段标签格式**: 托业1期、CFA1期（不用"第"字，除非用户要求）
- **日期修改前必须查官网验证**（test-toeic.cn、cfainstitute.org）

## Token 信息

GitHub PAT（public_repo 权限）存在用户 localStorage `gh_token` 中，全设备共享。
如需重新生成: https://github.com/settings/tokens → classic token → public_repo 权限。
