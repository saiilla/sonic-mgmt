# Overview

This document describes the end to end test plan for exercising the following 5 classes of Parity Errors, and verifying the GNMI paths.

1) single bit recoverable\
2) double bit, recoverable \
3) single bit unrecoverable + Critical state reporting.\
4) double bit unrecoverable + Critical state reporting

A total of 4 tests will be written to cover these parity error paths, as summarized below.

As specified in the [component test plan](http://go/gpins-platform-gnmi-path-component-test-plan), end to end tests may only access the device using the gNMI interface. However, we have a caveat. To create the parity errors we are using BCM shell commands.

# TH3 and TH4G Parity Error Classifications

The document above classifies all the memories, and the error/detection and correction supported by the vendor, in the following broad categories.

<table>
  <thead>
    <tr>
      <th><strong>ECC/PAR</strong></th>
      <th><strong>TH3/TH4G</strong></th>
      <th><strong>SW ERR Response</strong></th>
      <th><strong>Class of Parity Error</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>*</td>
      <td>TH3/TH4G</td>
      <td>SER_NONE</td>
      <td>Unrecoverable Error</td>
    </tr>
    <tr>
      <td>PAR</td>
      <td>TH3</td>
      <td>SER_ECC_PARITY</td>
      <td><br>
(possible packet loss)</td>
    </tr>
    <tr>
      <td>ECC</td>
      <td>TH3/TH4G</td>
      <td>SER_ENTRY_CLEAR<br>
SER_WRITE_CACHE_RESTORE<br>
SER_ECC_CORRECTABLE<br>
SER_SPECIAL</td>
      <td>Recoverable Error</td>
    </tr>
  </tbody>
</table>

We have an ECC Optimization One bit ECC error feature, whereby any recoverable ECC Error is not reported to the GNMI error counters, so are taking that into account as a test case Test 2a.

Note: We do know that based on the above classifications, if there is a 1 bit error, the memory that is correctable, will be able to correct that error. We do not have information if all the recoverable memories can do 2 bit recovery.

If the SDK is not able to correct the error, even if it is capable of correcting (maybe the backup also had an error, or some other failure), the SDK does notify that the error was not corrected. Our E2E testing is not testing for such double errors and fail safe cases.

Maybe a more thorough Functional Testing of BCM SDK for such errors in each of the memories listed would be really good to have. However, such verifications could be covered in the component testing framework.

## Test Cases

### Test 1: Get of Parity Errors

**Paths Verified:**

<table>
  <thead>
    <tr>
      <th><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/corrected-parity-errors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/total-parity-errors</td>
    </tr>
  </tbody>
</table>

1.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit.
1.  Expect the following results:
    1.  `corrected-parity-errors `and` total-parity-errors `have a non-negative integral value.

### Test 2a: Single Bit ECC Error - Recoverable

**Paths Verified:**

<table>
  <thead>
    <tr>
      <th><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/corrected-parity-errors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/total-parity-errors</td>
    </tr>
  </tbody>
</table>

1.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit.
2.  Use BCM Shell Command to trigger a Single Bit Parity Error
   "/usr/tools/bin/bcmcmd -c 'ser inject pt=L3_IIFm Single=1'
3.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit
4.  Expect the following result
    1.  `corrected-parity-errors `and` total-parity-errors `from step 3 are unchanged from step 1.
    2.  This is to test the optimization where memories with ECC bits do not increment the counters on 1 bit error.
    3.  Reference Document: [One bit ECC error](https://docs.google.com/document/d/1jp1ZP4fEc6iSd_flGiWv450Qh743_zXT0N3_axBsqj8/edit#heading=h.x9snb54sjlu9)

### Test 2b: Single Bit Parity Error - Recoverable

**Paths Verified:**

<table>
  <thead>
    <tr>
      <th><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/corrected-parity-errors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/total-parity-errors</td>
    </tr>
  </tbody>
</table>

1.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit.
2.  BCM Shell Command to trigger a Single Bit Parity Error
3.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit
4.  Expect the following result
    1.  `corrected-parity-errors `and` total-parity-errors `from step 3 should increment by 1.
    2.  This is to test the optimization where memories with Parity bits do increment the counters on 1 bit error.

### Test 3: Double Bit ECC Error - Recoverable

**Paths Verified:**

<table>
  <thead>
    <tr>
      <th><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/corrected-parity-errors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/total-parity-errors</td>
    </tr>
  </tbody>
</table>

1.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit.
2.  BCM Shell Command to trigger a Double Bit Parity Error
3.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit
4.  Expect the following result
    1.  `corrected-parity-errors `and` total-parity-errors `should increment by one

### Test 4: Single Bit Unrecoverable Error

**Paths Verified:**

<table>
  <thead>
    <tr>
      <th><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/corrected-parity-errors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/total-parity-errors</td>
    </tr>
  </tbody>
</table>

1.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit.
2.  BCM Shell Command to trigger a Double Bit Parity Error
3.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit
4.  Expect the following result
    1.  `corrected-parity-errors `is the same as step1
    2.   `total-parity-errors `should increment by one

### Test 5: Double Bit Unrecoverable Error

**Paths Verified:**

<table>
  <thead>
    <tr>
      <th><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/corrected-parity-errors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><br>
/components/component[name=<integrated-circuit>]/integrated-circuit/memory/state/total-parity-errors</td>
    </tr>
  </tbody>
</table>

1.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit.
2.  BCM Shell Command to trigger a Double Bit Parity Error
3.  Use gNMI get operation to fetch `corrected-parity-errors `and` total-parity-errors` for the integrated circuit
4.  Expect the following result
    1.  `corrected-parity-errors `is the same as step1
    2.   `total-parity-errors `should be more than what's seen in step 1. (since the switch is in an unrecoverable state, the errors keep increasing, so we should not be checking for an absolute increase)
5.  Restart Gpins Stack to bring the switch back to the original state
