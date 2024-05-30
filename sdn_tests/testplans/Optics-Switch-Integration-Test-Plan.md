This plan covers Google optics qualification testing on the system level. The goal is to ensure that newly released optical modules are seamlessly deployed into production switches.

## 3.2. Unified Optics Testbed
### Test Infrastructure

-   Publish dashboard listing all the Optics being covered in the testbed along with their manufacturer name, model, GPN
    -   This data can be obtained by querying CMAL/GNMI on the switches.
-   Run Bert and Link Qualification on the connections to validate traffic on Optics.
-   Ability to bring down/up ports on both Sandcastle (CMAL) and GPINs (GNMI) switches
-   Ability to reboot both Sandcastle and GPINs switches.
-   Ability to test and verify that port speed, modulation, channelization changes don't require a chassis reboot and only port_id changes (i.e. cc, cp, qe, xe, etc.) require a reboot. This needs to be verified on both Sandcastle and GPINs switches.
-   Add ability to run tests on more granular criteria like
    -   Specific pair of chassis Taygeta <--> Brixia
    -   Specific Optic
-   Any optimizations identified under Phase 3 and beyond

One of the main aspects of optics testing is to maximize as many combinations of configurations and connections as possible to get the most coverage. The presence of a palomar in the testbed design makes it possible to rewire connection points as needed. However, not all optics can handle the loss going through a palomar, so direct back-to-back connections must exist in the testbed topology.

# 4. Methodology

A combination of manual and automated tests procedures will be performed.

All test cases will be able to run on the paired and unified optics testbeds. Tests will need to behave slightly differently if the SUT is GPINs or Sandcastle. New tests for the specialized optics testbed are expected to be written and added to the regression workflows. Existing standalone regression tests should be modified to be able to select a single switch from the testbed and run a standard test suite thereby leveraging existing tests.

Manual testing involves constructing custom configurations and pushing to the switch. Actions and verifications are done through cmal_demo/gNOI, the broadcom shell, Linux shell, and/or the cli. For interfaces that have a base configuration from Nimble modeling, chemia *configpush* and *linkqualification* options can be used. When automation is ready, all manual commands should have an associated cmal_demo command along with a stubby API for the switchstack-service to call.

## 5.1. Module initialization

After a configuration is pushed, the module must be recognized correctly by the software.

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Push the default modeled configuration on the switch</li>
</ul>
<ul>
<li>Verify the media information</li>
</ul>
<ul>
<li>Verify the interface information</li>
</ul>
<ul>
<li>Verify the LED state</li>
</ul>
<ul>
<li>Set LED states through gNMI  (<em>/interfaces/interface[name=<port>]/state/health-indicator</em>) and check appropriate sysfs entries [<a href="https://source.corp.google.com/piper///depot/google3/platforms/networking/sandblaze/stack/hal/lib/bcm/bcm_tomahawk_led_manager.cc;l=119-153;rcl=353732109">BCM LED Manager code</a>][<a href="http://google3/platforms/networking/sandblaze/stack/ofa/lib/providers/netdev_port_ofa.cc?l=633-637&rcl=364639074">OFA LED Control</a>] [<a href="http://go/gpins-leds">go/gpins-leds</a>]</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Optics model, part number, serial number and vendor should be read correctly over CMAL/gNMI. The Rx light level should show some readings within spec.</li>
</ul>
<ul>
<li>Link status, statistics and counters for the associated interface should be readable.</li>
</ul>
<ul>
<li>There should be no presence of SerDes errors in Sandcastle/GPINs switch logs.</li>
</ul>
<ul>
<li>Sysfs entries should be as below:<br>
<em>LED State   -   Entry in /sys/devices/gfpga-platform/osfp_led_*</em><br>
Off (Down)  - 0x0</li>
</ul>
Green (Up, good) - 0x1<br>
Amber (Admin down) - 0x2<br>
Blinking Green - 0x5 <em>[this may not be doable]</em><br>
Blinking Amber (Up, bad) - 0x6</td>
    </tr>
  </tbody>
</table>

## 5.2. Link operations
### 5.2.1. Verify the link comes up between all pairs of connected optics modules.

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>In a stable state (without any operations) perform the following checks:</li>
</ul>
<ul>
<li>Verify the port states over gNMI/CMAL</li>
</ol>
</li>
</ul>
<ul>
<li>Verify <em>show interfaces</em> over cli.</li>
</ol>
</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>LED state should be green</li>
</ul>
<ul>
<li>gNMI/CMAL response should have port up.</li>
</ul>
<ul>
<li>From the cli, <em>show interfaces</em> should show the port in Up state.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.2. Link Toggling (admin disable one)

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Bring down the link on the near end and verify the link status is down on both ends.</li>
</ul>
<ul>
<li>Reverse the operation and bring down the link on the far end.</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Verify the LED state</li>
</ul>
<ul>
<li>Verify link goes down when near/far end is brought down on both ends</li>
</ul>
<ul>
<li>Verify link comes up when near/far end is brought up on both ends</li>
</ul>
<ul>
<li>Verify no other links affected on both ends</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.3. Simulate optic pull out and plug in (one)

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Call the fake_sfp_removal script to remove the sysfs entry for the optic (SSH then gNOI)</li>
</ul>
<ul>
<li>Call it again to bring it back</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Verify the LED state</li>
</ul>
<ul>
<li>Verify link goes down on both ends</li>
</ul>
<ul>
<li>Verify link comes up on both ends</li>
</ul>
<ul>
<li>Verify no other links affected</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.4. Link Toggling (admin disable all)

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Bring down all links on the near end and verify the link status is down on both ends.</li>
</ul>
<ul>
<li>Reverse the operation and bring down all links on the far end.</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Verify the LED state</li>
</ul>
<ul>
<li>Verify links go down when near/far end is brought down on both ends</li>
</ul>
<ul>
<li>Verify links come up when near/far end is brought up on both ends</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.5. Simulate optic pull out and plug in (all)

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Call the fake_sfp_removal script to remove the sysfs entry for all optics.</li>
</ul>
<ul>
<li>Call it again to bring it back</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Verify the LED state</li>
</ul>
<ul>
<li>Verify links go down on both ends</li>
</ul>
<ul>
<li>Verify links come up on both ends</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.6. Verify links come up within an established threshold of time.

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>For <a href="#heading=h.lljknhg0rgci">5.2.2</a>, <a href="#heading=h.cva5i74xrc3n">5.2.3</a>, <a href="#heading=h.x5dkr4gsyug9">5.2.4</a>, <a href="#heading=h.8wbm665t373a">5.2.5</a> verify the time for link up</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Ideally, no link should take more than <em>20</em> seconds to come up. *</li>
</ul>
* final threshold value to be determined<br>
<strong>Note</strong>: The test timeouts were increased to 60 secs based on  b/197792239#comment8 </td>
    </tr>
  </tbody>
</table>

### 5.2.7. Reboot the switch

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Reboot the switch and verify all modules are recognized correctly and links are back up. This test will be repeated for <em>X*</em> number of consecutive reboots. <br>
<em>*X is 100 as per the Optics team's suggestions</em></li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Verify the LED state</li>
</ul>
<ul>
<li>The optics modules are always expected to come back up.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.8. Power cycle

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>For Taygeta/Brixia, power cycle the switch with the <em>poweroff</em> command and verify all modules are recognized correctly and links are back up. This test will be repeated for <em>X*</em> number of consecutive reboots. <br>
<em>*X is 100 as per the Optics team's suggestions</em></li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Verify the LED state</li>
</ul>
<ul>
<li>The optics modules are always expected to come back up.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.9. BERT/Link Qualification

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Run BERT, Link Qualification between the links</li>
</ul>
<ul>
<li>This will be done with chemia's <em>linkqualification</em> option if the links are modeled.</li>
</ul>
<ul>
<li>For unmodeled links, BERT will be initiated from the switch shell UI.</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>The optics modules are always expected to come back up.</li>
</ul>
<ul>
<li>BERT should pass.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.10. Push a configuration change (changes in speed, modulation, channelization and names) and verify if reboot is required (reboot required check for SC only).

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Push the following configuration changes:</li>
</ul>
<ul>
<li>Speed changes without channelization changes (port speed change)</li>
</ol>
</li>
</ul>
<ul>
<li>Channelization changes (regardless of speed) (flexport operation/DPB)<br>
NOTE: Modulation changes are interdependent on speed/channels.</li>
</ol>
</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Config pushes should succeed without needing a reboot</li>
</ul>
<ul>
<li>Only port_id changes (i.e. cc, cp, qe, xe, etc.) requires a reboot. (Sandcastle only, GPINs ports are eth*)</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.11. Upgrade the switch.

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Upgrade the switch software and verify all modules are recognized correctly and links are back up.</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>The optics modules are always expected to come back up.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.2.12. Install the switch.

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Install the switch and verify all modules are recognized correctly and links are back up.</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>The optics modules are always expected to come back up.</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

## 5.3. Optics firmware upgrade

### 5.3.1. Force upgrade the switch optic

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Force the switch into thinking optics upgrade is needed</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>There is a difference in the latest firmware version for the optic part number and the current running firmware version.</li>
</ul>
<ul>
<li>The optics modules are always expected to come back up with the upgraded firmware</li>
</ul>
</td>
    </tr>
  </tbody>
</table>

### 5.3.2. Upgrade the optics firmware using Chemia

<table>
  <thead>
    <tr>
      <th><strong>Priority</strong></th>
      <th>P0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Procedure</strong></td>
      <td><ul>
<li>Perform upgrade of the switch software</li>
</ul>
<ul>
<li>There is a difference in the latest firmware version for the optic part number and the current running firmware version.</li>
</ul>
<ul>
<li>Upgrade the firmware and expect it to succeed over (CMAL OR over SSH and eventually <a href="https://docs.google.com/document/d/1lx0F_vTolNt-UzJPe7rSRjMikxMjNOmS9xtze9EW724/edit#heading=h.mvmwdj6om6wl">gNOI</a>)</li>
</ul>
</td>
    </tr>
    <tr>
      <td><strong>Expected results</strong></td>
      <td><ul>
<li>Upgrade should succeed</li>
</ul>
<ul>
<li>The optics modules are always expected to come back up with the upgraded firmware</li>
</ul>
</td>
    </tr>
  </tbody>
</table>
