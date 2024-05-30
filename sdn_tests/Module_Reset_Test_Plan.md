# Test Cases

Test gNOI module reset.

## Module Reset on a single transceiver

Run System.Reboot RPC with subcomponents set to the path of one transceiver and method set to COLD. Verify that all interfaces not on the transceiver remain up. Wait, and verify that all interfaces are up.

## Successful Module Reset on all transceivers

Run System.Reboot RPC with subcomponents set to the paths of all non-empty transceivers (/components/component[name=Ethernet1], etc.), and method set to COLD. Wait for some time, and verify that interfaces are up.

## Module Reset fails for invalid transceiver name

Run System.Reboot RPC with subcomponents set to a transceiver with an invalid name (EthernetX where X is greater than max transceiver number)  and method set to COLD. Verify that RPC fails.
