# Sample requests and responses

Real model output, not illustrations — every response here was produced by the
published container, so you can diff your own results against them.

| File | What it is |
| --- | --- |
| `realtime-request.json` | One forecast request: 72 hourly observations, 12-step horizon, p10/p50/p90 |
| `realtime-response.json` | The response — `quantiles[step][level]` plus the echoed levels |
| `batch-input.jsonl` | Three series for batch transform, one request per line, differing lengths and horizons |
| `batch-output.jsonl` | The three responses, one per line, in input order |

## The request

| Field | Type | Required | Meaning |
| --- | --- | --- | --- |
| `context` | `list[float]` | yes | Past observations, oldest first. Up to 16,384 points. |
| `horizon` | `int` | yes | Steps to forecast, 1 to 2,048. |
| `quantiles` | `list[float]` | no | Levels in `(0, 1)`, ascending. Defaults to `[0.1, 0.5, 0.9]`. |
| `future_covariates` | `list[list[float]]` | no | Shaped `[n_covariates][len(context) + horizon]` — supplied across the history *and* the horizon. |

You send these with `predictor.predict()` (SageMaker Python SDK) or
`InvokeEndpoint` (boto3 or the AWS CLI). The endpoint accepts either a single
request object or a JSON list of them — the list form is what makes the batch file
above work.

## The response

| Field | Type | Meaning |
| --- | --- | --- |
| `quantiles` | `list[list[float]]` | `[horizon][n_quantiles]` |
| `quantile_levels` | `list[float]` | The levels, in the order requested |

## Missing values

Send gaps as `NaN`, **not** `null`. A literal `null` is rejected with HTTP 422
(`Input should be a valid number`).

JSON has no NaN literal, so you need an encoder that emits the non-standard `NaN`
token. Python's standard-library `json.dumps` does by default, as does the SageMaker
SDK's `JSONSerializer`. Strict encoders such as `orjson` refuse to emit it — prefer
the standard library for series containing gaps.

That is also why none of the sample files above contain a gap: they are valid JSON,
and a file demonstrating `NaN` would not be.

## Batch framing

`batch-input.jsonl` is meant to be read with `split_type="Line"` and
`strategy="SingleRecord"`, so each line is sent to the model separately and the
responses are reassembled one per line, in order. See the notebook for the full call.
