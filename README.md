# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

# AIOps Monitoring and Event Processing

## Scenario

This project monitors a payment-service application. The service produces
performance metrics and log messages. The purpose of the AIOps workflow is to
identify abnormal service behaviour and process it as an event.

## Operational Data

The data is stored in `data/service_data.json`.

Metrics:

- `response_time_ms`
- `cpu_percent`
- `memory_percent`

Log fields:

- `log_level`
- `message`

Each record also contains a timestamp and service name.

## Data Observations

Most records represent normal behaviour. Response times are approximately
120 to 150 milliseconds, CPU usage is approximately 42 to 50 percent, memory
usage is approximately 51 to 57 percent, and the log level is `INFO`.

Two records are abnormal:

- At `10:05`, response time is `610 ms` and the log reports a payment service timeout.
- At `10:06`, response time is `640 ms`, CPU is `94%`, memory is `91%`, and the log reports a database connection timeout.

## AAnomaly Detection

The detector uses fixed thresholds:

- Response time above `500 ms`
- CPU above `80%`
- Memory above `80%`
- Log level equal to `ERROR`

The detector found two anomalies.

## Event Processing Flow

The workflow is:

Operational data -> Anomaly detector -> Event -> Producer -> Topic -> Consumer -> AIOps output

The producer publishes anomaly events to an in-memory topic. The consumer
reads events from the same topic and returns them as processed output.

## Problems Found and Corrections

The detector originally checked for `WARNING`, but the supplied data uses
`ERROR`. The detector was corrected to identify `ERROR` log records.

The producer and consumer originally used different topics. The producer used
`service-events`, while the consumer used `anomaly-events`. They were corrected
to use the same topic.

## Final Result

The final execution processed 10 records, detected 2 anomalies, and consumed
2 anomaly events successfully.

## Limitation

The detector uses fixed thresholds. These values may not be suitable for every
service and may fail to detect unusual behaviour that remains below the
thresholds. A future improvement would be dynamic thresholds based on historical
service behaviour.

## How to Reproduce

Install dependencies:

```bash
pip install -r requirements.txt

Run the pipeline:
python3 src/aiops_pipeline.py


Run the tests:
python3 -m pytest -q

