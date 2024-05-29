This document describes the Inband end-to-end testing plan. It consists of three parts:

1.  Inband Manager software loopback interface gNMI paths E2E testing
2.  Inband Manager E2E testing
3.  Inband Manager feature testing

# Part 1 – Inband Software Loopback Interface gNMI Path E2E Testing

## Overview

This document describes the test plan for 24 gNMI paths related to inband loopback interface with the heading `sw_loopback`.

A total of 12 test cases are organized in the following 4 categories:

1.  Generic. This includes
    1.  get interface "Name";
    1.  get "Type";
    1.  get "Oper-Status";
    1.  get "Mac-Address".

1.  IPv4 Suite. This includes
    1.  set/get "IPv4 address" and "IPv4 prefix length";
    1.  set/get invalid "IPv4 address" and/or "IPv4 prefix length".

1.  IPv6 Suite. This includes
    1.  set/get "IPv6 address" and "IPv prefix length";
    1.  set/get invalid "IPv6 address" and "IPv prefix length".

1.  Interface counters. This includes
    1.  get incoming traffic stats counters;
    1.  get outgoing traffic stats counters;
  
### State Paths vs. Config Paths

In the following, we have two kinds of gNMI paths. State paths are read-only paths.  They reflect the state of the system.  Most of the gNMI state path tests verify the result from the gNMI get (read) path. Config paths can be read or written.  Writing a config path indicates a desired system change.  There is a corresponding state path that is updated once the change takes effect.  Most of the gNMI config path tests verify both the gNMI get (read) path is operating and that the gNMI set (write) path updates the system state.

## Generic Status Testings

### Test Cases Summary

1.  "Get MAC-address"

> Read MAC from sw_loopback, expect to see the output in MAC format, should match the MAC of the software loopback interface on the switch.

1.  "Get Oper-Status"

> Read Oper-Status from sw_loopback, expect to see the status to be UP.

1.  "Get Name"

> Get Name on sw_loopback, expect to see output matching "Loopback0".

1.  "Get Type"

> Get Type, expect to see output to match the type of software loopback.

### Paths to be Tested

<table>
  <thead>
    <tr>
      <th><strong>OpenConfig Path</strong></th>
      <th><strong>Description</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/ethernet/<br>
state/mac-address</td>
      <td>Get MAC Address of Loopback0</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/<br>
state/oper-status</td>
      <td>Get oper-status of Loopback0</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/<br>
state/name</td>
      <td>Get name/alias of Loopback0</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/<br>
state/type</td>
      <td>Get type of Loopback0</td>
    </tr>
  </tbody>
</table>

### Test Cases

#### Get MAC-address

> Read MAC from sw_loopback, expect to see the output in MAC format, and should match MAC of the software loopback interface on the switch.

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client Get the gNMI path /interfaces/interface[name=<sw_loopback>]/ethernet/state/mac-address.</li>
</ul>
<ul>
<li>SSH to the switch and issue "ifconfig Loopback0" to find out the MAC address used on Loopback0. Alternatively, use <code>redis-cli</code> to fetch initial value of  <code>APPL_STATE_DB</code> <code>LOOPBACK_INTERFACE|Loopback0</code> and find the <code>mac-address.</code></li>
</ul>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect initial values of both paths to be equivalent.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

#### Get Oper-Status

> Read Oper-Status from sw_loopback, expect to see the status UP in the return.

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client to Get the gNMI path /interfaces/interface[name=<sw_loopback>]/state/oper-status.</li>
</ul>
<ul>
<li>SSH to the switch and issue "ifconfig Loopback0" to find out the status of Loopback0. Alternatively, use <code>redis-cli</code> to fetch initial value of  <code>APPL_STATE_DB</code> <code>LOOPBACK_INTERFACE|Loopback0</code> <code>oper-status.</code></li>
</ul>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect initial values of both paths to be equivalent</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

#### Get Name

> Get Name on sw_loopback,expect to see the output to match "Loopback0".

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client to Get the gNMI path /interfaces/interface[name=<sw_loopback>]/state/name.</li>
</ul>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the name to be Loopback0.</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

#### Get Type

> Get Type, expect to see output with match "iana-if-type:softwareLoopback".

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client to Get the gNMI path /interfaces/interface[name=<sw_loopback>]/state/type.</li>
</ul>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the output to be type "iana-if-type:softwareLoopback".</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

## IPv4 Testings

### Summary of Test Cases

1.  "Get/Set IPv4 Address and Prefix length"

> Set IPv4 address and IPv4 prefix length on sw_loopback, then read back IPv4 and netmask, expect to see IP address and netmask matching the values set.

1.  "Get/Set invalid IPv4 Address and Prefix length"

> Set invalid IPv4 address and IPv4 prefix length on sw_loopback, then read back IPv4 and netmask, expect to see IP address and netmask unchanged (matching the previous values).


### Paths to be Tested

<table>
  <thead>
    <tr>
      <th><strong>OpenConfig Path</strong></th>
      <th><strong>Description</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/state/ip</td>
      <td>Get IPv4 address of software loopback interface</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/state/prefix-length</td>
      <td>Get IPv4 prefix length of software loopback interface</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/config/ip</td>
      <td>Set IPv4 address of software loopback interface</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/config/prefix-length</td>
      <td>Set IPv4 prefix length of software loopback interface</td>
    </tr>
  </tbody>
</table>

### Test Cases

#### Get/Set IPv4 Address and Prefix length

> Set IPv4 address and IPv4 prefix length on sw_loopback, then read back IPv4 and netmask, expect to see IP address and netmask matching the values set.

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client to use the following gNMI paths to<strong> set </strong>the IP address / prefix length:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/config/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/state/prefix-length,<br>
<br>
<strong>Test:</strong><br>
<ul>
<li>Use gNMI client to <strong>get </strong>the gNMI paths:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/config/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/config/prefix-length, <br>
And providing valid and reachable IPv4 address and prefix length.<br>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the IPv4 address and prefix length are matching the values set. </li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

#### Set invalid IPv4 Address and Prefix length

> Set invalid IPv4 address and IPv4 prefix length on sw_loopback, then read back IPv4 and netmask, expect to see IP address and netmask unchanged (matching the previous values);

<table>
  <thead>
    <tr>
      <th><br>
<strong>Test:</strong><br>
<ul>
<li>Use gNMI client to set the gNMI paths:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/config/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/config/prefix-length, <br>
And providing valid and <strong>invalid</strong> IPv4 address and prefix length.<br>
<br>
<ul>
<li>Use gNMI client to get the gNMI paths:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/state/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv4/addresses/address[ip=<address>]/state/prefix-length.<br>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the IPv4 address and prefix length are matching the values previously set. </li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

## IPv6 Testings

### Summary of Test Cases

1.  "Get/Set IPv6 Address and Prefix length"

> Set IPv6 address and IPv6 prefix length on sw_loopback, then read back IPv6 and netmask, expect to see IP address and netmask matching the values set;

1.  "Get/Set invalid IPv6 Address and Prefix length"

> Set invalid IPv6 address and IPv6 prefix length on sw_loopback, then read back IPv6 and netmask, expect to see IP address and netmask unchanged (matching the previous values);

### Paths to be Tested

<table>
  <thead>
    <tr>
      <th><strong>OpenConfig Path</strong></th>
      <th><strong>Description</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/state/ip</td>
      <td>Get IPv6 address of software loopback interface</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/state/prefix-length</td>
      <td>Get IPv6 prefix length of software loopback interface</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/config/ip</td>
      <td>Set IPv6 address of software loopback interface</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/config/prefix-length</td>
      <td>Set IPv6 prefix length of software loopback interface</td>
    </tr>
  </tbody>
</table>

### Test Cases

#### Get/Set IPv6 Address and Prefix length

> Set IPv6 address and IPv6 prefix length on sw_loopback, then read back IPv6 and netmask, expect to see IP address and netmask matching the values set.

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client to Set the gNMI paths:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/config/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/config/prefix-length, <br>
And providing valid and reachable IPv6 address and prefix length.<br>
<br>
<ul>
<li>Use gNMI client to Get the gNMI paths:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/state/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/state/prefix-length.<br>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the IPv6 address and prefix length are matching the values set. </li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

#### Set invalid IPv6 Address and Prefix length

> Set invalid IPv6 address and IPv6 prefix length on sw_loopback, then read back IPv6 and netmask, expect to see IP address and netmask unchanged (matching the previous values);

<table>
  <thead>
    <tr>
      <th><br>
<strong>Test:</strong><br>
<ul>
<li>Use gNMI client to Set the gNMI paths:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/config/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/config/prefix-length, <br>
And providing valid and <strong>invalid</strong> IPv6 address and prefix length.<br>
<br>
<ul>
<li>Use gNMI client to Get the gNMI paths:</li>
</ul>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/state/ip,<br>
/interfaces/interface[name=<sw_loopback>]/subinterfaces/subinterface[index=<index>]/ipv6/addresses/address[ip=<address>]/state/prefix-length.<br>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the IPv6 address and prefix length are matching the values previously set. </li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

## Statistics Counters Testings

### Summary of Test Cases

1.  Read in-octets, out-octets, in-pkts, out-pkts, expect to see these counters increasing over time

### Paths to be Tested

<table>
  <thead>
    <tr>
      <th><strong>OpenConfig Path</strong></th>
      <th><strong>Description</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/<br>
state/counters/in-octets</td>
      <td>The counter for the number of octets received on Loopback0</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/<br>
state/counters/in-pkts</td>
      <td>The counter for the number of packets received on Loopback0</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/<br>
state/counters/out-octets</td>
      <td>The counter for the number of octets transmitted on Loopback0</td>
    </tr>
    <tr>
      <td>/interfaces/interface[name=<sw_loopback>]/<br>
state/counters/out-pkts</td>
      <td>The counter for the number of packets transmitted on Loopback0</td>
    </tr>
  </tbody>
</table>

### Test Cases

#### Get incoming traffic counters
Read in-pkts, in-octets, expect to see increasing over time

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client to Get the gNMI paths (tests are done for each individual path): </li>
</ul>
/interfaces/interface[name=<sw_loopback>]/state/counters/in-pkts; <br>
/interfaces/interface[name=<sw_loopback>]/state/counters/in-octets.<br>
<ul>
<li>Repeat the above for several times</li>
</ul>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the values are increasing over time</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

#### Get outgoing traffic counters

Read out-pkts, out-octets, expect to see increasing over time

<table>
  <thead>
    <tr>
      <th><strong>Test:</strong><br>
<ul>
<li>Use gNMI client to get the gNMI paths (tests are done for each individual path): </li>
</ul>
/interfaces/interface[name=<sw_loopback>]/state/counters/out-pkts;<br>
/interfaces/interface[name=<sw_loopback>]/state/counters/out-octets;<br>
<ul>
<li>Repeat the above for several times</li>
</ul>
<br>
<strong>Verify:</strong><br>
<ul>
<li>Expect the values are increasing over time</li>
</ul>
</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>

While packets could have errors when received on front-panel interfaces, those packets could be rejected when forwarded to the loopback interface. So the error counters on the Loopback0 interface are expected to be all 0.

#### Helper Switches

In the real world, the switches are set up layer by layer. That is, in the above example, S3 is the first switch to set up the inband manager, then S21 and S22, and finally S1. By the time S21 is to be set up, S3 inband manager should be already up and running, and S3 is the "help switch" for S21. The controller should set up the S3 in such a way that packets between S21 and controller can be routed on S3. By the time it's time to set up S1, switches above S1 are "helpers" and should be able to route the packets between S1 and the controller.

## Test Suite #1 – Inband Manager Basic Operation

### Test Case 1.1: Return Path Establishment
