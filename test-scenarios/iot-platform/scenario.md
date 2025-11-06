# Test Scenario 3: IoT Data Platform

## Scenario Description

**Project:** Real-time IoT device telemetry platform with alerting

**User Request (Initial):**
"We're building a platform to collect telemetry from thousands of IoT sensors (temperature, humidity, pressure). We need to store this data, visualize it in real-time, and trigger alerts when values go out of acceptable ranges."

**Complexity Level:** High
- High-volume data ingestion (thousands of devices, frequent updates)
- Real-time processing
- Time-series data storage
- Alert/notification system
- Device management

## Expected Skill Behavior

### Phase 1: Requirements
- Should ask about data volume (devices count, update frequency)
- Should ask about latency requirements (real-time vs near-real-time)
- Should ask about data retention policy
- Should ask about alert delivery methods (email, SMS, webhook)
- Should ask about device provisioning/authentication
- Should ask about scale constraints (storage, processing)

### Phase 2: Big Picture
- Should identify events: DeviceRegistered, TelemetryReceived, DataStored, ThresholdExceeded, AlertTriggered, AlertSent, DeviceOffline, DeviceReconnected
- Should identify commands: RegisterDevice, SendTelemetry, SetThreshold, ConfigureAlert, AcknowledgeAlert, UpdateDeviceConfig
- Should identify actors: Device, Operator, Admin, Maintenance Technician
- Should identify systems: Time-Series Database, Message Queue, Notification Service, Dashboard UI
- Should mark hotspots: Data retention policy, alert fatigue (too many alerts), device authentication, network reliability

### Phase 3: Process EventStorming
- Should zoom into critical processes:
  - Telemetry ingestion process
  - Alert evaluation process
  - Device registration/provisioning process
  - Alert notification process
  - Data aggregation/downsampling process
- Should identify aggregates: Device, Telemetry, Threshold, Alert, AlertRule, DeviceConfig
- Should show data flow and processing pipeline

### Phase 4: System Design
- **ERD**: Should include entities: Device, DeviceType, TelemetryReading, Threshold, AlertRule, Alert, Notification, User, DeviceConfig
- **State Charts**: Should create for:
  - Device lifecycle (provisioning → active → offline → reconnected → decommissioned)
  - Alert state (triggered → notified → acknowledged → resolved)
  - Telemetry processing (received → validated → stored → aggregated)
- **Sequence Diagrams**: Should create for:
  - Device registration flow
  - Telemetry ingestion and storage flow
  - Alert evaluation and notification flow
  - Real-time dashboard update flow

### Phase 5: Integration
- Should generate catalog README
- Should potentially suggest time-series database (InfluxDB, TimescaleDB)
- Should suggest message queue for ingestion (Kafka, MQTT)
- Should ask about implementation planning

## Pressure Points to Test

1. **Scale considerations**: Will agent address high-volume data ingestion?
2. **Real-time requirements**: Will agent model async processing correctly?
3. **Data retention**: Will agent consider downsampling/aggregation strategies?
4. **Alert fatigue**: Will agent mark this as a hotspot?
5. **System integrations**: Will agent identify necessary external systems?
6. **Device management complexity**: Will agent handle provisioning, auth, config updates?

## Expected Artifacts

```
docs/design-catalog/
  README.md
  requirements.md
  big-picture.mmd
  processes/
    process-telemetry-ingestion.mmd
    process-alert-evaluation.mmd
    process-device-registration.mmd
    process-notification.mmd
    process-data-aggregation.mmd
  data/
    erd.mmd
    state-device.mmd
    state-alert.mmd
    state-telemetry.mmd
  flows/
    sequence-device-registration.mmd
    sequence-telemetry-ingestion.mmd
    sequence-alert-flow.mmd
    sequence-dashboard-update.mmd
```

## Test Questions During Execution

- Mid-Phase 1: "We also need to support firmware updates over-the-air (OTA)"
- Mid-Phase 2: "What about batch data upload when devices come back online?"
- Phase 3: "How do we handle alert rules that depend on multiple sensors?"
- Phase 4: "Should we add device groups/hierarchies for organizational structure?"
- Phase 4: "We need historical data export for compliance audits"

## Success Criteria

- Addresses high-volume data concerns
- Models async/real-time processing appropriately
- Identifies appropriate external systems (time-series DB, message queue)
- Handles device lifecycle complexity
- Creates state charts for devices and alerts
- Marks operational concerns as hotspots (alert fatigue, retention)
- Handles iterative additions (OTA updates, batch upload, exports)
- Suggests appropriate technology choices for scale
