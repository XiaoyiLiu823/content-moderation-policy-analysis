版本迭代中

第一版：已实现
A. overall_metrics.csv
对比 v1 / v2 的总体指标（每行一个版本）：
total_requests（去重后总请求数）
refusal_rate
avg_latency_ms
p50_latency_ms
p90_latency_ms
avg_output_len
p50_output_len
p90_output_len
like_rate（user_vote==1 的占比）
dislike_rate（user_vote==-1 的占比）

B. refusal_reason_diff.csv
按 refusal_reason 分桶统计并对比：
v1_refusal_cnt、v1_refusal_rate（分母用该版本总请求数）
v2_refusal_cnt、v2_refusal_rate
delta_rate = v2_refusal_rate - v1_refusal_rate
按 delta_rate 降序排序，输出全量。

C. scenario_regression_topn.csv
找“退化最明显”的场景 TopN（用 config里的 topn）：
你需要先按 scenario 分桶，计算每个版本：
refusal_rate
dislike_rate
avg_latency_ms
然后计算差值：
delta_refusal_rate
delta_dislike_rate
delta_latency_ms

D. bad_cases_sample.csv
抽取“疑似误拒/体验差”的样本，方便人工看：
满足任一条件就入选：
is_refusal==1 且 refusal_reason=="RISK_KEYWORD"
latency_ms >= bad_latency_threshold_ms
output_len <= short_output_threshold_chars 且 is_refusal==0


第二版：预计增加数据清洗，补齐调用入口

version, request_id, ts, scenario, is_refusal, refusal_reason, latency_ms, output_len, user_vote, output_text

每个版本各抽取最多 50 条（如果不足就全出）。抽样规则你定，但要可复现（比如固定 random_state 或取最新/最早）。
