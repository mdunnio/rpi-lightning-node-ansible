# README

## Preamble

This playbook assumes the following configurations:

1) The rpis has been initialized with raspbian stretch lite.
2) An external hdd is being used to store blockchain data. It will require some extra
bit of configuration to change this setup. All bitcoind/lightningd data is stored on the
external hdd and not the SD card. This is very, very intentional.
3) Two users will be running on the rpi: pi and bitcoin
4) systemd is used to manage bitcoind/lightningd processes
5) ssh keys are used instead of password authentication.

## Running

`cd ansible`
`ansible-playbook main.yaml -i inventory/`

## Required Configurations

This repo does not contain an inventory directory or inventory files, as they are bespoke
to each user. However, the following sample inventory file outlines all the required
host vars for all the roles.

### Inventory

rpi-lightning-node-ansible/ansible/inventory/lightning.yaml:

```
all:
  hosts:
    example-lightning-node:
      ansible_host: 192.168.0.10
      ansible_user: pi
```

### Host Vars

rpi-lightning-node-ansible/ansible/inventory/host_vars/example-lightning-node:

```
# Need to be configured:

mount_src: "/dev/sda1" # Where the external drive is mounted

## bitcoind
bitcoind_rpc_user: "testuser"
bitcoind_rpc_password: "donotusethispassword"
bitcoind_version: "0.17.1"
bitcoind_network: "testnet"
bitcoind_mount: "/var/lib/bitcoin" # Directory in which the external hdd is mounted
bitcoind_dir: "{{bitcoind_mount}}" # Location of the bitcoin data dir, should be the same as the bitcoind_mount

## lightningd
lightningd_version: "v0.6.3"
lightningd_network: "testnet"
lightningd_node_alias: "Test Lightning Node"
lightningd_build_dir: "/home/pi/lightning" # The location to build the c-lightning binaries
lightningd_dir: "/var/lib/bitcoin/lightning"

# Not needed for configuration, used for DRY and convenience:

## bitcoind
bitcoind_tarball: "bitcoin-{{bitcoind_version}}-arm-linux-gnueabihf.tar.gz"
bitcoind_url: "https://bitcoin.org/bin/bitcoin-core-{{bitcoind_version}}/{{bitcoind_tarball}}"
bitcoind_conf: "{{bitcoind_dir}}/bitcoin.conf"

## c-lightning/lightningd
lightningd_git_repo: "https://github.com/ElementsProject/lightning.git"
lightningd_conf: "{{lightningd_dir}}/lightningd.conf"

```

### SSH Keys

Put your ssh keys in `rpi-lightning-node-ansible/ansible/roles/raspbian/harden-ssh/files/keys`
