# 日本机器人与自动化教授研究导航

面向日本修士申请准备的交互式研究导航，汇总目标教授、研究方向、教授/研究室/学院分野入口，以及近三年代表论文、原文链接和发表平台初步分级。

在线页面：<https://sheldon2233.github.io/OwnPaperSearchWeb/>

## 数据驱动更新

- `index.html` 是稳定的产品界面。
- `data.json` 是独立数据文件，页面启动时加载，并每 60 秒检查一次新版本。
- 论文明细自动拆分到 `data/publications-*.json`；`data.json` 只保存清单和教授数据。
- 日常更新教授或论文时，只需重新生成并提交 `data.json`；无需重新打包页面。
- 页面会保留最近一次成功加载的数据；短暂网络失败不会清空当前内容。
- Excel 缺失或错配的教授、研究室与学院/分野链接由 `work/professor_analysis/link_overrides_reviewed.json` 覆盖；每项记录官网证据、核验日期与说明，刷新 Excel 后仍会自动应用。
- 用户指定且不在原 Excel 数字顺序行中的教员记录保存在 `work/professor_analysis/professor_additions_reviewed.json`，包含官网证据、职级、论文身份标识和姓名纠错，刷新 Excel 时会自动合并并去重。
- 论文刷新默认并行查询 researchmap 与 OpenAlex，对缺口再调用 Crossref；上次已复核入选记录会作为保底证据合并，避免单次上游限流造成网页数据回退。

日常仅更新数据：

```powershell
npm run refresh:data -- -PagesRepoPath "..\OwnPaperSearchWeb"
```

界面代码发生变化时执行完整构建：

```powershell
npm run refresh:site -- -PagesRepoPath "..\OwnPaperSearchWeb"
```

两条命令都会提取 Excel、合并用户指定新增教员、刷新公开论文源、应用已复核链接，并校验链接格式、三年时间范围、姓名唯一性及每位教员最多 20 篇规则。数据模式只生成 `data.json` 与论文 JSON 分片；完整模式还会重新构建页面静态资源。需要复用已有论文快照而不访问外部学术 API 时，可追加 `-SkipPublicationFetch`。
