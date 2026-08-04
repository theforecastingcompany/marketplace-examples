# Marketplace examples

Sample notebooks and request/response data for The Forecasting Company's models as
published on cloud marketplaces.

If you have subscribed to one of our listings, start with the notebook for your
cloud — it runs end to end and needs nothing from you but the Model Package ARN your
subscription provides.

## Licence

The example code in this repository is licensed under **Apache-2.0** — copy it into
your own projects freely.

**The models themselves are not open source.** T0, including its weights and its
serving container, is proprietary and licensed separately under the agreement through
which you obtain it from a cloud marketplace. Nothing in this repository grants any
licence to any model.

## Layout

```
samples/          request and response examples — identical across clouds
aws/t0/           T0 on AWS Marketplace (SageMaker)
```

The wire format is the same wherever a model is deployed, so `samples/` lives once at
the top level. Per-cloud directories hold only the deployment glue that genuinely
differs — the SageMaker SDK calls, the Vertex AI calls, and so on.

## AWS — T0

[`aws/t0/notebook.ipynb`](aws/t0/notebook.ipynb) covers the full lifecycle:

- deploying a real-time endpoint from your Model Package ARN
- forecasting a series and reading the quantile interval, checked against held-back data
- conditioning on known-future covariates such as holidays or planned promotions
- running a batch transform job over many series from a file
- tearing everything down

Run it in SageMaker Studio with an execution role that can create models, endpoints
and transform jobs, and read and write S3. Deploy in the same AWS Region as your
subscription.

**Instance types.** T0 runs on GPU — `ml.g4dn.*` or `ml.g5.*` — for both real-time
endpoints and batch transform jobs.

## Support

Questions about a subscription or a deployment: **support@theforecastingcompany.com**
