# Bludit: Bosh release

This is a release for [BOSH](http://bosh.io/) that deploys [Bludit](https://bludit.com), with Nginx and PHP-FPM.

## How to deploy this release

In this tutorial, I have Bosh-lite working on my computer, and my BOSH director has the IP 192.168.50.4.

### 1. Clone this repository

```
$ git clone git@github.com:dignajar/bludit-bosh-release.git
$ cd bludit-bosh-release
```

### 2. Build the release

```
$ bosh create release --force
```

### 3. Upload the release to BOSH director

```
$ bosh upload release
```

Check if the release is uploaded.

```
$ bosh releases

+----------------+------------+-------------+
| Name           | Versions   | Commit Hash |
+----------------+------------+-------------+
| bludit         | 0+dev.1    | b9dda341    |
+----------------+------------+-------------+
```


### 4. Deployment manifest

The file `manifest.yml` has a lot of properties about the infrastructure.

This manifest uses the stemcell `bosh-warden-boshlite-ubuntu-trusty-go_agent`, check if you have this stemcell on your BOSH.

```
$ bosh stemcells

+---------------------------------------------+---------------+---------+--------------------------------------+
| Name                                        | OS            | Version | CID                                  |
+---------------------------------------------+---------------+---------+--------------------------------------+
| bosh-warden-boshlite-ubuntu-trusty-go_agent | ubuntu-trusty | 3262.2* | 68ab61b0-090a-42bf-635a-c146fa3434a1 |
+---------------------------------------------+---------------+---------+--------------------------------------+
```

Get the `uuid` of the BOSH director.

```
$ bosh status --uuid

fb70f16a-4d6f-42f8-ad2e-3381a03dff18
```

Change the field `uuid` in the file `manifest.yml`, with this new one.

Now set the deployment manifest

```
$ bosh deployment manifest.yml

Deployment set to '.../bludit-bosh-release/manifest.yml'
```

### Deploy

```
$ bosh deploy
```

Check if the VM is up and running.

```
$ bosh vms

+-------------------------------------------------+---------+-----+---------+----------+
| VM                                              | State   | AZ  | VM Type | IPs      |
+-------------------------------------------------+---------+-----+---------+----------+
| bludit/0 (78e4d24e-d9cb-4a6e-8bba-c35b5651037a) | running | n/a | default | 10.0.0.2 |
+-------------------------------------------------+---------+-----+---------+----------+
```

Add the next static route, to get access to the VM on the Bosh director.

```
$ sudo ip route add 10.0.0.0/30 via 192.168.50.4
```

### Install Bludit

Go with your browser to the URL `http://10.0.0.2` and finish the installation.

## Sources

- https://bosh.io/docs
- https://github.com/mariash/learn-bosh-release
- https://github.com/cloudfoundry/bosh-sample-release
- https://github.com/eljuanchosf/my-first-bosh-release