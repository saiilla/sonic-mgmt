# Overview

This document will cover the end-to-end test plan of gNMI alarms for GPINs. The test plan will mainly test 2 areas:

-  The report of gNMI alarms. This includes 6 alarm paths. Verify the format and the reporting behavior of those paths.
    -  The format of the gNMI alarm paths is documented [here](http://doc/1T7ZH8ppBM8DDvgPraItb-uNSHn3Zr2GOszkfGU83vXg#heading=h.nq0xmdw9c84w).
    -  The gNMI alarms can only be cleared with system reboot. New gNMI get or subscribe requests should be able to read all existing alarms.
    -  The gNMI alarms should be clear after system reboot.

-  For critical alarms (system critical state), verify the correct system behavior. This will include:
    -  P4rt
        -  Stop processing write requests.
        -  Continue to process read requests.
        -  Continue to perform packetIO.

    -  gNMI
        -  Stop processing write (set) requests.
        -  Continue to process read (get & subscribe) requests.

    -  gNOI
        -  Stop processing Diag service (linkqual & burnin).
        -  Continue to process other gNOI services.

    -  gNSI
        -  Continue to process gNSI requests.

# Test Cases

## gNMI Alarm Path Verification

The gNMI alarm path verification tests are also documented in the [alarm path document](http://doc/1T7ZH8ppBM8DDvgPraItb-uNSHn3Zr2GOszkfGU83vXg#heading=h.xmzi87ws5g1t). The tests will verify the correct alarm filed format and the correct alarm report behavior.  
For background information, there are two alarm severities: WARNING and CRITICAL. And there are also two ways to raise an alarm: a component goes to ERROR state or INACTIVE state. This gives a total of 4 combinations (plus every test goes over all components, see details below). More information can be found in the [GPIN state document](go/gpins-state) and the [alarm path document](go/gpins-state-openconfig-paths).

### Test Error State Critical Alarm

<table>
  <thead>
    <tr>
      <th>Testbeds:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the essential component {p4rt, orchagent, syncd, telemetry, host}, do the following:<br>
<ul>
<li>Use gNMI client to subscribe to the gNMI alarm paths (/system/alarms).</li>
</ul>
<ul>
<li>Use gNOI SetAlarm API to set ERROR state for the target essential component.If the gNOI SetAlarm is not available, ssh into the switch and use redis-cli to set ERROR state.</li>
</ul>
<ul>
<li>Disconnect the gNMI client and reconnect gNMI client for subscription.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch. After the switch is rebooted, reconnect the gNMI client for subscription.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the essential components being set above):<br>
<ul>
<li>No alarm before calling gNOI SetAlarm API.</li>
</ul>
<ul>
<li>A CRITICAL severity alarm will be generated when an essential component changes to state ERROR.</li>
</ul>
<ul>
<li>Verify alarm id has format: "<component_id>_<timestamp_in_nanoseconds>".</li>
</ul>
<ul>
<li>Verify alarm text has format: "<component_state_string>: <reason>".</li>
</ul>
<ul>
<li>Verify alarm type-id.</li>
</ul>
<ul>
<li>Verify alarm resource is the component id.</li>
</ul>
<ul>
<li>Verify alarm severity is CRITICAL.</li>
</ul>
<ul>
<li>Verify alarm time-created is the timestamp in nanoseconds.</li>
</ul>
<ul>
<li>Verify chassis alarm status is true.</li>
</ul>
<ul>
<li>After reconnection, verify the alarm is raised for new gNMI subscription (after the alarm event has occurred.)</li>
</ul>
<ul>
<li>After reboot, verify the alarm is cleared.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Test Inactive State Critical Alarm

<table>
  <thead>
    <tr>
      <th>Testbeds:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the essential component {p4rt, orchagent, syncd, telemetry, host}, do the following:<br>
<ul>
<li>Use gNMI client to subscribe to the gNMI alarm paths (/system/alarms).</li>
</ul>
<ul>
<li>Use gNOI SetAlarm API to set INACTIVE state for the target essential component.If the gNOI SetAlarm is not available, ssh into the switch and use redis-cli to set INACTIVE state.</li>
</ul>
<ul>
<li>Disconnect the gNMI client and reconnect gNMI client for subscription.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch. After the switch is rebooted, reconnect the gNMI client for subscription.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the essential components being set above):<br>
<ul>
<li>No alarm before calling gNOI SetAlarm API.</li>
</ul>
<ul>
<li>A CRITICAL severity alarm will be generated when an essential component changes to state INACTIVE.</li>
</ul>
<ul>
<li>Verify alarm id has format: "<component_id>_<timestamp_in_nanoseconds>".</li>
</ul>
<ul>
<li>Verify alarm text has format: "<component_state_string>: <reason>".</li>
</ul>
<ul>
<li>Verify alarm type-id.</li>
</ul>
<ul>
<li>Verify alarm resource is the component id.</li>
</ul>
<ul>
<li>Verify alarm severity is CRITICAL.</li>
</ul>
<ul>
<li>Verify alarm time-created is the timestamp in nanoseconds.</li>
</ul>
<ul>
<li>Verify chassis alarm status is true.</li>
</ul>
<ul>
<li>After reconnection, verify the alarm is raised for new gNMI subscription (after the alarm event has occurred.)</li>
</ul>
<ul>
<li>After reboot, verify the alarm is cleared.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Test Error State Warning Alarm

<table>
  <thead>
    <tr>
      <th>Testbeds:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the non-essential component {linkqual}, do the following:<br>
<ul>
<li>Use gNMI client to subscribe to the gNMI alarm paths (/system/alarms).</li>
</ul>
<ul>
<li>Use gNOI SetAlarm API to set ERROR state for the target non-essential component. If the gNOI SetAlarm is not available, ssh into the switch and use redis-cli to set ERROR state.</li>
</ul>
<ul>
<li>Disconnect the gNMI client and reconnect gNMI client for subscription.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch. After the switch is rebooted, reconnect the gNMI client for subscription.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the non-essential components being set above):<br>
<ul>
<li>No alarm before calling gNOI SetAlarm API.</li>
</ul>
<ul>
<li>A WARNING severity alarm will be generated when a non-essential component changes to state ERROR.</li>
</ul>
<ul>
<li>Verify alarm id has format: "<component_id>_<timestamp_in_nanoseconds>".</li>
</ul>
<ul>
<li>Verify alarm text has format: "<component_state_string>: <reason>".</li>
</ul>
<ul>
<li>Verify alarm type-id.</li>
</ul>
<ul>
<li>Verify alarm resource is the component id.</li>
</ul>
<ul>
<li>Verify alarm severity is WARNING.</li>
</ul>
<ul>
<li>Verify alarm time-created is the timestamp in nanoseconds.</li>
</ul>
<ul>
<li>Verify chassis alarm status is false.</li>
</ul>
<ul>
<li>After reconnection, verify the alarm is raised for new gNMI subscription (after the alarm event has occurred.)</li>
</ul>
<ul>
<li>After reboot, verify the alarm is cleared.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Test Inactive State Warning Alarm

<table>
  <thead>
    <tr>
      <th>Testbeds:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the non-essential component {linkqual}, do the following:<br>
<ul>
<li>Use gNMI client to subscribe to the gNMI alarm paths (/system/alarms).</li>
</ul>
<ul>
<li>Use gNOI SetAlarm API to set INACTIVE state for the target non-essential component. If the gNOI SetAlarm is not available, ssh into the switch and use redis-cli to set INACTIVE state.</li>
</ul>
<ul>
<li>Disconnect the gNMI client and reconnect gNMI client for subscription.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch. After the switch is rebooted, reconnect the gNMI client for subscription.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the non-essential components being set above):<br>
<ul>
<li>No alarm before calling gNOI SetAlarm API.</li>
</ul>
<ul>
<li>A WARNING severity alarm will be generated when a non-essential component changes to state INACTIVE.</li>
</ul>
<ul>
<li>Verify alarm id has format: "<component_id>_<timestamp_in_nanoseconds>".</li>
</ul>
<ul>
<li>Verify alarm text has format: "<component_state_string>: <reason>".</li>
</ul>
<ul>
<li>Verify alarm type-id.</li>
</ul>
<ul>
<li>Verify alarm resource is the component id.</li>
</ul>
<ul>
<li>Verify alarm severity is WARNING.</li>
</ul>
<ul>
<li>Verify alarm time-created is the timestamp in nanoseconds.</li>
</ul>
<ul>
<li>Verify chassis alarm status is false.</li>
</ul>
<ul>
<li>After reconnection, verify the alarm is raised for new gNMI subscription (after the alarm event has occurred.)</li>
</ul>
<ul>
<li>After reboot, verify the alarm is cleared.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

## P4rt Critical State Behavior Verification

These tests will verify the correct p4rt behaviors when the system is in critical state. P4rt should allow read request but reject write request, and also perform packetIO during critical state.

### Test P4rt Read in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Program a table entry.</li>
</ul>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm. If the gNOI SetAlarm is not available, ssh into the switch and use redis-cli to set the DB.</li>
</ul>
<ul>
<li>Issue a p4rt read request to read the programmed entry.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch to clear critical state.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>The read operation returns an OK status.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Test P4rt Write in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm. If the gNOI SetAlarm is not available, ssh into the switch and use redis-cli to set the DB.</li>
</ul>
<ul>
<li>Issue a few of p4rt write requests. They should include insert, modify, and delete requests.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch to clear critical state.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>All write operations return failure.</li>
</ul>
<ul>
<li>The error message should mention that the failure was due to the critical state. The error message should also include the reasons for the critical state.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Test P4rt PacketIO in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Setup packetIO testing on the mirror testbed. This will include the ACL rule programming in the control switch and the SUT.</li>
</ul>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm. If the gNOI SetAlarm is not available, ssh into the switch and use redis-cli to set the DB.</li>
</ul>
<ul>
<li>Trigger a packet-in on the SUT.</li>
</ul>
<ul>
<li>Trigger a packet-out on the SUT.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch to clear critical state.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>Both packet-in and packet-out function normally when the system is in critical state.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

## gNMI Critical State Behavior Verification

The gNMI behavior tests will verify the gNMI behaviors in critical state. GNMI should allow read requests (get and subscribe) but reject write requests (set) when the system is in critical state. It is recommended to run these tests in the software component test framework since it is less expensive than the hardware testbed.

### Test gNMI Read in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Software component test frameworks.</li>
</ul>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm. In software component test frameworks, this can be done by modifying the DB.</li>
</ul>
<ul>
<li>Issue a gNMI get (for any path).</li>
</ul>
<ul>
<li>Issue a gNMI subscribe (for any path).</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch to clear critical state. In software component tests, this can be done by cleaning the DB.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>Both get and subscribe return successfully when the system is in critical state.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Test gNMI Write in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Software component test frameworks.</li>
</ul>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
<ul>
<li>Single vGPINs setup.</li>
</ul>
<ul>
<li>Mirror vGPINs setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm. In software component test frameworks, this can be done by modifying the DB.</li>
</ul>
<ul>
<li>Issue a gNMI set (for any path).</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch to clear critical state. In software component tests, this can be done by cleaning the DB.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>The gNMI set returns failure during critical state.</li>
</ul>
<ul>
<li>The error message should mention that the failure was due to the critical state. The error message should also include the reasons for the critical state.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

## gNOI Critical State Behavior Verification

The gNOI behavior tests will verify the gNOI behaviors in critical state. GNOI should reject Diag service and allow all other services when the system is in critical state.

### Test gNOI Diag in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm.</li>
</ul>
<ul>
<li>Issue a few gNOI Diag services. It should include StartBERT, StartPacketQualification, StartBurnin.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch to clear critical state.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>All gNOI Diag services return failure during critical state.</li>
</ul>
<ul>
<li>The error message should mention that the failure was due to the critical state. The error message should also include the reasons for the critical state.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Test gNOI Installation in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm.</li>
</ul>
<ul>
<li>Perform full image installation through gNOI.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>Switch image installation is successful.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

## gNSI Critical State Behavior Verification

The gNSI behavior tests will verify the gNSI behaviors in critical state. GNSI should allow all services to perform when the system is in critical state.

### Test gNSI Certificate Management in Critical State

<table>
  <thead>
    <tr>
      <th>Testbed:<br>
<ul>
<li>Single standalone hardware switch.</li>
</ul>
<ul>
<li>Mirror switch setup.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Steps:<br>
For each of the CRITICAL alarms ({p4rt, orchagent, syncd, telemetry, host} x {ERROR, INACTIVE}), do the following:<br>
<ul>
<li>Use gNOI SetAlarm API to set a CRITICAL alarm.</li>
</ul>
<ul>
<li>Perform mTLS cert rotation.</li>
</ul>
<ul>
<li>Perform ssh cert rotation.</li>
</ul>
<ul>
<li>Use gNOI API to cold reboot the switch to clear critical state.</li>
</ul>
</td>
    </tr>
    <tr>
      <td>Verify (for each of the CRITICAL alarms above):<br>
<ul>
<li>All gNSI certificate management operations are successful.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>
