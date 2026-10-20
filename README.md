# 农业经济学、制度经济学与农民稳定增收文献 Agent

本项目复制自 lit_digest_agent_mvp。主程序及已有中文格式修复、失败重试、邮件发送功能保持不变，仅调整研究主题、检索词和优先匹配词，以及主题名称、邮件标题和配置文件引用。原项目未修改。不复制历史报告、日志、缓存或密钥。

## 检索范围

1. 农业经济学与农户收入增长：农业生产率、技术采用、商业化、市场参与、小农收入。
2. 制度经济学与农村制度安排：土地权利、产权、交易成本、契约农业、合作社、集体行动。制度经济学以农村应用为重点。
3. 风险冲击与农民收入波动：气候、干旱、洪涝、价格和健康冲击，收入波动与生计。
4. 风险应对、韧性与稳定增收：农业保险、信贷、社会保护、收入平滑、多元化和气候适应。

关键词用英文检索国际数据库，输出中文摘要、总结和评论。每模块3条检索式、最多6篇，检索最近30天，沿用每周一北京时间09:10的计划。风险冲击与稳定增收采用 income volatility、income smoothing、income stabilization、livelihood resilience 等不同表达。

主配置为 config.agricultural_institutional_econ.json；config.example.json 为同主题默认副本。以后修改主题时，workflow 使用前者。本地不传 --config 时使用后者。

按本次只改关键词的范围，OpenAlex经济学筛选、NBER/IZA/World Bank来源、旧期刊加权及排除词均保留。因此尚未增加农业经济学专业来源或期刊权重，不能视为该领域的穷尽检索。中文评议仍仅依据摘要和元数据。

## 上传 GitHub：新建独立仓库

1. 在 GitHub 新建仓库，建议名 agricultural-econ-digest-agent。可勾选创建README，方便进入文件上传页面。使用独立仓库可保留旧agent继续运行。
2. 在新仓库根目录选择 Add file → Upload files，上传本目录内文件（不要将整个 agricultural_econ_digest_agent 外层目录套在仓库根目录）。文件必须包括 literature_digest_agent.py、test_chinese_reviews.py、requirements.txt、config.agricultural_institutional_econ.json、config.example.json。README.md、subscribers.example.json 和本地启动脚本也可以上传。
3. 必须上传真正的 .github/workflows/weekly_digest.yml。Windows中开启“查看→显示→隐藏的项目”，确保能看到 .github 文件夹。若浏览器未保留该目录，使用 Add file → Create new file，文件名输入 .github/workflows/weekly_digest.yml，粘贴本地同名文件全部内容。不要上传旧项目的 .lnk 快捷方式。
4. 提交到默认分支（通常 main）。如果选择其他分支，合并到默认分支后才有定时运行。
5. 在新仓库 Settings → Secrets and variables → Actions → Secrets → New repository secret 中逐个配置以下名称。新仓库不会自动复制旧仓库的Secrets；从你自己的配置中重新填写，不能从旧仓库读取已保存密钥的明文。

必需模型密钥：DEEPSEEK_API_KEY。
邮件设置：SMTP_HOST、SMTP_PORT、SMTP_USERNAME、SMTP_PASSWORD、SMTP_FROM、DIGEST_EMAIL_TO、SMTP_USE_TLS、SMTP_USE_SSL。
DIGEST_EMAIL_TO 填你希望接收农业主题周报的邮箱。SMTP_PASSWORD 使用邮件服务商要求的授权码或应用密码；其余值沿用已成功发信的旧项目配置。
可选模型覆盖：在 Variables 中添加 DEEPSEEK_MODEL；不填则使用配置中的 deepseek-flash。API密钥必须放Secrets，不放Variables或代码。

6. 只给自己发送时，不创建 subscribers.json，workflow自动使用DIGEST_EMAIL_TO。subscribers.example.json只是示例，不会自动触发多人模式。若确需多人订阅，复制为 subscribers.json 并逐项确认邮箱与新配置路径后再上传。
7. 在 Actions → Weekly Literature Digest → Run workflow 中选择 main 手动验证。已配置SMTP时，此操作会真实发邮件。成功后检查邮箱中的主题、中文摘要和评论，并检查运行详情中的 weekly-literature-digest artifact。
8. 保持Actions启用及workflow在默认分支，之后按计划自动触发。GitHub定时任务可能延迟，计划时间不是邮件准时到达保证；检索和模型生成还需要时间。不要另建第二份相同定时workflow，以免重复发信。

## 本地验证

安装依赖：python -m pip install -r requirements.txt
离线测试：python -m unittest -v test_chinese_reviews
生成预览（不会发邮件，需要本地DEEPSEEK_API_KEY）：
python literature_digest_agent.py --config config.agricultural_institutional_econ.json --output-dir outputs_preview

## 常见问题

DEEPSEEK_API_KEY missing：检查新仓库Secrets名称和内容。
Chinese reviews failed：向上查看逐篇Warning，现有代码已兼容常见中文Markdown格式并对字段错误额外重试2次；最终失败仍停止发送。
没有邮件：检查SMTP_HOST和DIGEST_EMAIL_TO是否都设置，确认发送步骤是否被跳过。
没有定时触发：检查默认分支上是否存在正确路径的workflow，以及Actions是否启用。

官方说明：
- https://docs.github.com/en/actions/concepts/workflows-and-actions/workflows
- https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows
- https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets
