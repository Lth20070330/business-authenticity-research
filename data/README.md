# 数据目录约定

仅跟踪此说明、来源登记和空白标注模板。真实文件在本地 `data/raw/`、`data/interim/`、`data/processed/` 管理，默认不进入 Git。目录可在获取数据后创建。

`source_registry.csv` 记录来源与权限，`annotation_template.csv` 仅为表头。请勿将真实标注写入该受版本控制的模板；复制到被忽略的本地目录后使用。

公开样例也需确认无真实标识和再分发限制，再有意调整忽略规则。不要通过 `git add -f` 绕过数据约定。
