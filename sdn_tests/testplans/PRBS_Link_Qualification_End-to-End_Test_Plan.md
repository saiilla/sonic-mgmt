# Objective

This document presents the test plan for the link qualification in the GPINs software stacks on Brixia. This test plan covers PRBS BERT link-qualification on hardware based testbeds.

# Overview

This test plan aims to provide comprehensive testing of PRBS BERT link qualification feature in GPINs. In GPINs, linkqual related gNOI APIs are defined that will be used to invoke link qualification. API design is [here](https://docs.google.com/document/d/15PZXPZex72xQmtWydmOidz_u2egoCyQK_3dwsl5fCBo/edit#). Some sequence diagrams for operations are [here](https://docs.google.com/document/d/1isZAM_D1o1X44zRKwngW42mhXy7SmmhYJKHFgKrf-WA/edit).

## Involved Entities and Ownership

Following are main involved entities:

-   **Test infrastructure:** It invokes the link qualification requests using the gNOI APIs and verifies the link qualification results.
-   **gNOI front end**: Handles the link qualification requests coming from test infrastructure, validates the requests and requests link qualification back-end to run link qualification.
-   **Link qualification back end**: Maintains and runs the link qualification.

## Initial Setup

The testbed is initially setup as follows:

-   SUT is installed with the GPINs stack image under test using push-imager.
-   Control switch is installed using a push-imager. The software stack (GPINs vs Sandcastle) running on the control switch is decided at run time depending on the suite of tests.

## Blackbox Validations

Following are the key validations that test should pass:

-   External connections to the switches are intact - P4, GNMI, Ping, SSH, etc.
-   All containers/processes are up.
-   No critical errors (unless induced and expected)
-   All switch ports are operational
-   No errors in the log (unless induced and expected)

# Test Strategy

We cover both the positive and negative test cases. Positive cases test that the PRBS BERT linkqual works end-to-end. Negative cases will focus mostly on the API contract (i.e., invalid requests) and some induced failures to cover unexpected failures that may happen in actual deployment.

## PRBS BERT

### Goal

This test checks the health of port connections including port modules, and optics/cables by sending PseudoRandom Binary Sequence (PRBS) patterns. The steps can be summarized as follows:

-   Before running BERT, check that all the ports are UP.
-   Start BERT on the target ports.
-   Poll for the test to stop.
-   Get BERT results for the target ports and check if the test PASSED.
-   Check that all the ports are UP after BERT has run.

### Tests

#### Test 1: StartBert fails due to invalid request parameters

**Case 1**: This sub-test expects to fail the StartBert request when the request is sent with an invalid port.\
**Case 2**: This sub-test expects to fail the StartBert request when the test duration is too long in the request.\
**Case 3**: This subtest expects to fail the StartBert request when the test duration is too short in the request.\
**Case 4**: This subtest expects to fail the StartBert request when requested with invalid PRBS.

Steps in test:

1.  Test Infrastructure gets a set of up ports on SUT.
1.  Test Infrastructure issues the StartBert RPC an invalid port name on SUT and receives failure in RPC response.
1.  Test Infrastructure issues the StartBert RPC with test duration 0 secs on SUT and receives failure in RPC response.
1.  Test Infrastructure issues the StartBert RPC with test duration more than maximum allowed duration on SUT and receives failure in RPC response.
1.  Test Infrastructure issues the StartBert RPC with invalid PRBS order on SUT and receives failure in RPC response.
1.  Test Infrastructure verifies ports are UP after the test.

Sample StartBERT request proto:

```
R"PROTO(
   bert_operation_id: "OpId-1"
   per_port_requests {
       interface {
            origin: "openconfig"
            elem { name: "interfaces" }
            elem {
                  name: "interface"
                  key { key: "name" value: "InvalidPort" }
            }
       }
       prbs_polynomial: PRBS_POLYNOMIAL_PRBS23
       test_duration_in_secs: 0
   }
)PROTO";
```

####

#### Test 2: StopBert fails if no BERT is running on the port(s)
This test expects to fail the StopBert request when no BERT is running on the port.

Steps in test:

1.  Test Infrastructure gets one up port on SUT and issues the StopBert RPC.
1.  Test Infrastructure receives failure in RPC response.
1.  Test infrastructure verifies ports are UP after test.

Sample StopBERTRequest proto:
```
R"PROTO(
  bert_operation_id: "OpId-1"
  per_port_requests {
    interface {
      origin: "openconfig"
      elem { name: "interfaces" }
      elem {
        name: "interface"
        key { key: "name" value: "Ethernet1" }
      }
    }
  }
)PROTO";
```

#### Test 3: GetBertResult fails to get BERT result due to invalid op id

This test expects to fail the GetBertResult request issued with an operation id that has not been used.

Steps in test:

1.  Test Infrastructure issue GetBertStatus RPC with an unused operation id on a port.
1.  Test Infrastructure receives failure in RPC response.
1.  Test infrastructure verifies ports are UP after the test.

Sample GetBERTResultRequest proto:

```
R"PROTO(
  bert_operation_id: "OpId-1"
  per_port_requests {
    interface {
      origin: "openconfig"
      elem { name: "interfaces" }
      elem {
        name: "interface"
        key { key: "name" value: "Ethernet1" }
      }
    }
  }
  per_port_requests {
    interface {
      origin: "openconfig"
      elem { name: "interfaces" }
      elem {
        name: "interface"
        key { key: "name" value: "Ethernet2" }
      }
    }
  }
)PROTO";
```

#### Test 4: StartBert fails on a port If peer port is not running linkqual

This test expects to fail the BERT if the StartBert request was issued on only one side of the link - one side of the link was issued a BERT request but the other side of the link was not.

Steps in test:

1.  Test Infrastructure gets an up port and issues the StartBert RPC only on the SUT switch.
1.  Test Infrastructure polls at every 30 seconds for a maximum of 10 mins (delay due to peer lock sync check and post BERT link recovery operations) for BERT to complete.
1.  Test Infrastructure requests the BERT result and it should get BERT failure on port due to LOCK LOSS failure.
1.  Test infrastructure verifies ports are UP after the test.

Sample StartBERT request proto:

```
R"PROTO(
   bert_operation_id: "OpId-1"
   per_port_requests {
       interface {
            origin: "openconfig"
            elem { name: "interfaces" }
            elem {
                  name: "interface"
                  key { key: "name" value: "Ethernet0" }
            }
       }
       prbs_polynomial: PRBS_POLYNOMIAL_PRBS23
       test_duration_in_secs: 1800
   }
)PROTO";
```

#### Test 5: BERT success Tests

Following 3 tests can run separately but since linkqual tests are time consuming tests, we will merge the next 3 tests into a single test.

##### Test 5.1: Run a successful BERT on a set of ports

This test expects to execute StartBert requests successfully on a set of ports.

Steps in test:

1.  Test Infrastructure checks and gets a list of ports that are up on paired switches. Test Infrastructure selects a set of UP ports (maximum 2 links) and issues the StartBert RPC on both the switches.
1.  Test Infrastructure should receive SUCCESS response for StartBert RPC.
1.  Test Infrastructure waits for BERT sync duration and after that checks if BERT is running on the ports.
1.  Test Infrastructure polls at every 30 seconds for a maximum of (test duration + post BERT link recovery time) for BERT to complete.
1.  Test Infrastructure queries the result and verifies the result.
    1.  If result is good, marks test PASSED, else
    1.  Marks test as FAILURE.
1.  Test infrastructure verifies ports are UP after the completion of the test.

Sample StartBERT request proto:

```
R"PROTO(
   bert_operation_id: "OpId-1"
   per_port_requests {
       interface {
            origin: "openconfig"
            elem { name: "interfaces" }
            elem {
                  name: "interface"
                  key { key: "name" value: "Ethernet0" }
            }
       }
       prbs_polynomial: PRBS_POLYNOMIAL_PRBS23
       test_duration_in_secs: 1800
   }
   per_port_requests {
       interface {
            origin: "openconfig"
            elem { name: "interfaces" }
            elem {
                  name: "interface"
                  key { key: "name" value: "Ethernet1" }
            }
       }
       prbs_polynomial: PRBS_POLYNOMIAL_PRBS23
       test_duration_in_secs: 1800
   }

)PROTO";
```
##### Test 5.2: StartBert fails if requested ports are already running BERT

This test expects to execute a StartBert request successfully on a set of ports and then expected to fail if another BERT is requested on those ports while BERT is already running.

Steps in test:

1.  Test Infrastructure checks and gets a list of ports that are up on paired switches. Test Infrastructure selects a set of UP ports (maximum 2 links) and issues the StartBert RPC on both the switches.
1.  Test Infrastructure should receive SUCCESS response for StartBert RPC.
1.  Test Infrastructure waits for BERT sync duration and after that checks if BERT is running on the ports.
1.  Test Infrastructure issues another StartBert on the same ports with different operation id .
1.  Test Infrastructure queries the result for just requested StrtBert and finds that request failed due to BERT_STATUS_PORT_ALREADY_IN_BERT.
1.  Test Infrastructure polls at every 30 seconds for a maximum of (test duration + post BERT link recovery time) for the first BERT request to complete.
1.  Test infrastructure verifies ports are UP after the completion of the test.

Sample StartBERT request proto: Same as above.
##### Test 5.3: StartBert fails If requested operation id is already in use

This test expects to fail the StartBert request when the request is sent with an operation id that is already in use by a previous StartBert request.

Steps in test:

1.  Test Infrastructure checks and gets a list of ports that are up on paired switches. Test Infrastructure selects a set of UP ports (maximum 2 links) and issues the StartBert RPC on both the switches.
1.  Test Infrastructure should receive SUCCESS response for StartBert RPC.
1.  Test Infrastructure waits for the above requested BERT to successfully complete.
1.  Test Infrastructure issues another StartBert request for some port with the same operation id and receives the failure in the RPC response.
1.  Test infrastructure verifies ports are UP after the completion of the test.

Sample StartBERT request proto: Same as above.

#### Test 6: Scale test - StartBert fails on a set of ports when running on all the ports on switch

This test requests StartBert requests on all the up ports and during BERT run on the ports, a set of ports fail to complete the BERT and the rest successfully run the BERT.

Steps in test:

1.  Test Infrastructure checks all the ports are up and issues the StartBert RPC on all the ports on both the switches.
1.  Test Infrastructure should receive SUCCESS response for StartBert RPC.
1.  Test Infrastructure waits for BERT sync duration.
1.  Test infrastructure periodically polls to get the BERT status and once BERT status is RUNNING, Test Infrastructure will force admin state down on a set of ports to force the BERT failure (For example: if we are forcing admin down on 10 ports, then 5 ports will be on SUT running GPINs and 5 ports on control switch running Sandcastle).
1.  Test Infrastructure polls at every 30 seconds for a maximum of (test duration + post BERT link recovery time) for BERT to complete.
1.  Test Infrastructure should request GetBertResult on all the ports and it should find a set of ports failed BERT where port admin state down was forced and the rest of the ports succeeded.
1.  Test infrastructure forces the admin state up on ports there were forced down in step 4.
1.  Test infrastructure verifies ports are UP after the completion of the test.

Sample StartBERT request proto:

```
R"PROTO(
   bert_operation_id: "OpId-1"
   per_port_requests {
       interface {
            origin: "openconfig"
            elem { name: "interfaces" }
            elem {
                  name: "interface"
                  key { key: "name" value: "Ethernet0" }
            }
       }
       prbs_polynomial: PRBS_POLYNOMIAL_PRBS23
       test_duration_in_secs: 1800
   }
   per_port_requests {
       interface {
            origin: "openconfig"
            elem { name: "interfaces" }
            elem {
                  name: "interface"
                  key { key: "name" value: "Ethernet1" }
            }
       }
       prbs_polynomial: PRBS_POLYNOMIAL_PRBS23
       test_duration_in_secs: 1800
   }
…..

)PROTO";
```

#### Test 7: StopBert succeeds if BERT is running on the port(s)

This test expects the StopBert request to succeed when BERT is running on the port.

Steps in test:

1.  Test Infrastructure gets one up port and issues the StartBert RPC on SUT and control switch.
1.  Test Infrastructure receives SUCCESS in StartBert RPC response.
1.  Test Infrastructure waits for BERT sync duration and requests GetBertResult to make sure the BERT process is started on the control switch.
1.  Test Infrastructure issues the StopBert RPC on SUT for the port running the BERT.
1.  Test Infrastructure receives SUCCESS in StopBert RPC response.
1.  Test Infrastructure polls every 30 seconds for post BERT link recovery time to check if BERT stopped on SUT and control switch.
1.  Test Infrastructure requests GetBertResult and verifies the BERT process was stopped on the SUT port but failed on the control switch port due to peer port lock loss.
1.  Test infrastructure verifies ports are UP after the test.

Sample StopBERTRequest proto:

```
R"PROTO(
  bert_operation_id: "OpId-1"
  per_port_requests {
    interface {
      origin: "openconfig"
      elem { name: "interfaces" }
      elem {
        name: "interface"
        key { key: "name" value: "Ethernet1" }
      }
    }
  }
)PROTO";
```
