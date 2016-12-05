# Bludit: Bosh release

This is a release for [BOSH](http://bosh.io/) that deploys [Bludit](https://bludit.com), with Nginx and PHP-FPM.

## 1. Clone this repository

```
$ git clone https://github.com/dignajar/bludit-bosh-release.git
$ cd bludit-bosh-release
```

## 2. Build the release

```
$ bosh create release --force
```

## 3. Upload the release to BOSH director

```
$ bosh upload release
```

Check the list of releases
```
$ bosh releases

+----------------+------------+-------------+
| Name           | Versions   | Commit Hash |
+----------------+------------+-------------+
| bludit         | 0+dev.1    | b9dda341    |
+----------------+------------+-------------+
```

## 4. Stemcells

Upload the stemcell to BOSH.
```
bosh upload stemcell https://s3.amazonaws.com/bosh-aws-light-stemcells/light-bosh-stemcell-3312.6-aws-xen-hvm-ubuntu-trusty-go_agent.tgz
```

Check the list of stemcells
```
$ bosh stemcells

Acting as user 'admin' on 'my-bosh'

+-----------------------------------------+---------------+---------+--------------------+
| Name                                    | OS            | Version | CID                |
+-----------------------------------------+---------------+---------+--------------------+
| bosh-aws-xen-hvm-ubuntu-trusty-go_agent | ubuntu-trusty | 3312.6  | ami-aa9e07c6 light |
+-----------------------------------------+---------------+---------+--------------------+

(*) Currently in-use

Stemcells total: 1
```

## 5. Deployment manifest
The file `manifest.yml` has a lot of properties about the infrastructure. This manifest is prepared for Amazon AWS, check the variables inside.

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

Deploy Bludit
```
$ bosh deploy
```

Check deployments
```
$ bosh deployments

+--------+----------------+------------------------------------------------+--------------+
| Name   | Release(s)     | Stemcell(s)                                    | Cloud Config |
+--------+----------------+------------------------------------------------+--------------+
| bludit | bludit/0+dev.1 | bosh-aws-xen-hvm-ubuntu-trusty-go_agent/3312.6 | none         |
+--------+----------------+------------------------------------------------+--------------+

Deployments total: 1
```

Check if the VM is up and running.

```
$ bosh vms

+-------------------------------------------------+---------+-----+----------------+---------------+
| VM                                              | State   | AZ  | VM Type        | IPs           |
+-------------------------------------------------+---------+-----+----------------+---------------+
| bludit/0 (2a31bd26-998d-4772-a95a-6844bef819c8) | running | n/a | infrastructure | 10.100.10.120 |
|                                                 |         |     |                | 52.67.56.58   |
+-------------------------------------------------+---------+-----+----------------+---------------+

VMs total: 1

```

## Finish Bludit installation

Go with your browser to the URL `http://{ELASTIC-IP}` and finish the installation.

## Sources

- https://bosh.io/docs
- https://github.com/mariash/learn-bosh-release
- https://github.com/cloudfoundry/bosh-sample-release
- https://github.com/eljuanchosf/my-first-bosh-release