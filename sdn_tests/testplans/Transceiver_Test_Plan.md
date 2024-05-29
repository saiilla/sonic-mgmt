# Objective

This document describes the planned tests to cover the 19 current transceiver-related gNMI paths (those containing the string "/components/component[name=<transceiver>]/"). These include 3 config paths and 16 telemetry paths

# Overview

## Paths Covered

The below paths are covered in the tests in this document:

<table>
  <thead>
    <tr>
      <th><br>
/components/component[name=    <transceiver>]/config/name</th>
      <th><br>
Config</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/config/fec-mode</td>
      <td><br>
Config</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/state/fec-mode</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/config/index</td>
      <td><br>
Config</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/index</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/name</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/mfg-name</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/mfg-date</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/part-no</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/serial-no</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/firmware-version</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/type</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/parent</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/empty</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/state/ethernet-pmd</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/laser-bias-current/instant</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/input-power/instant</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/output-power/instant</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/state/temperature/instant</td>
      <td><br>
Telemetry</td>
    </tr>
    <tr>
      <td><br>
/components/component[name=    <transceiver>]/transceiver/state/form-factor</td>
      <td><br>
Telemetry</td>
    </tr>
  </tbody>
</table>

## Testbed Requirements

All tests in this suite can be run on mirror testbeds provided they have optical modules installed. Testbeds connected only to Ixia or hosts may also be used except where otherwise noted. vGPINs generally will not work since these are testing transceiver-related functionality. Arista testbeds have a different platform layer from Google-built switches, so are likewise not suitable.

# Test Cases

## Read Name

### Testbed

-   any testbed with at least one transceiver present

### Test Steps

<table>
  <thead>
    <tr>
      <th><strong>Procedure</strong></th>
      <th><ul>
<li>Traverse the config to find a transceiver which is present (state/empty=false)</li>
</ul>
<ul>
<li>Read the state/name path for the transceiver</li>
</ul>
<ul>
<li>Verify the name is of the form /Ethernet[0-9]+/</li>
</ul>
<ul>
<li>Verify config path and state path match</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Expected Results</strong></td>
      <td><ul>
<li>At least on transceiver found, empty=false</li>
</ul>
<ul>
<li>Names match</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Paths Tested

<table>
  <thead>
    <tr>
      <th>/components/component[name=    <transceiver>]/config/name</th>
      <th>Config</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/name</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/empty</td>
      <td>Telemetry</td>
    </tr>
  </tbody>
</table>

## Index

### Testbed

-   any testbed with at least one transceiver present

### Test Steps

<table>
  <thead>
    <tr>
      <th><strong>Procedure</strong></th>
      <th><ul>
<li>Find a transceiver which is present (state/empty=false)</li>
</ul>
<ul>
<li>read transceiver path for an OSFP module /components/component[name=    <transceiver>]/transceiver/physical-channels/channel</li>
</ul>
<ul>
<li>verify number of indexes present</li>
</ul>
<ul>
<li>verify that all indices are unique for the transceiver</li>
</ul>
<ul>
<li>verify that state paths match config paths</li>
</ul>
<ul>
<li>request an invalid index (negative or 8 for OSFP, 4 for QSFP), verify that the request fails</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Expected Results</strong></td>
      <td><ul>
<li>8 lanes present for all OSFP modules, starting with lane 0</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Paths Tested

<table>
  <thead>
    <tr>
      <th>/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/config/index</th>
      <th>Config</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/index</td>
      <td>Telemetry</td>
    </tr>
  </tbody>
</table>

## Read Transceiver Static Data

### Testbed

-   Any testbed with at least one transceiver present

### Test Steps

<table>
  <thead>
    <tr>
      <th><strong>Procedure</strong></th>
      <th><ul>
<li>Traverse the config to find a transceiver which is present.</li>
</ul>
<ul>
<li>Verify the manufacturer string information is present and non-blank</li>
</ul>
<ul>
<li>mfg-name</li>
</ol>
</li>
</ul>
<ul>
<li>part-no</li>
</ol>
</li>
</ul>
<ul>
<li>serial-no</li>
</ol>
</li>
</ul>
<ul>
<li>mfg-date</li>
</ol>
</li>
</ul>
<ul>
<li>Verify ethernet-pmd.</li>
</ul>
<ul>
<li>should not be Unknown, should be in valid list for Google use-cases</li>
</ol>
</li>
</ul>
<ul>
<li>Verify form-factor</li>
</ul>
<ul>
<li>should be OSFP</li>
</ol>
</li>
</ul>
<ul>
<li>Verify type</li>
</ul>
<ul>
<li>should be TRANSCEIVER</li>
</ol>
</li>
</ul>
<ul>
<li>verify parent</li>
</ul>
<ul>
<li>should be of the form 1/N where N is the the transceiver number +1</li>
</ol>
</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Expected Results</strong></td>
      <td><ul>
<li>All fields present and verified</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Paths Tested

<table>
  <thead>
    <tr>
      <th>/components/component[name=    <transceiver>]/state/mfg-name</th>
      <th>Telemetry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/mfg-date</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/part-no</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/serial-no</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/firmware-version</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/transceiver/state/ethernet-pmd</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/transceiver/state/form-factor</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/type</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/parent</td>
      <td>Telemetry</td>
    </tr>
  </tbody>
</table>

## Read Transceiver Dynamic Data

### Testbed

-   Any mirror testbed with at least one optical (active) module present.
    -   All mirror testbeds should fulfill this requirement.

### Test Steps

<table>
  <thead>
    <tr>
      <th><strong>Procedure</strong></th>
      <th><ul>
<li>Traverse the config to find an optical module with link up (state/empty=false)</li>
</ul>
<ul>
<li>All even numbered transceivers should be optical modules in mirror testbeds, possibly can use transceiver/state/ethernet-pmd as well</li>
</ol>
</li>
</ul>
<ul>
<li>Read values for laser bias current, input power, output power and temperature</li>
</ul>
<ul>
<li>Validate all are within range of expected values</li>
</ol>
</li>
</ul>
<ul>
<li>Bias current should be positive</li>
</ol>
</li>
</ol>
</li>
</ul>
<ul>
<li>input, output power should be larger than -30 dBm and less than 10 dBm</li>
</ol>
</li>
</ol>
</li>
</ul>
<ul>
<li>temperature should be in the range of 10C to 55C</li>
</ol>
</li>
</ol>
</li>
</ul>
<ul>
<li>On the control switch, trigger tx_disable for the connected interface</li>
</ul>
<ul>
<li>easiest method is to admin disable the port, which also sets tx_disable for the lanes</li>
</ol>
</li>
</ul>
<ul>
<li>Poll for input-power on the disabled lanes for the SUT to drop below -30dBm, should occur within 1 minute</li>
</ul>
<ul>
<li>Restore tx power on the control switch</li>
</ul>
<ul>
<li>Poll for input power on the disabled lanes for the SUT to be restored, should occur within 1 minute</li>
</ul>
<ul>
<li>On the SUT, trigger tx_disable for the interface</li>
</ul>
<ul>
<li>Poll for output-power on the disabled lane for the SUT to drop below -30dBm, should occur within 1 minute</li>
</ul>
<ul>
<li>Restore tx_disable settings on module</li>
</ul>
<ul>
<li>Poll for values to be restored to valid range. should occur within 1 minute</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Expected Results</strong></td>
      <td><ul>
<li>All values are filled</li>
</ul>
<ul>
<li>for ports that are up, all values are expected to be non-zero</li>
</ol>
</li>
</ul>
<ul>
<li>temperatures reported are in the range of 20-55C</li>
</ul>
<ul>
<li>Verify laser bias current is present and is greater than 0</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

**Paths Tested**

<table>
  <thead>
    <tr>
      <th>/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/laser-bias-current/instant</th>
      <th>Telemetry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/input-power/instant</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/transceiver/physical-channels/channel[index=<lane>]/state/output-power/instant</td>
      <td>Telemetry</td>
    </tr>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/temperature/instant</td>
      <td>Telemetry</td>
    </tr>
  </tbody>
</table>

## Read Parent Path

### Testbed

-   any testbed with at least one transceiver present

### Test Steps

<table>
  <thead>
    <tr>
      <th><strong>Procedure</strong></th>
      <th><ul>
<li>Traverse the config to find a transceiver which is present.</li>
</ul>
<ul>
<li>Read /components/component[name=    <transceiver>]</li>
</ul>
<ul>
<li>Verify that all of the expected fields are returned:</li>
</ul>
<ul>
<li>config/name</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/config/fec-mode</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/state/fec-mode</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/physical-channels/channel[index=<lane>]/config/index</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/physical-channels/channel[index=<lane>]/state/index</li>
</ol>
</li>
</ul>
<ul>
<li>state/name</li>
</ol>
</li>
</ul>
<ul>
<li>state/mfg-name</li>
</ol>
</li>
</ul>
<ul>
<li>state/mfg-date</li>
</ol>
</li>
</ul>
<ul>
<li>state/part-no</li>
</ol>
</li>
</ul>
<ul>
<li>state/serial-no</li>
</ol>
</li>
</ul>
<ul>
<li>state/type</li>
</ol>
</li>
</ul>
<ul>
<li>state/parent</li>
</ol>
</li>
</ul>
<ul>
<li>state/empty</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/state/ethernet-pmd</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/physical-channels/channel[index=<lane>]/state/laser-bias-current/instant <strong>(optics only)</strong></li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/physical-channels/channel[index=<lane>]/state/input-power/instant <strong>(optics only)</strong></li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/physical-channels/channel[index=<lane>]/state/output-power/instant <strong>(optics only)</strong></li>
</ol>
</li>
</ul>
<ul>
<li>state/temperature/instant</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/state/form-factor</li>
</ol>
</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Expected Results</strong></td>
      <td><ul>
<li>All expected paths are populated</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Paths Tested

<table>
  <thead>
    <tr>
      <th>/components/component[name=    <transceiver>]/</th>
      <th>Telemetry</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

## Transceiver Removal

### Testbed

-   any testbed with at least one transceiver present
### Test Steps

<table>
  <thead>
    <tr>
      <th><strong>Procedure</strong></th>
      <th><ul>
<li>Traverse the config to find a transceiver which is present.</li>
</ul>
<ul>
<li>Verify that the transceiver state information is present in gNOI</li>
</ul>
<ul>
<li>Use the <a href="go/gnoi-blackbox-setopticstate">gNOI service <code>SetTransceiverState</a> </code>with<code> state=REMOVED</code> to "fake" removal of the module.</li>
</ul>
<ul>
<li>For 60 seconds, poll the transceiver information every 5 seconds</li>
</ul>
<ul>
<li>if state/empty becomes "true", stop polling</li>
</ol>
</li>
</ul>
<ul>
<li>If state/empty remains false, fail the test</li>
</ul>
<ul>
<li>verify that the following are no longer present</li>
</ul>
<ul>
<li>state/mfg-name</li>
</ol>
</li>
</ul>
<ul>
<li>state/mfg-date</li>
</ol>
</li>
</ul>
<ul>
<li>state/part-no</li>
</ol>
</li>
</ul>
<ul>
<li>state/serial-no</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/physical-channels/channel</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/state/ethernet-pmd-preconf</li>
</ol>
</li>
</ul>
<ul>
<li>transceiver/physical-channels/channel[index=*]/</li>
</ol>
</li>
</ul>
<ul>
<li>Restore the transceiver using  gNOI service <code>SetTransceiverState </code>with<code> state=UNDO_REMOVE</code> to "fake" insertion of the module.</li>
</ul>
<ul>
<li>Verify that within 60 seconds state/empty becomes "false"</li>
</ul>
<ul>
<li>All expected paths for the transceiver should be restored</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Expected Results</strong></td>
      <td><ul>
<li>Transceiver information will be removed within 60 seconds</li>
</ul>
<ul>
<li>Transceiver information will be restored within 60 seconds of insertion</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### Paths Tested

<table>
  <thead>
    <tr>
      <th>/components/component[name=    <transceiver>]/</th>
      <th>Telemetry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/components/component[name=    <transceiver>]/state/empty</td>
      <td>Telemetry</td>
    </tr>
  </tbody>
</table>

##
