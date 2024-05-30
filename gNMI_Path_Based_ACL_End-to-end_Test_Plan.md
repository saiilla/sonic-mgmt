# Overview

This document describes  the end-to-end testing for the gNMI path based ACL feature.

# Objective

The end-to-end testing will cover the following:

-   End-to-end execution of the gNSI rotation API for path base ACL.
-   Various error scenarios in executing the gNSI rotation API.
-   Authorization verification of the gNMI path based ACL using different gNMI calls.

The end-to-end testing will not cover:

-   Full coverage of the authorization logic in the implementation. Those will be covered in the unix test of the implementation.

# Background

## gNMI Path Based ACL

The gNMI path based ACL is a feature to provide authorization based on requested gNMI paths. The configuration can specify authorization policies separately for read requests and write requests. A gNSI API will also be defined for configuration rotation. In this test plan, the tests will use gNSI API to update the configuration to a test version, and make several gNMI calls on the test version to verify the authorization. The test will also exercise different error scenarios during the gNSI API call.

## Test Configuration File

A single test configuration file will be used for all the testing. The test configuration file will define a few policies that cover several gNMI paths in read and write access with permit and deny permissions. Before the test begins, the switch should have a default configuration that is pushed by PlanetClub. At the end of each test, the test should call PlanetClub to re-push the configuration as the test might have rotated the configuration with the test version.

To verify the configuration is successfully rotated and the authorization feature is functioning properly, the test configuration should avoid conflict with the default configuration. The default configuration will most likely give all access permissions to the test user. And hence the test configuration should include some deny policies on the test user for verification purposes.

## Testbed User

Currently the standalone testing environment has two users:

-   "sysinfra-gpins-test-jobs"\
This is the user name on standalone testing.
-   "netinfra-biglab-test-jobs-dedicated"\
This is the user name when the test runs in BigLab Flex.

The test configuration file will have both users configured for the permitted paths. Changing the user in a test is difficult, and hence the test will use a fixed user to test various paths with different ACLs to verify the feature.

## Verification

To verify that the test configuration is applied, the test will need to send a request that is supposed to be rejected by the test configuration. The opposite will apply in verifying the default configuration is applied.

# Testbed

All testbeds should be able to run the end-to-end testing for gath based ACL, including the virtual testbed, vGPINs.

# Test Cases

## gNSI Rotation Tests

### Close gNSI Stream Without Rotation

Steps:

-   Open a gNSI stream for path based ACL rotation.
-   Close the stream without pushing the test configuration.

Verify:

-   Error status is returned from the switch.
-   The default configuration is applied.

### Invalid Configuration

Steps:

-   Open a gNSI stream for path based ACL rotation.
-   Send an invalid configuration. (The invalid configuration will be used in this test only.)

Verify:

-   Error status is returned from the switch.
-   The default configuration is applied.

### Valid Configuration

Steps:

-   Open a gNSI stream for path based ACL rotation.
-   Send the test configuration.

Verify:

-   Rotation RPC completed successfully.
-   The test configuration is applied.

## gNMI Authorization Tests

### Get Test

Steps:

-   Update to the test configuration.
-   Send a gNMI Get request with some paths that are permitted but some paths are denied.

Verify:

-   The Get RPC is successful.
-   The Get response only includes the permitted paths.

### Subscribe Test

Steps:

-   Update to the test configuration.
-   Send a gNMI Subscribe request with some paths that are permitted but some paths are denied.

Verify:

-   The Subscribe RPC is successful.
-   The Subscribe stream response only includes the permitted paths.

### Set Delete Test

Steps:

-   Update to the test configuration.
-   Send a gNMI Set request which includes:
    -   A denied path in the delete.
    -   A permitted path in the update.

Verify:

-   The Set RPC failed with PERMISSION_DENIED.

### Set Replace Test

Steps:

-   Update to the test configuration.
-   Send a gNMI Set request which includes:
    -   A denied path in the replace.
    -   A permitted path in the update.

Verify:

-   The Set RPC failed with PERMISSION_DENIED.

### Set Update Test

Steps:

-   Update to the test configuration.
-   Send a gNMI Set request which includes:
    -   A denied path in the update.
    -   A permitted path in the delete.

Verify:

-   The Set RPC failed with PERMISSION_DENIED.
