# Host-Based Threat Emulation and SOC Detection Lab

## Objective

Build and validate a Windows endpoint monitoring workflow using Sysmon/Windows security telemetry, Wazuh, and Splunk. Simulate selected Living-off-the-Land (LotL) behaviors in an isolated lab, examine the resulting events, and identify gaps in endpoint visibility.

## Investigation workflow

1. Simulate selected command-line and registry-based behaviors on the lab Windows endpoint.
2. Inspect the generated Windows telemetry and identify missing process command-line visibility.
3. Enable the relevant Windows process-creation auditing settings.
4. Repeat the controlled test and verify the resulting event data.
5. Search the ingested events in Splunk and document the evidence supporting detection.
6. Record the detection outcome, remaining visibility gaps, and recommended defensive improvements.

## Evidence and validation

Document the relevant event IDs, timestamps, host, process image, command line, registry changes, SPL searches, and screenshots from successful test runs.

Distinguish between telemetry successfully collected, suspicious activity identified, and an alert successfully generated. These are separate validation outcomes.

## Scope and limitations

This is a controlled learning lab, not a production SOC deployment. Findings demonstrate the behavior observed in the configured environment and should not be generalized to systems with different logging policies or detection configurations.
