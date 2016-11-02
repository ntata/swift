.. contents::
   :depth: 3
..

Swift Rolling Upgrade Swift 2.7.0 – Mitaka to current master (2.9.0 +)

1. Tests to be executed during an upgrade

    -tempest.api.object\_storage

    [
    `*https://github.com/openstack/tempest/tree/master/tempest/api/object\_storage* <https://github.com/openstack/tempest/tree/master/tempest/api/object_storage>`__
    ]

    -swift.test.functional[
    `*https://github.com/openstack/swift/tree/master/test/functional* <https://github.com/openstack/swift/tree/master/test/functional>`__
    ]

1. Tests to be executed after an upgrade

    -tempest.api.object\_storage

    [
    `*https://github.com/openstack/tempest/tree/master/tempest/api/object\_storage* <https://github.com/openstack/tempest/tree/master/tempest/api/object_storage>`__
    ]

    -swift.test.functional[
    `*https://github.com/openstack/swift/tree/master/test/functional* <https://github.com/openstack/swift/tree/master/test/functional>`__
    ]

Dataplane -

i.   Erasure Coding (EC storage policy) object download and test

ii.  Replication (Storage policy) object download and test

iii. Versioned writes (enable middleware) object download and test

iv.  Tempurl (enable middleware) object download and test

v.   Container , Account create and delete

Generic Object download ,upload: [
`*http://docs.openstack.org/cli-reference/swift.html* <http://docs.openstack.org/cli-reference/swift.html>`__
]

$ swift upload <container\_name> <file\_or\_directory>

container\_name -> name of the container to upload the object to

file\_or\_directory -> object to upload

$ swift download <container\_name> <object\_name>

How to set the storage policy: Define storage policies in
/etc/swift/swift.conf file

    **Swift Manual upgrade steps**

On a multi-node Swift cluster (1 Proxy , 3 ACO)

Steps – Upgrade a single storage (ACO) node first

1. Stop all background Swift jobs with –

    $ swift-init rest stop

1. Shutdown all Swift storage processes with –

    $ swift-init {account\|container\|object} shutdown --graceful

1. Upgrade all system packages and Swift code –

    -update source code

    $ git tag –l # lists all the tags for the given repo

    $ git checkout <tag\_to\_update\_to>

    -upgrading the packages

    $ sudo pip install –r requirements.txt --upgrade

    $ sudo pip install –r test-requirements.txt –upgrade

-  $ sudo python setup.py install

1. Update Swift configs (in test setup with SAIOs)

    $ sudo cp $HOME/swift/doc/saio/rsyncd.conf /etc/

    $ sudo sed –i “s/<your-user-name>/${USER}/” /etc/rsyncd.conf

    $ sudo cp $HOME/swift/doc/saio/rsyslog.d/10-swift.conf
    /etc/rsyslog.d/

    $ sudo cp –r $HOME/swift/doc/saio/swift /etc/swift

    $ sudo chown –R ${USER}:${USER} /etc/swift

    $ sudo cp $HOME/swift/test/sample.conf /etc/swift/test.conf

    $ sudo cp $HOME/swift/doc/saio/bin/\* $HOME/bin

    $ sudo chmod +x $HOME/bin/\*

1. If needed, reboot server

2. Start the storage service with – swift-init
   {account\|container\|object} start

3. Start background Swift jobs with – swift-init rest start

Upgrading all of the other storage nodes

-  Repeat the above steps for each node

Upgrading Proxy node \*\* (will update when tested with multiple
proxies)

ACO- Account Container Object

SAIO- Swift All In One

Reference -
`*https://www.swiftstack.com/blog/2013/12/20/upgrade-openstack-swift-no-downtime/* <https://www.swiftstack.com/blog/2013/12/20/upgrade-openstack-swift-no-downtime/>`__

Testing releases -

+----------------------------------+-------------+--------------------+
|                                  | unittests   | functional tests   |
+----------------------------------+-------------+--------------------+
| Mitaka (2.7.0) Before upgrade    | PASS        | PASS               |
+----------------------------------+-------------+--------------------+
| 2.9.0 – After upgrade (Node 2)   |             |                    |
+----------------------------------+-------------+--------------------+
| 2.9.0 – After upgrade (Node 3)   | PASS        | PASS               |
+----------------------------------+-------------+--------------------+
| 2.9.0 – After upgrade (Node 4)   | PASS        | PASS               |
+----------------------------------+-------------+--------------------+

**Logs **

1. Unit tests Pass- Mitaka – 2.7.0

+----------------------------------+----------------------------------+----------------------------------+----------------------------------+
| Node 1                           | Node 2                           | Node 3                           | Node 4                           |
+----------------------------------+----------------------------------+----------------------------------+----------------------------------+
| Stmts Miss Branch BrMiss Cover   | Stmts Miss Branch BrMiss Cover   | Stmts Miss Branch BrMiss Cover   | Stmts Miss Branch BrMiss Cover   |
|                                  |                                  |                                  |                                  |
| TOTAL 23378 1519 9240 972 92%    | TOTAL 23378 1507 9240 970 92%    | TOTAL 23378 1507 9240 971 92%    | TOTAL 23378 1519 9240 971 92%    |
|                                  |                                  |                                  |                                  |
| Ran 4553 tests in 107.454s       | Ran 4553 tests in 104.580s       | Ran 4553 tests in 108.287s       | Ran 4553 tests in 109.010s       |
+----------------------------------+----------------------------------+----------------------------------+----------------------------------+

1. Functional Pass- Mitaka – 2.7.0

+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
| Node 1                                                                                 | Node 2                                                                                 | Node 3                                                                                 | Node 4                                                                                 |
+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
| ======                                                                                 | ======                                                                                 | ======                                                                                 | ======                                                                                 |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| Totals                                                                                 | Totals                                                                                 | Totals                                                                                 | Totals                                                                                 |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| ======                                                                                 | ======                                                                                 | ======                                                                                 | ======                                                                                 |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| Ran: 422 tests in 119.0000 sec.                                                        | Ran: 422 tests in 116.0000 sec.                                                        | Ran: 422 tests in 98.0000 sec.                                                         | Ran: 422 tests in 98.0000 sec.                                                         |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| - Passed: 406                                                                          | - Passed: 406                                                                          | - Passed: 406                                                                          | - Passed: 406                                                                          |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| - Skipped: 16                                                                          | - Skipped: 16                                                                          | - Skipped: 16                                                                          | - Skipped: 16                                                                          |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| - Expected Fail: 0                                                                     | - Expected Fail: 0                                                                     | - Expected Fail: 0                                                                     | - Expected Fail: 0                                                                     |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| - Unexpected Success: 0                                                                | - Unexpected Success: 0                                                                | - Unexpected Success: 0                                                                | - Unexpected Success: 0                                                                |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| - Failed: 0                                                                            | - Failed: 0                                                                            | - Failed: 0                                                                            | - Failed: 0                                                                            |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| Sum of execute time for each test: 117.2884 sec.                                       | Sum of execute time for each test: 114.1175 sec.                                       | Sum of execute time for each test: 97.4543 sec.                                        | Sum of execute time for each test: 97.2962 sec.                                        |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| ==============                                                                         | ==============                                                                         | ==============                                                                         | ==============                                                                         |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| Worker Balance                                                                         | Worker Balance                                                                         | Worker Balance                                                                         | Worker Balance                                                                         |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| ==============                                                                         | ==============                                                                         | ==============                                                                         | ==============                                                                         |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| - Worker 0 (422 tests) => 0:01:57.704223                                               | - Worker 0 (422 tests) => 0:01:54.517981                                               | - Worker 0 (422 tests) => 0:01:37.757013                                               | - Worker 0 (422 tests) => 0:01:37.615864                                               |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| Slowest Tests:                                                                         | Slowest Tests:                                                                         | Slowest Tests:                                                                         | Slowest Tests:                                                                         |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| Test id Runtime (s)                                                                    | Test id Runtime (s)                                                                    | Test id Runtime (s)                                                                    | Test id Runtime (s)                                                                    |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| ------------------------------------------------------------------------ -----------   | ------------------------------------------------------------------------ -----------   | ------------------------------------------------------------------------ -----------   | ------------------------------------------------------------------------ -----------   |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestFile.testFileSizeLimit 12.025                                | test.functional.tests.TestFileUTF8.testFileSizeLimit 12.032                            | test.functional.tests.TestFileUTF8.testFileSizeLimit 12.024                            | test.functional.tests.TestFile.testFileSizeLimit 12.023                                |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestFileUTF8.testFileSizeLimit 12.019                            | test.functional.tests.TestFile.testFileSizeLimit 12.022                                | test.functional.tests.TestFile.testFileSizeLimit 12.023                                | test.functional.tests.TestFileUTF8.testFileSizeLimit 12.019                            |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestAccount.testContainerSerializedInfo 4.393                    | test.functional.test\_object.TestObject.test\_x\_delete\_at 4.272                      | test.functional.test\_object.TestObject.test\_x\_delete\_at 4.222                      | test.functional.test\_object.TestObject.test\_x\_delete\_at 4.218                      |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.test\_object.TestObject.test\_x\_delete\_at 4.317                      | test.functional.tests.TestAccountUTF8.testContainerSerializedInfo 3.989                | test.functional.tests.TestContainer.testContainerExistenceCachingProblem 2.890         | test.functional.tests.TestContainer.testContainerExistenceCachingProblem 2.800         |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestAccountNoContainers.testGetRequest 3.795                     | test.functional.tests.TestContainer.testContainerExistenceCachingProblem 3.682         | test.functional.tests.TestAccountUTF8.testContainerSerializedInfo 2.815                | test.functional.tests.TestAccountUTF8.testContainerSerializedInfo 2.581                |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestContainer.testContainerExistenceCachingProblem 3.572         | test.functional.tests.TestAccount.testContainerSerializedInfo 3.510                    | test.functional.tests.TestAccountNoContainers.testGetRequest 2.388                     | test.functional.tests.TestAccountNoContainers.testGetRequest 2.495                     |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestAccountUTF8.testContainerSerializedInfo 3.422                | test.functional.tests.TestAccountNoContainers.testGetRequest 3.160                     | test.functional.tests.TestAccount.testContainerSerializedInfo 2.385                    | test.functional.tests.TestAccount.testContainerSerializedInfo 2.413                    |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestSlo.test\_slo\_container\_listing 2.803                      | test.functional.tests.TestSlo.test\_slo\_container\_listing 2.600                      | test.functional.test\_object.TestObject.test\_x\_delete\_after 2.209                   | test.functional.test\_object.TestObject.test\_x\_delete\_after 2.192                   |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestFileComparison.testIfMatch 2.411                             | test.functional.tests.TestFileComparison.testIfMatch 2.478                             | test.functional.tests.TestSlo.test\_slo\_container\_listing 2.041                      | test.functional.tests.TestSlo.test\_slo\_container\_listing 1.987                      |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
| test.functional.tests.TestContainerPaths.testContainerListing 2.342                    | test.functional.test\_object.TestObject.test\_x\_delete\_after 2.266                   | test.functional.tests.TestFileComparison.testIfMatch 1.894                             | test.functional.tests.TestFileComparison.testIfMatch 1.893                             |
|                                                                                        |                                                                                        |                                                                                        |                                                                                        |
|                                                                                        | /home/swift/swift                                                                      | /home/swift/swift                                                                      | /home/swift/swift                                                                      |
+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+

1. Node 2 – start A/C/O services

swift@sha-mn-cluster-2:~$ **swift-init account all start**

WARNING: Unable to modify file descriptor limit. Running as non-root?

Starting container-updater...(/etc/swift/container-server/1.conf)

Starting container-updater...(/etc/swift/container-server/2.conf)

Starting container-updater...(/etc/swift/container-server/3.conf)

Starting container-updater...(/etc/swift/container-server/4.conf)

Starting account-auditor...(/etc/swift/account-server/1.conf)

Starting account-auditor...(/etc/swift/account-server/2.conf)

Starting account-auditor...(/etc/swift/account-server/3.conf)

Starting account-auditor...(/etc/swift/account-server/4.conf)

Starting object-replicator...(/etc/swift/object-server/1.conf)

Starting object-replicator...(/etc/swift/object-server/2.conf)

Starting object-replicator...(/etc/swift/object-server/3.conf)

Starting object-replicator...(/etc/swift/object-server/4.conf)

Starting container-reconciler...(/etc/swift/container-reconciler.conf)

Starting container-replicator...(/etc/swift/container-server/1.conf)

Starting container-replicator...(/etc/swift/container-server/2.conf)

Starting container-replicator...(/etc/swift/container-server/3.conf)

Starting container-replicator...(/etc/swift/container-server/4.conf)

Starting object-auditor...(/etc/swift/object-server/1.conf)

Starting object-auditor...(/etc/swift/object-server/2.conf)

Starting object-auditor...(/etc/swift/object-server/3.conf)

Starting object-auditor...(/etc/swift/object-server/4.conf)

Starting object-expirer...(/etc/swift/object-expirer.conf)

Starting container-auditor...(/etc/swift/container-server/1.conf)

Starting container-auditor...(/etc/swift/container-server/2.conf)

Starting container-auditor...(/etc/swift/container-server/3.conf)

Starting container-auditor...(/etc/swift/container-server/4.conf)

Starting container-server...(/etc/swift/container-server/1.conf)

Starting container-server...(/etc/swift/container-server/2.conf)

Starting container-server...(/etc/swift/container-server/3.conf)

Starting container-server...(/etc/swift/container-server/4.conf)

Starting object-reconstructor...(/etc/swift/object-server/1.conf)

Starting object-reconstructor...(/etc/swift/object-server/2.conf)

Starting object-reconstructor...(/etc/swift/object-server/3.conf)

Starting object-reconstructor...(/etc/swift/object-server/4.conf)

Starting account-server...(/etc/swift/account-server/1.conf)

Starting account-server...(/etc/swift/account-server/2.conf)

Starting account-server...(/etc/swift/account-server/3.conf)

Starting account-server...(/etc/swift/account-server/4.conf)

Starting account-reaper...(/etc/swift/account-server/1.conf)

Starting account-reaper...(/etc/swift/account-server/2.conf)

Starting account-reaper...(/etc/swift/account-server/3.conf)

Starting account-reaper...(/etc/swift/account-server/4.conf)

Unable to locate config for proxy-server

Starting account-replicator...(/etc/swift/account-server/1.conf)

Starting account-replicator...(/etc/swift/account-server/2.conf)

Starting account-replicator...(/etc/swift/account-server/3.conf)

Starting account-replicator...(/etc/swift/account-server/4.conf)

Starting object-updater...(/etc/swift/object-server/1.conf)

Starting object-updater...(/etc/swift/object-server/2.conf)

Starting object-updater...(/etc/swift/object-server/3.conf)

Starting object-updater...(/etc/swift/object-server/4.conf)

Starting object-server...(/etc/swift/object-server/1.conf)

Starting object-server...(/etc/swift/object-server/2.conf)

Starting object-server...(/etc/swift/object-server/3.conf)

Starting object-server...(/etc/swift/object-server/4.conf)

Starting container-sync...(/etc/swift/container-server/1.conf)

Starting container-sync...(/etc/swift/container-server/2.conf)

Starting container-sync...(/etc/swift/container-server/3.conf)

Starting container-sync...(/etc/swift/container-server/4.conf)

swift@sha-mn-cluster-2:~$

swift@sha-mn-cluster-2:~$ **swift-init container all start**

WARNING: Unable to modify file descriptor limit. Running as non-root?

container-updater running (28160 - /etc/swift/container-server/1.conf)

container-updater running (28161 - /etc/swift/container-server/2.conf)

container-updater running (28162 - /etc/swift/container-server/3.conf)

container-updater running (28163 - /etc/swift/container-server/4.conf)

container-updater already started...

account-auditor running (28164 - /etc/swift/account-server/1.conf)

account-auditor running (28165 - /etc/swift/account-server/2.conf)

account-auditor running (28166 - /etc/swift/account-server/3.conf)

account-auditor running (28167 - /etc/swift/account-server/4.conf)

account-auditor already started...

object-replicator running (28168 - /etc/swift/object-server/1.conf)

object-replicator running (28169 - /etc/swift/object-server/2.conf)

object-replicator running (28170 - /etc/swift/object-server/3.conf)

object-replicator running (28175 - /etc/swift/object-server/4.conf)

object-replicator already started...

container-sync running (28408 - /etc/swift/container-server/2.conf)

container-sync running (28403 - /etc/swift/container-server/1.conf)

container-sync running (28435 - /etc/swift/container-server/4.conf)

container-sync running (28423 - /etc/swift/container-server/3.conf)

container-sync already started...

container-replicator running (28186 -
/etc/swift/container-server/1.conf)

container-replicator running (28195 -
/etc/swift/container-server/3.conf)

container-replicator running (28190 -
/etc/swift/container-server/2.conf)

container-replicator running (28199 -
/etc/swift/container-server/4.conf)

container-replicator already started...

object-auditor running (28217 - /etc/swift/object-server/4.conf)

object-auditor running (28204 - /etc/swift/object-server/1.conf)

object-auditor running (28214 - /etc/swift/object-server/3.conf)

object-auditor running (28206 - /etc/swift/object-server/2.conf)

object-auditor already started...

object-expirer running (28220 - /etc/swift/object-expirer.conf)

object-expirer already started...

container-auditor running (28224 - /etc/swift/container-server/1.conf)

container-auditor running (28242 - /etc/swift/container-server/4.conf)

container-auditor running (28238 - /etc/swift/container-server/3.conf)

container-auditor running (28231 - /etc/swift/container-server/2.conf)

container-auditor already started...

container-server running (28248 - /etc/swift/container-server/1.conf)

container-server running (28266 - /etc/swift/container-server/4.conf)

container-server running (28262 - /etc/swift/container-server/3.conf)

container-server running (28254 - /etc/swift/container-server/2.conf)

container-server already started...

object-reconstructor running (28273 - /etc/swift/object-server/1.conf)

object-reconstructor running (28275 - /etc/swift/object-server/2.conf)

object-reconstructor running (28278 - /etc/swift/object-server/3.conf)

object-reconstructor running (28281 - /etc/swift/object-server/4.conf)

object-reconstructor already started...

object-server running (28388 - /etc/swift/object-server/3.conf)

object-server running (28381 - /etc/swift/object-server/1.conf)

object-server running (28398 - /etc/swift/object-server/4.conf)

object-server running (28383 - /etc/swift/object-server/2.conf)

object-server already started...

account-reaper running (28306 - /etc/swift/account-server/1.conf)

account-reaper running (28331 - /etc/swift/account-server/4.conf)

account-reaper running (28317 - /etc/swift/account-server/3.conf)

account-reaper running (28310 - /etc/swift/account-server/2.conf)

account-reaper already started...

Unable to locate config for proxy-server

account-replicator running (28352 - /etc/swift/account-server/2.conf)

account-replicator running (28347 - /etc/swift/account-server/1.conf)

account-replicator running (28364 - /etc/swift/account-server/4.conf)

account-replicator running (28358 - /etc/swift/account-server/3.conf)

account-replicator already started...

object-updater running (28376 - /etc/swift/object-server/3.conf)

object-updater running (28369 - /etc/swift/object-server/1.conf)

object-updater running (28378 - /etc/swift/object-server/4.conf)

object-updater running (28372 - /etc/swift/object-server/2.conf)

object-updater already started...

container-reconciler running (28184 -
/etc/swift/container-reconciler.conf)

container-reconciler already started...

account-server running (28298 - /etc/swift/account-server/3.conf)

account-server running (28302 - /etc/swift/account-server/4.conf)

account-server running (28294 - /etc/swift/account-server/2.conf)

account-server running (28287 - /etc/swift/account-server/1.conf)

account-server already started...

swift@sha-mn-cluster-2:~$

swift@sha-mn-cluster-2:~$ **swift-init object all start**

WARNING: Unable to modify file descriptor limit. Running as non-root?

container-updater running (28160 - /etc/swift/container-server/1.conf)

container-updater running (28161 - /etc/swift/container-server/2.conf)

container-updater running (28162 - /etc/swift/container-server/3.conf)

container-updater running (28163 - /etc/swift/container-server/4.conf)

container-updater already started...

account-auditor running (28164 - /etc/swift/account-server/1.conf)

account-auditor running (28165 - /etc/swift/account-server/2.conf)

account-auditor running (28166 - /etc/swift/account-server/3.conf)

account-auditor running (28167 - /etc/swift/account-server/4.conf)

account-auditor already started...

object-replicator running (28168 - /etc/swift/object-server/1.conf)

object-replicator running (28169 - /etc/swift/object-server/2.conf)

object-replicator running (28170 - /etc/swift/object-server/3.conf)

object-replicator running (28175 - /etc/swift/object-server/4.conf)

object-replicator already started...

container-sync running (28408 - /etc/swift/container-server/2.conf)

container-sync running (28403 - /etc/swift/container-server/1.conf)

container-sync running (28435 - /etc/swift/container-server/4.conf)

container-sync running (28423 - /etc/swift/container-server/3.conf)

container-sync already started...

container-replicator running (28186 -
/etc/swift/container-server/1.conf)

container-replicator running (28195 -
/etc/swift/container-server/3.conf)

container-replicator running (28190 -
/etc/swift/container-server/2.conf)

container-replicator running (28199 -
/etc/swift/container-server/4.conf)

container-replicator already started...

object-server running (28388 - /etc/swift/object-server/3.conf)

object-server running (28381 - /etc/swift/object-server/1.conf)

object-server running (28398 - /etc/swift/object-server/4.conf)

object-server running (28383 - /etc/swift/object-server/2.conf)

object-server already started...

object-expirer running (28220 - /etc/swift/object-expirer.conf)

object-expirer already started...

container-auditor running (28224 - /etc/swift/container-server/1.conf)

container-auditor running (28242 - /etc/swift/container-server/4.conf)

container-auditor running (28238 - /etc/swift/container-server/3.conf)

container-auditor running (28231 - /etc/swift/container-server/2.conf)

container-auditor already started...

container-server running (28248 - /etc/swift/container-server/1.conf)

container-server running (28266 - /etc/swift/container-server/4.conf)

container-server running (28262 - /etc/swift/container-server/3.conf)

container-server running (28254 - /etc/swift/container-server/2.conf)

container-server already started...

object-reconstructor running (28273 - /etc/swift/object-server/1.conf)

object-reconstructor running (28275 - /etc/swift/object-server/2.conf)

object-reconstructor running (28278 - /etc/swift/object-server/3.conf)

object-reconstructor running (28281 - /etc/swift/object-server/4.conf)

object-reconstructor already started...

object-auditor running (28217 - /etc/swift/object-server/4.conf)

object-auditor running (28204 - /etc/swift/object-server/1.conf)

object-auditor running (28214 - /etc/swift/object-server/3.conf)

object-auditor running (28206 - /etc/swift/object-server/2.conf)

object-auditor already started...

account-reaper running (28306 - /etc/swift/account-server/1.conf)

account-reaper running (28331 - /etc/swift/account-server/4.conf)

account-reaper running (28317 - /etc/swift/account-server/3.conf)

account-reaper running (28310 - /etc/swift/account-server/2.conf)

account-reaper already started...

Unable to locate config for proxy-server

account-replicator running (28352 - /etc/swift/account-server/2.conf)

account-replicator running (28347 - /etc/swift/account-server/1.conf)

account-replicator running (28364 - /etc/swift/account-server/4.conf)

account-replicator running (28358 - /etc/swift/account-server/3.conf)

account-replicator already started...

object-updater running (28376 - /etc/swift/object-server/3.conf)

object-updater running (28369 - /etc/swift/object-server/1.conf)

object-updater running (28378 - /etc/swift/object-server/4.conf)

object-updater running (28372 - /etc/swift/object-server/2.conf)

object-updater already started...

container-reconciler running (28184 -
/etc/swift/container-reconciler.conf)

container-reconciler already started...

account-server running (28298 - /etc/swift/account-server/3.conf)

account-server running (28302 - /etc/swift/account-server/4.conf)

account-server running (28294 - /etc/swift/account-server/2.conf)

account-server running (28287 - /etc/swift/account-server/1.conf)

account-server already started...

swift@sha-mn-cluster-2:~$

1. Node 3 – Start A/C/O services

    swift@sha-mn-cluster-3:~$ **swift-init account all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    Starting container-updater...(/etc/swift/container-server/1.conf)

    Starting container-updater...(/etc/swift/container-server/2.conf)

    Starting container-updater...(/etc/swift/container-server/3.conf)

    Starting container-updater...(/etc/swift/container-server/4.conf)

    Starting account-auditor...(/etc/swift/account-server/1.conf)

    Starting account-auditor...(/etc/swift/account-server/2.conf)

    Starting account-auditor...(/etc/swift/account-server/3.conf)

    Starting account-auditor...(/etc/swift/account-server/4.conf)

    Starting object-replicator...(/etc/swift/object-server/1.conf)

    Starting object-replicator...(/etc/swift/object-server/2.conf)

    Starting object-replicator...(/etc/swift/object-server/3.conf)

    Starting object-replicator...(/etc/swift/object-server/4.conf)

    Starting
    container-reconciler...(/etc/swift/container-reconciler.conf)

    Starting container-replicator...(/etc/swift/container-server/1.conf)

    Starting container-replicator...(/etc/swift/container-server/2.conf)

    Starting container-replicator...(/etc/swift/container-server/3.conf)

    Starting container-replicator...(/etc/swift/container-server/4.conf)

    Starting object-auditor...(/etc/swift/object-server/1.conf)

    Starting object-auditor...(/etc/swift/object-server/2.conf)

    Starting object-auditor...(/etc/swift/object-server/3.conf)

    Starting object-auditor...(/etc/swift/object-server/4.conf)

    Starting object-expirer...(/etc/swift/object-expirer.conf)

    Starting container-auditor...(/etc/swift/container-server/1.conf)

    Starting container-auditor...(/etc/swift/container-server/2.conf)

    Starting container-auditor...(/etc/swift/container-server/3.conf)

    Starting container-auditor...(/etc/swift/container-server/4.conf)

    Starting container-server...(/etc/swift/container-server/1.conf)

    Starting container-server...(/etc/swift/container-server/2.conf)

    Starting container-server...(/etc/swift/container-server/3.conf)

    Starting container-server...(/etc/swift/container-server/4.conf)

    Starting object-reconstructor...(/etc/swift/object-server/1.conf)

    Starting object-reconstructor...(/etc/swift/object-server/2.conf)

    Starting object-reconstructor...(/etc/swift/object-server/3.conf)

    Starting object-reconstructor...(/etc/swift/object-server/4.conf)

    Starting account-server...(/etc/swift/account-server/1.conf)

    Starting account-server...(/etc/swift/account-server/2.conf)

    Starting account-server...(/etc/swift/account-server/3.conf)

    Starting account-server...(/etc/swift/account-server/4.conf)

    Starting account-reaper...(/etc/swift/account-server/1.conf)

    Starting account-reaper...(/etc/swift/account-server/2.conf)

    Starting account-reaper...(/etc/swift/account-server/3.conf)

    Starting account-reaper...(/etc/swift/account-server/4.conf)

    Unable to locate config for proxy-server

    Starting account-replicator...(/etc/swift/account-server/1.conf)

    Starting account-replicator...(/etc/swift/account-server/2.conf)

    Starting account-replicator...(/etc/swift/account-server/3.conf)

    Starting account-replicator...(/etc/swift/account-server/4.conf)

    Starting object-updater...(/etc/swift/object-server/1.conf)

    Starting object-updater...(/etc/swift/object-server/2.conf)

    Starting object-updater...(/etc/swift/object-server/3.conf)

    Starting object-updater...(/etc/swift/object-server/4.conf)

    Starting object-server...(/etc/swift/object-server/1.conf)

    Starting object-server...(/etc/swift/object-server/2.conf)

    Starting object-server...(/etc/swift/object-server/3.conf)

    Starting object-server...(/etc/swift/object-server/4.conf)

    Starting container-sync...(/etc/swift/container-server/1.conf)

    Starting container-sync...(/etc/swift/container-server/2.conf)

    Starting container-sync...(/etc/swift/container-server/3.conf)

    Starting container-sync...(/etc/swift/container-server/4.conf)

    swift@sha-mn-cluster-3:~$ **swift-init container all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    container-updater running (27874 -
    /etc/swift/container-server/1.conf)

    container-updater running (27875 -
    /etc/swift/container-server/2.conf)

    container-updater running (27876 -
    /etc/swift/container-server/3.conf)

    container-updater running (27877 -
    /etc/swift/container-server/4.conf)

    container-updater already started...

    account-auditor running (27880 - /etc/swift/account-server/3.conf)

    account-auditor running (27881 - /etc/swift/account-server/4.conf)

    account-auditor running (27878 - /etc/swift/account-server/1.conf)

    account-auditor running (27879 - /etc/swift/account-server/2.conf)

    account-auditor already started...

    object-replicator running (27882 - /etc/swift/object-server/1.conf)

    object-replicator running (27883 - /etc/swift/object-server/2.conf)

    object-replicator running (27884 - /etc/swift/object-server/3.conf)

    object-replicator running (27886 - /etc/swift/object-server/4.conf)

    object-replicator already started...

    container-sync running (28128 - /etc/swift/container-server/4.conf)

    container-sync running (28123 - /etc/swift/container-server/3.conf)

    container-sync running (28116 - /etc/swift/container-server/2.conf)

    container-sync running (28110 - /etc/swift/container-server/1.conf)

    container-sync already started...

    container-replicator running (27898 -
    /etc/swift/container-server/1.conf)

    container-replicator running (27899 -
    /etc/swift/container-server/2.conf)

    container-replicator running (27901 -
    /etc/swift/container-server/3.conf)

    container-replicator running (27902 -
    /etc/swift/container-server/4.conf)

    container-replicator already started...

    object-auditor running (27920 - /etc/swift/object-server/3.conf)

    object-auditor running (27913 - /etc/swift/object-server/2.conf)

    object-auditor running (27909 - /etc/swift/object-server/1.conf)

    object-auditor running (27925 - /etc/swift/object-server/4.conf)

    object-auditor already started...

    object-expirer running (27931 - /etc/swift/object-expirer.conf)

    object-expirer already started...

    container-auditor running (27938 -
    /etc/swift/container-server/2.conf)

    container-auditor running (27947 -
    /etc/swift/container-server/4.conf)

    container-auditor running (27934 -
    /etc/swift/container-server/1.conf)

    container-auditor running (27943 -
    /etc/swift/container-server/3.conf)

    container-auditor already started...

    container-server running (27953 -
    /etc/swift/container-server/1.conf)

    container-server running (27971 -
    /etc/swift/container-server/4.conf)

    container-server running (27966 -
    /etc/swift/container-server/3.conf)

    container-server running (27959 -
    /etc/swift/container-server/2.conf)

    container-server already started...

    object-reconstructor running (27988 -
    /etc/swift/object-server/4.conf)

    object-reconstructor running (27983 -
    /etc/swift/object-server/3.conf)

    object-reconstructor running (27980 -
    /etc/swift/object-server/2.conf)

    object-reconstructor running (27975 -
    /etc/swift/object-server/1.conf)

    object-reconstructor already started...

    object-server running (28104 - /etc/swift/object-server/3.conf)

    object-server running (28090 - /etc/swift/object-server/1.conf)

    object-server running (28100 - /etc/swift/object-server/2.conf)

    object-server running (28106 - /etc/swift/object-server/4.conf)

    object-server already started...

    account-reaper running (28032 - /etc/swift/account-server/4.conf)

    account-reaper running (28018 - /etc/swift/account-server/1.conf)

    account-reaper running (28028 - /etc/swift/account-server/2.conf)

    account-reaper running (28030 - /etc/swift/account-server/3.conf)

    account-reaper already started...

    Unable to locate config for proxy-server

    account-replicator running (28056 -
    /etc/swift/account-server/4.conf)

    account-replicator running (28042 -
    /etc/swift/account-server/2.conf)

    account-replicator running (28036 -
    /etc/swift/account-server/1.conf)

    account-replicator running (28050 -
    /etc/swift/account-server/3.conf)

    account-replicator already started...

    object-updater running (28065 - /etc/swift/object-server/1.conf)

    object-updater running (28069 - /etc/swift/object-server/2.conf)

    object-updater running (28081 - /etc/swift/object-server/4.conf)

    object-updater running (28077 - /etc/swift/object-server/3.conf)

    object-updater already started...

    container-reconciler running (27891 -
    /etc/swift/container-reconciler.conf)

    container-reconciler already started...

    account-server running (28014 - /etc/swift/account-server/4.conf)

    account-server running (27995 - /etc/swift/account-server/1.conf)

    account-server running (28006 - /etc/swift/account-server/3.conf)

    account-server running (27998 - /etc/swift/account-server/2.conf)

    account-server already started...

    swift@sha-mn-cluster-3:~$ **swift-init object all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    container-updater running (27874 -
    /etc/swift/container-server/1.conf)

    container-updater running (27875 -
    /etc/swift/container-server/2.conf)

    container-updater running (27876 -
    /etc/swift/container-server/3.conf)

    container-updater running (27877 -
    /etc/swift/container-server/4.conf)

    container-updater already started...

    account-auditor running (27880 - /etc/swift/account-server/3.conf)

    account-auditor running (27881 - /etc/swift/account-server/4.conf)

    account-auditor running (27878 - /etc/swift/account-server/1.conf)

    account-auditor running (27879 - /etc/swift/account-server/2.conf)

    account-auditor already started...

    object-replicator running (27882 - /etc/swift/object-server/1.conf)

    object-replicator running (27883 - /etc/swift/object-server/2.conf)

    object-replicator running (27884 - /etc/swift/object-server/3.conf)

    object-replicator running (27886 - /etc/swift/object-server/4.conf)

    object-replicator already started...

    container-sync running (28128 - /etc/swift/container-server/4.conf)

    container-sync running (28123 - /etc/swift/container-server/3.conf)

    container-sync running (28116 - /etc/swift/container-server/2.conf)

    container-sync running (28110 - /etc/swift/container-server/1.conf)

    container-sync already started...

    container-replicator running (27898 -
    /etc/swift/container-server/1.conf)

    container-replicator running (27899 -
    /etc/swift/container-server/2.conf)

    container-replicator running (27901 -
    /etc/swift/container-server/3.conf)

    container-replicator running (27902 -
    /etc/swift/container-server/4.conf)

    container-replicator already started...

    object-server running (28104 - /etc/swift/object-server/3.conf)

    object-server running (28090 - /etc/swift/object-server/1.conf)

    object-server running (28100 - /etc/swift/object-server/2.conf)

    object-server running (28106 - /etc/swift/object-server/4.conf)

    object-server already started...

    object-expirer running (27931 - /etc/swift/object-expirer.conf)

    object-expirer already started...

    container-auditor running (27938 -
    /etc/swift/container-server/2.conf)

    container-auditor running (27947 -
    /etc/swift/container-server/4.conf)

    container-auditor running (27934 -
    /etc/swift/container-server/1.conf)

    container-auditor running (27943 -
    /etc/swift/container-server/3.conf)

    container-auditor already started...

    container-server running (27953 -
    /etc/swift/container-server/1.conf)

    container-server running (27971 -
    /etc/swift/container-server/4.conf)

    container-server running (27966 -
    /etc/swift/container-server/3.conf)

    container-server running (27959 -
    /etc/swift/container-server/2.conf)

    container-server already started...

    object-reconstructor running (27988 -
    /etc/swift/object-server/4.conf)

    object-reconstructor running (27983 -
    /etc/swift/object-server/3.conf)

    object-reconstructor running (27980 -
    /etc/swift/object-server/2.conf)

    object-reconstructor running (27975 -
    /etc/swift/object-server/1.conf)

    object-reconstructor already started...

    object-auditor running (27920 - /etc/swift/object-server/3.conf)

    object-auditor running (27913 - /etc/swift/object-server/2.conf)

    object-auditor running (27909 - /etc/swift/object-server/1.conf)

    object-auditor running (27925 - /etc/swift/object-server/4.conf)

    object-auditor already started...

    account-reaper running (28032 - /etc/swift/account-server/4.conf)

    account-reaper running (28018 - /etc/swift/account-server/1.conf)

    account-reaper running (28028 - /etc/swift/account-server/2.conf)

    account-reaper running (28030 - /etc/swift/account-server/3.conf)

    account-reaper already started...

    Unable to locate config for proxy-server

    account-replicator running (28056 -
    /etc/swift/account-server/4.conf)

    account-replicator running (28042 -
    /etc/swift/account-server/2.conf)

    account-replicator running (28036 -
    /etc/swift/account-server/1.conf)

    account-replicator running (28050 -
    /etc/swift/account-server/3.conf)

    account-replicator already started...

    object-updater running (28065 - /etc/swift/object-server/1.conf)

    object-updater running (28069 - /etc/swift/object-server/2.conf)

    object-updater running (28081 - /etc/swift/object-server/4.conf)

    object-updater running (28077 - /etc/swift/object-server/3.conf)

    object-updater already started...

    container-reconciler running (27891 -
    /etc/swift/container-reconciler.conf)

    container-reconciler already started...

    account-server running (28014 - /etc/swift/account-server/4.conf)

    account-server running (27995 - /etc/swift/account-server/1.conf)

    account-server running (28006 - /etc/swift/account-server/3.conf)

    account-server running (27998 - /etc/swift/account-server/2.conf)

    account-server already started...

    swift@sha-mn-cluster-3:~$

1. Node 4 – Start A/C/O services

    swift@sha-mn-cluster-4:~$ **swift-init account all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    Starting container-updater...(/etc/swift/container-server/1.conf)

    Starting container-updater...(/etc/swift/container-server/2.conf)

    Starting container-updater...(/etc/swift/container-server/3.conf)

    Starting container-updater...(/etc/swift/container-server/4.conf)

    Starting account-auditor...(/etc/swift/account-server/1.conf)

    Starting account-auditor...(/etc/swift/account-server/2.conf)

    Starting account-auditor...(/etc/swift/account-server/3.conf)

    Starting account-auditor...(/etc/swift/account-server/4.conf)

    Starting object-replicator...(/etc/swift/object-server/1.conf)

    Starting object-replicator...(/etc/swift/object-server/2.conf)

    Starting object-replicator...(/etc/swift/object-server/3.conf)

    Starting object-replicator...(/etc/swift/object-server/4.conf)

    Starting
    container-reconciler...(/etc/swift/container-reconciler.conf)

    Starting container-replicator...(/etc/swift/container-server/1.conf)

    Starting container-replicator...(/etc/swift/container-server/2.conf)

    Starting container-replicator...(/etc/swift/container-server/3.conf)

    Starting container-replicator...(/etc/swift/container-server/4.conf)

    Starting object-auditor...(/etc/swift/object-server/1.conf)

    Starting object-auditor...(/etc/swift/object-server/2.conf)

    Starting object-auditor...(/etc/swift/object-server/3.conf)

    Starting object-auditor...(/etc/swift/object-server/4.conf)

    Starting object-expirer...(/etc/swift/object-expirer.conf)

    Starting container-auditor...(/etc/swift/container-server/1.conf)

    Starting container-auditor...(/etc/swift/container-server/2.conf)

    Starting container-auditor...(/etc/swift/container-server/3.conf)

    Starting container-auditor...(/etc/swift/container-server/4.conf)

    Starting container-server...(/etc/swift/container-server/1.conf)

    Starting container-server...(/etc/swift/container-server/2.conf)

    Starting container-server...(/etc/swift/container-server/3.conf)

    Starting container-server...(/etc/swift/container-server/4.conf)

    Starting object-reconstructor...(/etc/swift/object-server/1.conf)

    Starting object-reconstructor...(/etc/swift/object-server/2.conf)

    Starting object-reconstructor...(/etc/swift/object-server/3.conf)

    Starting object-reconstructor...(/etc/swift/object-server/4.conf)

    Starting account-server...(/etc/swift/account-server/1.conf)

    Starting account-server...(/etc/swift/account-server/2.conf)

    Starting account-server...(/etc/swift/account-server/3.conf)

    Starting account-server...(/etc/swift/account-server/4.conf)

    Starting account-reaper...(/etc/swift/account-server/1.conf)

    Starting account-reaper...(/etc/swift/account-server/2.conf)

    Starting account-reaper...(/etc/swift/account-server/3.conf)

    Starting account-reaper...(/etc/swift/account-server/4.conf)

    Unable to locate config for proxy-server

    Starting account-replicator...(/etc/swift/account-server/1.conf)

    Starting account-replicator...(/etc/swift/account-server/2.conf)

    Starting account-replicator...(/etc/swift/account-server/3.conf)

    Starting account-replicator...(/etc/swift/account-server/4.conf)

    Starting object-updater...(/etc/swift/object-server/1.conf)

    Starting object-updater...(/etc/swift/object-server/2.conf)

    Starting object-updater...(/etc/swift/object-server/3.conf)

    Starting object-updater...(/etc/swift/object-server/4.conf)

    Starting object-server...(/etc/swift/object-server/1.conf)

    Starting object-server...(/etc/swift/object-server/2.conf)

    Starting object-server...(/etc/swift/object-server/3.conf)

    Starting object-server...(/etc/swift/object-server/4.conf)

    Starting container-sync...(/etc/swift/container-server/1.conf)

    Starting container-sync...(/etc/swift/container-server/2.conf)

    Starting container-sync...(/etc/swift/container-server/3.conf)

    Starting container-sync...(/etc/swift/container-server/4.conf)

    swift@sha-mn-cluster-4:~$ **swift-init container all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    container-updater running (27554 -
    /etc/swift/container-server/1.conf)

    container-updater running (27555 -
    /etc/swift/container-server/2.conf)

    container-updater running (27556 -
    /etc/swift/container-server/3.conf)

    container-updater running (27557 -
    /etc/swift/container-server/4.conf)

    container-updater already started...

    account-auditor running (27560 - /etc/swift/account-server/3.conf)

    account-auditor running (27561 - /etc/swift/account-server/4.conf)

    account-auditor running (27558 - /etc/swift/account-server/1.conf)

    account-auditor running (27559 - /etc/swift/account-server/2.conf)

    account-auditor already started...

    object-replicator running (27562 - /etc/swift/object-server/1.conf)

    object-replicator running (27563 - /etc/swift/object-server/2.conf)

    object-replicator running (27564 - /etc/swift/object-server/3.conf)

    object-replicator running (27565 - /etc/swift/object-server/4.conf)

    object-replicator already started...

    container-sync running (27776 - /etc/swift/container-server/1.conf)

    container-sync running (27788 - /etc/swift/container-server/3.conf)

    container-sync running (27790 - /etc/swift/container-server/4.conf)

    container-sync running (27783 - /etc/swift/container-server/2.conf)

    container-sync already started...

    container-replicator running (27576 -
    /etc/swift/container-server/2.conf)

    container-replicator running (27569 -
    /etc/swift/container-server/1.conf)

    container-replicator running (27578 -
    /etc/swift/container-server/3.conf)

    container-replicator running (27580 -
    /etc/swift/container-server/4.conf)

    container-replicator already started...

    object-auditor running (27595 - /etc/swift/object-server/3.conf)

    object-auditor running (27597 - /etc/swift/object-server/4.conf)

    object-auditor running (27590 - /etc/swift/object-server/2.conf)

    object-auditor running (27583 - /etc/swift/object-server/1.conf)

    object-auditor already started...

    object-expirer running (27609 - /etc/swift/object-expirer.conf)

    object-expirer already started...

    container-auditor running (27616 -
    /etc/swift/container-server/4.conf)

    container-auditor running (27611 -
    /etc/swift/container-server/1.conf)

    container-auditor running (27613 -
    /etc/swift/container-server/2.conf)

    container-auditor running (27614 -
    /etc/swift/container-server/3.conf)

    container-auditor already started...

    container-server running (27618 -
    /etc/swift/container-server/1.conf)

    container-server running (27621 -
    /etc/swift/container-server/2.conf)

    container-server running (27638 -
    /etc/swift/container-server/4.conf)

    container-server running (27629 -
    /etc/swift/container-server/3.conf)

    container-server already started...

    object-reconstructor running (27656 -
    /etc/swift/object-server/3.conf)

    object-reconstructor running (27641 -
    /etc/swift/object-server/1.conf)

    object-reconstructor running (27651 -
    /etc/swift/object-server/2.conf)

    object-reconstructor running (27663 -
    /etc/swift/object-server/4.conf)

    object-reconstructor already started...

    object-server running (27761 - /etc/swift/object-server/2.conf)

    object-server running (27766 - /etc/swift/object-server/3.conf)

    object-server running (27758 - /etc/swift/object-server/1.conf)

    object-server running (27769 - /etc/swift/object-server/4.conf)

    object-server already started...

    account-reaper running (27696 - /etc/swift/account-server/1.conf)

    account-reaper running (27706 - /etc/swift/account-server/2.conf)

    account-reaper running (27718 - /etc/swift/account-server/4.conf)

    account-reaper running (27710 - /etc/swift/account-server/3.conf)

    account-reaper already started...

    Unable to locate config for proxy-server

    account-replicator running (27730 -
    /etc/swift/account-server/2.conf)

    account-replicator running (27740 -
    /etc/swift/account-server/4.conf)

    account-replicator running (27738 -
    /etc/swift/account-server/3.conf)

    account-replicator running (27726 -
    /etc/swift/account-server/1.conf)

    account-replicator already started...

    object-updater running (27746 - /etc/swift/object-server/2.conf)

    object-updater running (27754 - /etc/swift/object-server/4.conf)

    object-updater running (27749 - /etc/swift/object-server/3.conf)

    object-updater running (27742 - /etc/swift/object-server/1.conf)

    object-updater already started...

    container-reconciler running (27567 -
    /etc/swift/container-reconciler.conf)

    container-reconciler already started...

    account-server running (27691 - /etc/swift/account-server/4.conf)

    account-server running (27683 - /etc/swift/account-server/2.conf)

    account-server running (27685 - /etc/swift/account-server/3.conf)

    account-server running (27670 - /etc/swift/account-server/1.conf)

    account-server already started...

    swift@sha-mn-cluster-4:~$ **swift-init object all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    container-updater running (27554 -
    /etc/swift/container-server/1.conf)

    container-updater running (27555 -
    /etc/swift/container-server/2.conf)

    container-updater running (27556 -
    /etc/swift/container-server/3.conf)

    container-updater running (27557 -
    /etc/swift/container-server/4.conf)

    container-updater already started...

    account-auditor running (27560 - /etc/swift/account-server/3.conf)

    account-auditor running (27561 - /etc/swift/account-server/4.conf)

    account-auditor running (27558 - /etc/swift/account-server/1.conf)

    account-auditor running (27559 - /etc/swift/account-server/2.conf)

    account-auditor already started...

    object-replicator running (27562 - /etc/swift/object-server/1.conf)

    object-replicator running (27563 - /etc/swift/object-server/2.conf)

    object-replicator running (27564 - /etc/swift/object-server/3.conf)

    object-replicator running (27565 - /etc/swift/object-server/4.conf)

    object-replicator already started...

    container-sync running (27776 - /etc/swift/container-server/1.conf)

    container-sync running (27788 - /etc/swift/container-server/3.conf)

    container-sync running (27790 - /etc/swift/container-server/4.conf)

    container-sync running (27783 - /etc/swift/container-server/2.conf)

    container-sync already started...

    container-replicator running (27576 -
    /etc/swift/container-server/2.conf)

    container-replicator running (27569 -
    /etc/swift/container-server/1.conf)

    container-replicator running (27578 -
    /etc/swift/container-server/3.conf)

    container-replicator running (27580 -
    /etc/swift/container-server/4.conf)

    container-replicator already started...

    object-server running (27761 - /etc/swift/object-server/2.conf)

    object-server running (27766 - /etc/swift/object-server/3.conf)

    object-server running (27758 - /etc/swift/object-server/1.conf)

    object-server running (27769 - /etc/swift/object-server/4.conf)

    object-server already started...

    object-expirer running (27609 - /etc/swift/object-expirer.conf)

    object-expirer already started...

    container-auditor running (27616 -
    /etc/swift/container-server/4.conf)

    container-auditor running (27611 -
    /etc/swift/container-server/1.conf)

    container-auditor running (27613 -
    /etc/swift/container-server/2.conf)

    container-auditor running (27614 -
    /etc/swift/container-server/3.conf)

    container-auditor already started...

    container-server running (27618 -
    /etc/swift/container-server/1.conf)

    container-server running (27621 -
    /etc/swift/container-server/2.conf)

    container-server running (27638 -
    /etc/swift/container-server/4.conf)

    container-server running (27629 -
    /etc/swift/container-server/3.conf)

    container-server already started...

    object-reconstructor running (27656 -
    /etc/swift/object-server/3.conf)

    object-reconstructor running (27641 -
    /etc/swift/object-server/1.conf)

    object-reconstructor running (27651 -
    /etc/swift/object-server/2.conf)

    object-reconstructor running (27663 -
    /etc/swift/object-server/4.conf)

    object-reconstructor already started...

    object-auditor running (27595 - /etc/swift/object-server/3.conf)

    object-auditor running (27597 - /etc/swift/object-server/4.conf)

    object-auditor running (27590 - /etc/swift/object-server/2.conf)

    object-auditor running (27583 - /etc/swift/object-server/1.conf)

    object-auditor already started...

    account-reaper running (27696 - /etc/swift/account-server/1.conf)

    account-reaper running (27706 - /etc/swift/account-server/2.conf)

    account-reaper running (27718 - /etc/swift/account-server/4.conf)

    account-reaper running (27710 - /etc/swift/account-server/3.conf)

    account-reaper already started...

    Unable to locate config for proxy-server

    account-replicator running (27730 -
    /etc/swift/account-server/2.conf)

    account-replicator running (27740 -
    /etc/swift/account-server/4.conf)

    account-replicator running (27738 -
    /etc/swift/account-server/3.conf)

    account-replicator running (27726 -
    /etc/swift/account-server/1.conf)

    account-replicator already started...

    object-updater running (27746 - /etc/swift/object-server/2.conf)

    object-updater running (27754 - /etc/swift/object-server/4.conf)

    object-updater running (27749 - /etc/swift/object-server/3.conf)

    object-updater running (27742 - /etc/swift/object-server/1.conf)

    object-updater already started...

    container-reconciler running (27567 -
    /etc/swift/container-reconciler.conf)

    container-reconciler already started...

    account-server running (27691 - /etc/swift/account-server/4.conf)

    account-server running (27683 - /etc/swift/account-server/2.conf)

    account-server running (27685 - /etc/swift/account-server/3.conf)

    account-server running (27670 - /etc/swift/account-server/1.conf)

    account-server already started...

    swift@sha-mn-cluster-4:~$

1. Node 1 (proxy)- swift-init proxy all start

    swift@sha-mn-cluster-1:~$ **swift-init proxy all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    Unable to locate config for container-updater

    Unable to locate config for account-auditor

    Unable to locate config for object-replicator

    Unable to locate config for container-sync

    Unable to locate config for container-replicator

    Unable to locate config for object-auditor

    Unable to locate config for object-expirer

    Unable to locate config for container-auditor

    Unable to locate config for container-server

    Unable to locate config for object-reconstructor

    Unable to locate config for object-server

    Unable to locate config for account-reaper

    Starting proxy-server...(/etc/swift/proxy-server.conf)

    Unable to locate config for account-replicator

    Unable to locate config for object-updater

    Unable to locate config for container-reconciler

    Unable to locate config for account-server

    swift@sha-mn-cluster-1:~$

1. **Test - Cluster Working : Before upgrade**

    swift@sha-mn-cluster-1:~$ source openrc

    swift@sha-mn-cluster-1:~$ swift stat

    Account: AUTH\_test

    Containers: 0

    Objects: 0

    Bytes: 0

    X-Put-Timestamp: 1474039684.98724

    X-Timestamp: 1474039684.98724

    X-Trans-Id: tx2be9db6fd43445388a0ff-0057dc0f84

    Content-Type: text/plain; charset=utf-8

    swift@sha-mn-cluster-1:~$

    swift@sha-mn-cluster-1:~$ swift post test\_container1

    swift@sha-mn-cluster-1:~$ swift upload test\_container1
    randomFile1.txt

    randomFile1.txt

    swift@sha-mn-cluster-1:~$ swift upload test\_container1
    randomFile2.txt

    randomFile2.txt

    swift@sha-mn-cluster-1:~$ swift upload test\_container1
    randomFile3.txt

    randomFile3.txt

    swift@sha-mn-cluster-1:~$ swift stat

    Account: AUTH\_test

    Containers: 1

    Objects: 3

    Bytes: 639631360

    Containers in policy "gold": 1

    Objects in policy "gold": 3

    Bytes in policy "gold": 639631360

    X-Timestamp: 1474040151.66836

    X-Trans-Id: txc0709e7f02a64b87a20d4-0057dc12d3

    Content-Type: text/plain; charset=utf-8

    Accept-Ranges: bytes

    swift@sha-mn-cluster-1:~$

    **Node 2: **

    swift@sha-mn-cluster-2:/mnt$ find . -name "\*.data"

    ./sdb1/2/node/sdb2/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    ./sdb1/2/node/sdb2/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    swift@sha-mn-cluster-2:/mnt$ find . -name "\*.db"

    ./sdb1/2/node/sdb2/accounts/802/178/c8bcccab3ddbfdc34b08e9223f4f5178/c8bcccab3ddbfdc34b08e9223f4f5178.db

    ./sdb1/2/node/sdb2/containers/926/e06/e796f02ad045f424c7b921421b016e06/e796f02ad045f424c7b921421b016e06.db

    swift@sha-mn-cluster-2:/mnt$

    **Node 3: **

    swift@sha-mn-cluster-3:/mnt$ find . -name "\*.data"

    ./sdb1/3/node/sdb3/objects/310/879/4d81c811b5d605c2c5aae538d4ea0879/1474040287.93403.data

    ./sdb1/4/node/sdb4/objects/310/879/4d81c811b5d605c2c5aae538d4ea0879/1474040287.93403.data

    swift@sha-mn-cluster-3:/mnt$ find . -name "\*.db"

    ./sdb1/1/node/sdb1/accounts/802/178/c8bcccab3ddbfdc34b08e9223f4f5178/c8bcccab3ddbfdc34b08e9223f4f5178.db

    ./sdb1/3/node/sdb3/containers/926/e06/e796f02ad045f424c7b921421b016e06/e796f02ad045f424c7b921421b016e06.db

    swift@sha-mn-cluster-3:/mnt$

    **Node 4:**

    swift@sha-mn-cluster-4:/var/log/swift$ find /mnt/ -name "\*.data"

    /mnt/sdb1/1/node/sdb1/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    /mnt/sdb1/1/node/sdb1/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    /mnt/sdb1/2/node/sdb2/objects/310/879/4d81c811b5d605c2c5aae538d4ea0879/1474040287.93403.data

    /mnt/sdb1/4/node/sdb4/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    /mnt/sdb1/4/node/sdb4/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    swift@sha-mn-cluster-4:/var/log/swift$ find /mnt/ -name "\*.db"

    /mnt/sdb1/1/node/sdb1/containers/926/e06/e796f02ad045f424c7b921421b016e06/e796f02ad045f424c7b921421b016e06.db

    /mnt/sdb1/4/node/sdb4/accounts/802/178/c8bcccab3ddbfdc34b08e9223f4f5178/c8bcccab3ddbfdc34b08e9223f4f5178.db

    **Proxy node- Healthcheck- OK**

    swift@sha-mn-cluster-1:~/swift$ curl
    http://192.168.0.205:8080/healthcheck

    OKswift@sha-mn-cluster-1:~/swift$

    **From A/C/O node 3 : List Swift capabilities **

    swift@sha-mn-cluster-3:~/swift$ swift capabilities
    http://192.168.0.205:8080/

    Core: swift

    Options:

    account\_autocreate: True

    account\_listing\_limit: 10000

    allow\_account\_management: True

    container\_listing\_limit: 10000

    extra\_header\_count: 0

    max\_account\_name\_length: 256

    max\_container\_name\_length: 256

    max\_file\_size: 5368709122

    max\_header\_size: 8192

    max\_meta\_count: 90

    max\_meta\_name\_length: 128

    max\_meta\_overall\_size: 4096

    max\_meta\_value\_length: 256

    max\_object\_name\_length: 1024

    policies: [{u'default': True, u'name': u'gold', u'aliases':
    u'gold'}, {u'name': u'silver', u'aliases': u'silver'}, {u'name':
    u'ec42', u'aliases': u'ec42'}]

    strict\_cors\_mode: True

    version: 2.7.1.dev8

    Additional middleware: account\_quotas

    Additional middleware: bulk\_delete

    Options:

    max\_deletes\_per\_request: 10000

    max\_failed\_deletes: 1000

    Additional middleware: bulk\_upload

    Options:

    max\_containers\_per\_extraction: 10000

    max\_failed\_extractions: 1000

    Additional middleware: container\_quotas

    Additional middleware: container\_sync

    Options:

    realms: {}

    Additional middleware: crossdomain

    Additional middleware: ratelimit

    Options:

    account\_ratelimit: 0.0

    container\_listing\_ratelimits: []

    container\_ratelimits: []

    max\_sleep\_time\_seconds: 60.0

    Additional middleware: slo

    Options:

    max\_manifest\_segments: 1000

    max\_manifest\_size: 2097152

    min\_segment\_size: 1

    Additional middleware: staticweb

    Additional middleware: tempauth

    Options:

    account\_acls: True

    Additional middleware: tempurl

    Options:

    incoming\_allow\_headers: []

    incoming\_remove\_headers: [u'x-timestamp']

    methods: [u'GET', u'HEAD', u'PUT', u'POST', u'DELETE']

    outgoing\_allow\_headers: [u'x-object-meta-public-\*']

    outgoing\_remove\_headers: [u'x-object-meta-\*']

    Additional middleware: versioned\_writes

    swift@sha-mn-cluster-3:~/swift$

1. **Proxy node – remakerings-**

    swift-ring-builder object.builder create 10 3 1

    swift-ring-builder object.builder add r1z1-192.168.0.203:6010/sdb1 1

    swift-ring-builder object.builder add r1z2-192.168.0.203:6020/sdb2 1

    swift-ring-builder object.builder add r1z3-192.168.0.203:6030/sdb3 1

    swift-ring-builder object.builder add r1z4-192.168.0.203:6040/sdb4 1

    swift-ring-builder object.builder add r1z1-192.168.0.204:6010/sdb1 1

    swift-ring-builder object.builder add r1z2-192.168.0.204:6020/sdb2 1

    swift-ring-builder object.builder add r1z3-192.168.0.204:6030/sdb3 1

    swift-ring-builder object.builder add r1z4-192.168.0.204:6040/sdb4 1

    swift-ring-builder object.builder add r1z1-192.168.0.206:6010/sdb1 1

    swift-ring-builder object.builder add r1z2-192.168.0.206:6020/sdb2 1

    swift-ring-builder object.builder add r1z3-192.168.0.206:6030/sdb3 1

    swift-ring-builder object.builder add r1z4-192.168.0.206:6040/sdb4 1

    swift-ring-builder object.builder rebalance

    swift-ring-builder object-1.builder create 10 2 1

    swift-ring-builder object-1.builder add r1z1-192.168.0.203:6010/sdb1
    1

    swift-ring-builder object-1.builder add r1z2-192.168.0.203:6020/sdb2
    1

    swift-ring-builder object-1.builder add r1z3-192.168.0.203:6030/sdb3
    1

    swift-ring-builder object-1.builder add r1z4-192.168.0.203:6040/sdb4
    1

    swift-ring-builder object-1.builder add r1z1-192.168.0.204:6010/sdb1
    1

    swift-ring-builder object-1.builder add r1z2-192.168.0.204:6020/sdb2
    1

    swift-ring-builder object-1.builder add r1z3-192.168.0.204:6030/sdb3
    1

    swift-ring-builder object-1.builder add r1z4-192.168.0.204:6040/sdb4
    1

    swift-ring-builder object-1.builder add r1z1-192.168.0.206:6010/sdb1
    1

    swift-ring-builder object-1.builder add r1z2-192.168.0.206:6020/sdb2
    1

    swift-ring-builder object-1.builder add r1z3-192.168.0.206:6030/sdb3
    1

    swift-ring-builder object-1.builder add r1z4-192.168.0.206:6040/sdb4
    1

    swift-ring-builder object-1.builder rebalance

    swift-ring-builder object-2.builder create 10 6 1

    swift-ring-builder object-2.builder add r1z1-192.168.0.203:6010/sdb1
    1

    swift-ring-builder object-2.builder add r1z1-192.168.0.203:6010/sdb5
    1

    swift-ring-builder object-2.builder add r1z2-192.168.0.203:6020/sdb2
    1

    swift-ring-builder object-2.builder add r1z2-192.168.0.203:6020/sdb6
    1

    swift-ring-builder object-2.builder add r1z3-192.168.0.203:6030/sdb3
    1

    swift-ring-builder object-2.builder add r1z3-192.168.0.203:6030/sdb7
    1

    swift-ring-builder object-2.builder add r1z4-192.168.0.203:6040/sdb4
    1

    swift-ring-builder object-2.builder add r1z4-192.168.0.203:6040/sdb8
    1

    swift-ring-builder object-2.builder add r1z1-192.168.0.204:6010/sdb1
    1

    swift-ring-builder object-2.builder add r1z1-192.168.0.204:6010/sdb5
    1

    swift-ring-builder object-2.builder add r1z2-192.168.0.204:6020/sdb2
    1

    swift-ring-builder object-2.builder add r1z2-192.168.0.204:6020/sdb6
    1

    swift-ring-builder object-2.builder add r1z3-192.168.0.204:6030/sdb3
    1

    swift-ring-builder object-2.builder add r1z3-192.168.0.204:6030/sdb7
    1

    swift-ring-builder object-2.builder add r1z4-192.168.0.204:6040/sdb4
    1

    swift-ring-builder object-2.builder add r1z4-192.168.0.204:6040/sdb8
    1

    swift-ring-builder object-2.builder add r1z1-192.168.0.206:6010/sdb1
    1

    swift-ring-builder object-2.builder add r1z1-192.168.0.206:6010/sdb5
    1

    swift-ring-builder object-2.builder add r1z2-192.168.0.206:6020/sdb2
    1

    swift-ring-builder object-2.builder add r1z2-192.168.0.206:6020/sdb6
    1

    swift-ring-builder object-2.builder add r1z3-192.168.0.206:6030/sdb3
    1

    swift-ring-builder object-2.builder add r1z3-192.168.0.206:6030/sdb7
    1

    swift-ring-builder object-2.builder add r1z4-192.168.0.206:6040/sdb4
    1

    swift-ring-builder object-2.builder add r1z4-192.168.0.206:6040/sdb8
    1

    swift-ring-builder object-2.builder rebalance

    swift-ring-builder container.builder create 10 3 1

    swift-ring-builder container.builder add
    r1z1-192.168.0.203:6011/sdb1 1

    swift-ring-builder container.builder add
    r1z2-192.168.0.203:6021/sdb2 1

    swift-ring-builder container.builder add
    r1z3-192.168.0.203:6031/sdb3 1

    swift-ring-builder container.builder add
    r1z4-192.168.0.203:6041/sdb4 1

    swift-ring-builder container.builder add
    r1z1-192.168.0.204:6011/sdb1 1

    swift-ring-builder container.builder add
    r1z2-192.168.0.204:6021/sdb2 1

    swift-ring-builder container.builder add
    r1z3-192.168.0.204:6031/sdb3 1

    swift-ring-builder container.builder add
    r1z4-192.168.0.204:6041/sdb4 1

    swift-ring-builder container.builder add
    r1z1-192.168.0.206:6011/sdb1 1

    swift-ring-builder container.builder add
    r1z2-192.168.0.206:6021/sdb2 1

    swift-ring-builder container.builder add
    r1z3-192.168.0.206:6031/sdb3 1

    swift-ring-builder container.builder add
    r1z4-192.168.0.206:6041/sdb4 1

    swift-ring-builder container.builder rebalance

    swift-ring-builder account.builder create 10 3 1

    swift-ring-builder account.builder add r1z1-192.168.0.203:6012/sdb1
    1

    swift-ring-builder account.builder add r1z2-192.168.0.203:6022/sdb2
    1

    swift-ring-builder account.builder add r1z3-192.168.0.203:6032/sdb3
    1

    swift-ring-builder account.builder add r1z4-192.168.0.203:6042/sdb4
    1

    swift-ring-builder account.builder add r1z1-192.168.0.204:6012/sdb1
    1

    swift-ring-builder account.builder add r1z2-192.168.0.204:6022/sdb2
    1

    swift-ring-builder account.builder add r1z3-192.168.0.204:6032/sdb3
    1

    swift-ring-builder account.builder add r1z4-192.168.0.204:6042/sdb4
    1

    swift-ring-builder account.builder add r1z1-192.168.0.206:6012/sdb1
    1

    swift-ring-builder account.builder add r1z2-192.168.0.206:6022/sdb2
    1

    swift-ring-builder account.builder add r1z3-192.168.0.206:6032/sdb3
    1

    swift-ring-builder account.builder add r1z4-192.168.0.206:6042/sdb4
    1

    swift-ring-builder account.builder rebalance

    swift@sha-mn-cluster-1:~$

1. Upgrade Node 3 –A/C/O service

    swift@sha-mn-cluster-3:~/swift$ **git branch**

    **\* stable/mitaka**

    swift@sha-mn-cluster-3:~/swift$ **swift-init account all stop**

    Signal container-updater pid: 27874 signal: 15

    Signal container-updater pid: 27875 signal: 15

    Signal container-updater pid: 27876 signal: 15

    Signal container-updater pid: 27877 signal: 15

    Signal account-auditor pid: 27878 signal: 15

    Signal account-auditor pid: 27879 signal: 15

    Signal account-auditor pid: 27880 signal: 15

    Signal account-auditor pid: 27881 signal: 15

    Signal object-replicator pid: 27882 signal: 15

    Signal object-replicator pid: 27883 signal: 15

    Signal object-replicator pid: 27884 signal: 15

    Signal object-replicator pid: 27886 signal: 15

    Signal container-reconciler pid: 27891 signal: 15

    Signal container-replicator pid: 27898 signal: 15

    Signal container-replicator pid: 27899 signal: 15

    Signal container-replicator pid: 27901 signal: 15

    Signal container-replicator pid: 27902 signal: 15

    Signal object-auditor pid: 27909 signal: 15

    Signal object-auditor pid: 27913 signal: 15

    Signal object-auditor pid: 27920 signal: 15

    Signal object-auditor pid: 27925 signal: 15

    Signal object-expirer pid: 27931 signal: 15

    Signal container-auditor pid: 27934 signal: 15

    Signal container-auditor pid: 27938 signal: 15

    Signal container-auditor pid: 27943 signal: 15

    Signal container-auditor pid: 27947 signal: 15

    Signal container-server pid: 27953 signal: 15

    Signal container-server pid: 27959 signal: 15

    Signal container-server pid: 27966 signal: 15

    Signal container-server pid: 27971 signal: 15

    Signal object-reconstructor pid: 27975 signal: 15

    Signal object-reconstructor pid: 27980 signal: 15

    Signal object-reconstructor pid: 27983 signal: 15

    Signal object-reconstructor pid: 27988 signal: 15

    Signal account-server pid: 27995 signal: 15

    Signal account-server pid: 27998 signal: 15

    Signal account-server pid: 28006 signal: 15

    Signal account-server pid: 28014 signal: 15

    Signal account-reaper pid: 28018 signal: 15

    Signal account-reaper pid: 28028 signal: 15

    Signal account-reaper pid: 28030 signal: 15

    Signal account-reaper pid: 28032 signal: 15

    No proxy-server running

    Signal account-replicator pid: 28036 signal: 15

    Signal account-replicator pid: 28042 signal: 15

    Signal account-replicator pid: 28050 signal: 15

    Signal account-replicator pid: 28056 signal: 15

    Signal object-updater pid: 28065 signal: 15

    Signal object-updater pid: 28069 signal: 15

    Signal object-updater pid: 28077 signal: 15

    Signal object-updater pid: 28081 signal: 15

    Signal object-server pid: 28090 signal: 15

    Signal object-server pid: 28100 signal: 15

    Signal object-server pid: 28104 signal: 15

    Signal object-server pid: 28106 signal: 15

    Signal container-sync pid: 28110 signal: 15

    Signal container-sync pid: 28116 signal: 15

    Signal container-sync pid: 28123 signal: 15

    Signal container-sync pid: 28128 signal: 15

    object-replicator (27883) appears to have stopped

    container-auditor (27943) appears to have stopped

    account-replicator (28042) appears to have stopped

    account-replicator (28050) appears to have stopped

    container-sync (28123) appears to have stopped

    container-sync (28116) appears to have stopped

    container-sync (28110) appears to have stopped

    container-updater (27874) appears to have stopped

    container-updater (27877) appears to have stopped

    account-auditor (27880) appears to have stopped

    account-auditor (27881) appears to have stopped

    container-replicator (27902) appears to have stopped

    object-auditor (27913) appears to have stopped

    account-reaper (28030) appears to have stopped

    object-reconstructor (27988) appears to have stopped

    object-reconstructor (27983) appears to have stopped

    object-reconstructor (27975) appears to have stopped

    object-replicator (27882) appears to have stopped

    object-replicator (27884) appears to have stopped

    object-replicator (27886) appears to have stopped

    object-expirer (27931) appears to have stopped

    object-server (28104) appears to have stopped

    object-server (28090) appears to have stopped

    object-server (28100) appears to have stopped

    object-server (28106) appears to have stopped

    container-auditor (27938) appears to have stopped

    container-auditor (27947) appears to have stopped

    container-auditor (27934) appears to have stopped

    account-replicator (28056) appears to have stopped

    account-replicator (28036) appears to have stopped

    container-sync (28128) appears to have stopped

    object-updater (28065) appears to have stopped

    object-updater (28069) appears to have stopped

    object-updater (28081) appears to have stopped

    object-updater (28077) appears to have stopped

    container-updater (27875) appears to have stopped

    container-updater (27876) appears to have stopped

    account-auditor (27878) appears to have stopped

    account-auditor (27879) appears to have stopped

    container-replicator (27898) appears to have stopped

    container-replicator (27899) appears to have stopped

    container-replicator (27901) appears to have stopped

    object-auditor (27920) appears to have stopped

    object-auditor (27909) appears to have stopped

    object-auditor (27925) appears to have stopped

    account-reaper (28032) appears to have stopped

    account-reaper (28018) appears to have stopped

    account-reaper (28028) appears to have stopped

    container-server (27953) appears to have stopped

    container-server (27971) appears to have stopped

    container-server (27966) appears to have stopped

    container-server (27959) appears to have stopped

    account-server (28014) appears to have stopped

    account-server (27995) appears to have stopped

    account-server (28006) appears to have stopped

    account-server (27998) appears to have stopped

    container-reconciler (27891) appears to have stopped

    object-reconstructor (27980) appears to have stopped

    swift@sha-mn-cluster-3:~/swift$ **swift-init container all stop**

    No container-updater running

    No account-auditor running

    No object-replicator running

    No container-sync running

    No container-replicator running

    No object-auditor running

    No object-expirer running

    No container-auditor running

    No container-server running

    No object-reconstructor running

    No object-server running

    No account-reaper running

    No proxy-server running

    No account-replicator running

    No object-updater running

    No container-reconciler running

    No account-server running

    swift@sha-mn-cluster-3:~/swift$ **swift-init object all stop**

    No container-updater running

    No account-auditor running

    No object-replicator running

    No container-sync running

    No container-replicator running

    No object-server running

    No object-expirer running

    No container-auditor running

    No container-server running

    No object-reconstructor running

    No object-auditor running

    No account-reaper running

    No proxy-server running

    No account-replicator running

    No object-updater running

    No container-reconciler running

    No account-server running

    swift@sha-mn-cluster-3:~/swift$

    swift@sha-mn-cluster-3:~/swift$ git branch

    \* stable/mitaka

    swift@sha-mn-cluster-3:~/swift$ **git remote update**

    Fetching origin

    remote: Counting objects: 494, done.

    remote: Compressing objects: 100% (36/36), done.

    remote: Total 494 (delta 275), reused 264 (delta 264), pack-reused
    194

    Receiving objects: 100% (494/494), 204.38 KiB \| 0 bytes/s, done.

    Resolving deltas: 100% (371/371), completed with 170 local objects.

    From https://github.com/openstack/swift

    1ab2a29..e8c0173 feature/hummingbird -> origin/feature/hummingbird

    ac81ccd..acb8971 master -> origin/master

    swift@sha-mn-cluster-3:~/swift$ git branch

    \* stable/mitaka

    swift@sha-mn-cluster-3:~/swift$

    swift@sha-mn-cluster-3:~/swift$ **git tag -l**

    1.0.0

    1.0.0-1

    1.0.1

    1.0.2

    1.10.0

    1.10.0.rc1

    1.11.0

    1.12.0

    1.13.0

    1.13.1

    1.13.1.rc1

    1.13.1.rc2

    1.3.0

    1.3gamma1

    1.3rc1

    1.4.0

    1.4.1

    1.4.2

    1.4.3

    1.4.4

    1.4.5

    1.4.6

    1.4.7

    1.4.8

    1.5.0

    1.6.0

    1.7.0

    1.7.2

    1.7.4

    1.7.5

    1.7.6

    1.8.0

    1.8.0.rc1

    1.8.0.rc2

    1.9.0

    1.9.1

    1.9.2

    2.0.0

    2.0.0.rc1

    2.0.0.rc2

    2.1.0

    2.1.0.rc1

    2.2.0

    2.2.0.rc1

    2.2.1

    2.2.1.rc1

    2.2.1c1

    2.2.2

    2.2.2rc1

    2.3.0

    2.3.0rc1

    2.3.0rc2

    2.4.0

    2.5.0

    2.6.0

    2.7.0

    2.8.0

    2.9.0

    diablo-eol

    erasure\_code\_dev\_history

    essex-eol

    folsom-eol

    grizzly-eol

    havana-eol

    icehouse-eol

    juno-eol

    kilo-eol

    storage-policy-historical

    upstream-1.0.0

    swift@sha-mn-cluster-3:~/swift$

    swift@sha-mn-cluster-3:~/swift$ git branch

    \* stable/mitaka

    swift@sha-mn-cluster-3:~/swift$ git checkout 2.9.0

    Note: checking out '2.9.0'.

    You are in 'detached HEAD' state. You can look around, make
    experimental

    changes and commit them, and you can discard any commits you make in
    this

    state without impacting any branches by performing another checkout.

    If you want to create a new branch to retain commits you create, you
    may

    do so (now or later) by using -b with the checkout command again.
    Example:

    git checkout -b new\_branch\_name

    HEAD is now at 8823810... authors and changelog updates for 2.9.0
    release

    swift@sha-mn-cluster-3:~/swift$ git branch

    **\* (detached from 2.9.0)**

    stable/mitaka

    swift@sha-mn-cluster-3:~/swift$

    swift@sha-mn-cluster-3:~/swift$ **sudo pip install -r
    requirements.txt --upgrade**

    sudo: unable to resolve host sha-mn-cluster-3

    The directory '/home/swift/.cache/pip/http' or its parent directory
    is not owned by the current user and the cache has been disabled.
    Please check the permissions and owner of that directory. If
    executing pip with sudo, you may want sudo's -H flag.

    The directory '/home/swift/.cache/pip' or its parent directory is
    not owned by the current user and caching wheels has been disabled.
    check the permissions and owner of that directory. If executing pip
    with sudo, you may want sudo's -H flag.

    Ignoring dnspython3: markers u"python\_version>='3.0'" don't match
    your environment

    /usr/local/lib/python2.7/dist-packages/pip/\_vendor/requests/packages/urllib3/util/ssl\_.py:318:
    SNIMissingWarning: An HTTPS request has been made, but the SNI
    (Subject Name Indication) extension to TLS is not available on this
    platform. This may cause the server to present an incorrect TLS
    certificate, which can cause validation failures. You can upgrade to
    a newer version of Python to solve this. For more information, see
    https://urllib3.readthedocs.org/en/latest/security.html#snimissingwarning.

    SNIMissingWarning

    /usr/local/lib/python2.7/dist-packages/pip/\_vendor/requests/packages/urllib3/util/ssl\_.py:122:
    InsecurePlatformWarning: A true SSLContext object is not available.
    This prevents urllib3 from configuring SSL appropriately and may
    cause certain SSL connections to fail. You can upgrade to a newer
    version of Python to solve this. For more information, see
    https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.

    InsecurePlatformWarning

    Requirement already up-to-date: dnspython>=1.12.0 in
    /usr/local/lib/python2.7/dist-packages (from -r requirements.txt
    (line 5))

    Requirement already up-to-date: eventlet>=0.17.4 in
    /usr/local/lib/python2.7/dist-packages (from -r requirements.txt
    (line 7))

    Collecting greenlet>=0.3.1 (from -r requirements.txt (line 8))

    Downloading greenlet-0.4.10-cp27-cp27mu-manylinux1\_x86\_64.whl
    (41kB)

    100% \|████████████████████████████████\| 51kB 11.6MB/s

    Requirement already up-to-date: netifaces!=0.10.0,!=0.10.1,>=0.5 in
    /usr/local/lib/python2.7/dist-packages (from -r requirements.txt
    (line 9))

    Requirement already up-to-date: pastedeploy>=1.3.3 in
    /usr/lib/python2.7/dist-packages (from -r requirements.txt (line
    10))

    Requirement already up-to-date: six>=1.9.0 in
    /usr/local/lib/python2.7/dist-packages (from -r requirements.txt
    (line 11))

    Collecting xattr>=0.4 (from -r requirements.txt (line 12))

    Downloading xattr-0.8.0.tar.gz

    Requirement already up-to-date: PyECLib>=1.2.0 in
    /usr/local/lib/python2.7/dist-packages (from -r requirements.txt
    (line 13))

    Collecting cryptography!=1.3.0,>=1.0 (from -r requirements.txt (line
    14))

    Downloading cryptography-1.5.tar.gz (400kB)

    100% \|████████████████████████████████\| 409kB 3.2MB/s

    Collecting cffi>=0.4 (from xattr>=0.4->-r requirements.txt (line
    12))

    Downloading cffi-1.8.2-cp27-cp27mu-manylinux1\_x86\_64.whl (385kB)

    100% \|████████████████████████████████\| 389kB 3.4MB/s

    Collecting idna>=2.0 (from cryptography!=1.3.0,>=1.0->-r
    requirements.txt (line 14))

    Downloading idna-2.1-py2.py3-none-any.whl (54kB)

    100% \|████████████████████████████████\| 61kB 12.4MB/s

    Collecting pyasn1>=0.1.8 (from cryptography!=1.3.0,>=1.0->-r
    requirements.txt (line 14))

    Downloading pyasn1-0.1.9-py2.py3-none-any.whl

    Collecting setuptools>=11.3 (from cryptography!=1.3.0,>=1.0->-r
    requirements.txt (line 14))

    Downloading setuptools-27.2.0-py2.py3-none-any.whl (465kB)

    100% \|████████████████████████████████\| 471kB 3.1MB/s

    Collecting enum34 (from cryptography!=1.3.0,>=1.0->-r
    requirements.txt (line 14))

    Downloading enum34-1.1.6-py2-none-any.whl

    Collecting ipaddress (from cryptography!=1.3.0,>=1.0->-r
    requirements.txt (line 14))

    Downloading ipaddress-1.0.17-py2-none-any.whl

    Collecting pycparser (from cffi>=0.4->xattr>=0.4->-r
    requirements.txt (line 12))

    Downloading pycparser-2.14.tar.gz (223kB)

    100% \|████████████████████████████████\| 225kB 5.9MB/s

    Installing collected packages: greenlet, pycparser, cffi, xattr,
    idna, pyasn1, setuptools, enum34, ipaddress, cryptography

    Found existing installation: greenlet 0.4.2

    DEPRECATION: Uninstalling a distutils installed project (greenlet)
    has been deprecated and will be removed in a future version. This is
    due to the fact that uninstalling a distutils project will only
    partially uninstall the project.

    Uninstalling greenlet-0.4.2:

    Successfully uninstalled greenlet-0.4.2

    Running setup.py install for pycparser ... done

    Found existing installation: xattr 0.6.4

    Uninstalling xattr-0.6.4:

    Successfully uninstalled xattr-0.6.4

    Running setup.py install for xattr ... done

    Found existing installation: setuptools 26.1.1

    Uninstalling setuptools-26.1.1:

    Successfully uninstalled setuptools-26.1.1

    Running setup.py install for cryptography ... done

    Successfully installed cffi-1.8.2 cryptography-1.5 enum34-1.1.6
    greenlet-0.4.10 idna-2.1 ipaddress-1.0.17 pyasn1-0.1.9
    pycparser-2.14 setuptools-27.2.0 xattr-0.8.0

    /usr/local/lib/python2.7/dist-packages/pip/\_vendor/requests/packages/urllib3/util/ssl\_.py:122:
    InsecurePlatformWarning: A true SSLContext object is not available.
    This prevents urllib3 from configuring SSL appropriately and may
    cause certain SSL connections to fail. You can upgrade to a newer
    version of Python to solve this. For more information, see
    https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.

    InsecurePlatformWarning

    swift@sha-mn-cluster-3:~/swift$

    swift@sha-mn-cluster-3:~/swift$ **sudo pip install -r
    test-requirements.txt --upgrade**

    sudo: unable to resolve host sha-mn-cluster-3

    The directory '/home/swift/.cache/pip/http' or its parent directory
    is not owned by the current user and the cache has been disabled.
    Please check the permissions and owner of that directory. If
    executing pip with sudo, you may want sudo's -H flag.

    The directory '/home/swift/.cache/pip' or its parent directory is
    not owned by the current user and caching wheels has been disabled.
    check the permissions and owner of that directory. If executing pip
    with sudo, you may want sudo's -H flag.

    /usr/local/lib/python2.7/dist-packages/pip/\_vendor/requests/packages/urllib3/util/ssl\_.py:318:
    SNIMissingWarning: An HTTPS request has been made, but the SNI
    (Subject Name Indication) extension to TLS is not available on this
    platform. This may cause the server to present an incorrect TLS
    certificate, which can cause validation failures. You can upgrade to
    a newer version of Python to solve this. For more information, see
    https://urllib3.readthedocs.org/en/latest/security.html#snimissingwarning.

    SNIMissingWarning

    /usr/local/lib/python2.7/dist-packages/pip/\_vendor/requests/packages/urllib3/util/ssl\_.py:122:
    InsecurePlatformWarning: A true SSLContext object is not available.
    This prevents urllib3 from configuring SSL appropriately and may
    cause certain SSL connections to fail. You can upgrade to a newer
    version of Python to solve this. For more information, see
    https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.

    InsecurePlatformWarning

    Requirement already up-to-date: hacking<0.11,>=0.10.0 in
    /usr/local/lib/python2.7/dist-packages (from -r
    test-requirements.txt (line 6))

    Collecting coverage (from -r test-requirements.txt (line 7))

    Downloading coverage-4.2.tar.gz (359kB)

    100% \|████████████████████████████████\| 368kB 3.8MB/s

    Collecting nose (from -r test-requirements.txt (line 8))

    Downloading nose-1.3.7-py2-none-any.whl (154kB)

    100% \|████████████████████████████████\| 163kB 7.7MB/s

    Requirement already up-to-date: nosexcover in
    /usr/local/lib/python2.7/dist-packages (from -r
    test-requirements.txt (line 9))

    Requirement already up-to-date: nosehtmloutput in
    /usr/local/lib/python2.7/dist-packages (from -r
    test-requirements.txt (line 10))

    Collecting openstackdocstheme>=1.0.3 (from -r test-requirements.txt
    (line 11))

    Downloading openstackdocstheme-1.5.0-py2.py3-none-any.whl (203kB)

    100% \|████████████████████████████████\| 204kB 6.5MB/s

    Requirement already up-to-date: oslosphinx in
    /usr/local/lib/python2.7/dist-packages (from -r
    test-requirements.txt (line 12))

    Collecting sphinx!=1.2.0,!=1.3b1,<1.3,>=1.1.2 (from -r
    test-requirements.txt (line 13))

    Downloading Sphinx-1.2.3-py2-none-any.whl (1.9MB)

    100% \|████████████████████████████████\| 1.9MB 832kB/s

    Collecting os-api-ref>=0.1.0 (from -r test-requirements.txt (line
    14))

    Downloading os\_api\_ref-1.0.0-py2-none-any.whl (78kB)

    100% \|████████████████████████████████\| 81kB 11.9MB/s

    Requirement already up-to-date: os-testr>=0.4.1 in
    /usr/local/lib/python2.7/dist-packages (from -r
    test-requirements.txt (line 15))

    Collecting mock>=1.0 (from -r test-requirements.txt (line 16))

    Downloading mock-2.0.0-py2.py3-none-any.whl (56kB)

    100% \|████████████████████████████████\| 61kB 11.1MB/s

    Collecting python-swiftclient (from -r test-requirements.txt (line
    17))

    Downloading python\_swiftclient-3.1.0-py2.py3-none-any.whl (67kB)

    100% \|████████████████████████████████\| 71kB 11.4MB/s

    Requirement already up-to-date: python-keystoneclient>=1.3.0 in
    /usr/local/lib/python2.7/dist-packages (from -r
    test-requirements.txt (line 18))

    Requirement already up-to-date: bandit>=0.10.1 in
    /usr/local/lib/python2.7/dist-packages (from -r
    test-requirements.txt (line 21))

    Requirement already up-to-date: six>=1.7.0 in
    /usr/local/lib/python2.7/dist-packages (from
    hacking<0.11,>=0.10.0->-r test-requirements.txt (line 6))

    Requirement already up-to-date: pbr<2.0,>=0.11 in
    /usr/local/lib/python2.7/dist-packages (from
    hacking<0.11,>=0.10.0->-r test-requirements.txt (line 6))

    Requirement already up-to-date: pyflakes==0.8.1 in
    /usr/local/lib/python2.7/dist-packages (from
    hacking<0.11,>=0.10.0->-r test-requirements.txt (line 6))

    Requirement already up-to-date: flake8==2.2.4 in
    /usr/local/lib/python2.7/dist-packages (from
    hacking<0.11,>=0.10.0->-r test-requirements.txt (line 6))

    Requirement already up-to-date: mccabe==0.2.1 in
    /usr/local/lib/python2.7/dist-packages (from
    hacking<0.11,>=0.10.0->-r test-requirements.txt (line 6))

    Requirement already up-to-date: pep8==1.5.7 in
    /usr/local/lib/python2.7/dist-packages (from
    hacking<0.11,>=0.10.0->-r test-requirements.txt (line 6))

    Requirement already up-to-date: requests>=2.10.0 in
    /usr/local/lib/python2.7/dist-packages (from
    openstackdocstheme>=1.0.3->-r test-requirements.txt (line 11))

    Requirement already up-to-date: docutils>=0.7 in
    /usr/local/lib/python2.7/dist-packages (from
    sphinx!=1.2.0,!=1.3b1,<1.3,>=1.1.2->-r test-requirements.txt (line
    13))

    Requirement already up-to-date: Pygments>=1.2 in
    /usr/local/lib/python2.7/dist-packages (from
    sphinx!=1.2.0,!=1.3b1,<1.3,>=1.1.2->-r test-requirements.txt (line
    13))

    Requirement already up-to-date: Jinja2>=2.3 in
    /usr/local/lib/python2.7/dist-packages (from
    sphinx!=1.2.0,!=1.3b1,<1.3,>=1.1.2->-r test-requirements.txt (line
    13))

    Collecting PyYAML>=3.1.0 (from os-api-ref>=0.1.0->-r
    test-requirements.txt (line 14))

    Downloading PyYAML-3.12.tar.gz (253kB)

    100% \|████████████████████████████████\| 256kB 6.0MB/s

    Requirement already up-to-date: python-subunit>=0.0.18 in
    /usr/local/lib/python2.7/dist-packages (from os-testr>=0.4.1->-r
    test-requirements.txt (line 15))

    Requirement already up-to-date: Babel>=2.3.4 in
    /usr/local/lib/python2.7/dist-packages (from os-testr>=0.4.1->-r
    test-requirements.txt (line 15))

    Requirement already up-to-date: testtools>=1.4.0 in
    /usr/local/lib/python2.7/dist-packages (from os-testr>=0.4.1->-r
    test-requirements.txt (line 15))

    Requirement already up-to-date: testrepository>=0.0.18 in
    /usr/local/lib/python2.7/dist-packages (from os-testr>=0.4.1->-r
    test-requirements.txt (line 15))

    Requirement already up-to-date: funcsigs>=1; python\_version < "3.3"
    in /usr/local/lib/python2.7/dist-packages (from mock>=1.0->-r
    test-requirements.txt (line 16))

    Requirement already up-to-date: futures>=2.1.3 in
    /usr/local/lib/python2.7/dist-packages/futures-3.0.5-py2.7.egg (from
    python-swiftclient->-r test-requirements.txt (line 17))

    Requirement already up-to-date: oslo.utils>=3.16.0 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: oslo.i18n>=2.1.0 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: oslo.config>=3.14.0 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: oslo.serialization>=1.10.0 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: keystoneauth1>=2.10.0 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: stevedore>=1.16.0 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: debtcollector>=1.2.0 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: positional>=1.0.1 in
    /usr/local/lib/python2.7/dist-packages (from
    python-keystoneclient>=1.3.0->-r test-requirements.txt (line 18))

    Requirement already up-to-date: GitPython>=1.0.1 in
    /usr/local/lib/python2.7/dist-packages (from bandit>=0.10.1->-r
    test-requirements.txt (line 21))

    Requirement already up-to-date: MarkupSafe in
    /usr/local/lib/python2.7/dist-packages (from
    Jinja2>=2.3->sphinx!=1.2.0,!=1.3b1,<1.3,>=1.1.2->-r
    test-requirements.txt (line 13))

    Requirement already up-to-date: extras in
    /usr/local/lib/python2.7/dist-packages (from
    python-subunit>=0.0.18->os-testr>=0.4.1->-r test-requirements.txt
    (line 15))

    Requirement already up-to-date: pytz>=0a in
    /usr/local/lib/python2.7/dist-packages (from
    Babel>=2.3.4->os-testr>=0.4.1->-r test-requirements.txt (line 15))

    Requirement already up-to-date: traceback2 in
    /usr/local/lib/python2.7/dist-packages (from
    testtools>=1.4.0->os-testr>=0.4.1->-r test-requirements.txt (line
    15))

    Requirement already up-to-date: python-mimeparse in
    /usr/local/lib/python2.7/dist-packages (from
    testtools>=1.4.0->os-testr>=0.4.1->-r test-requirements.txt (line
    15))

    Requirement already up-to-date: fixtures>=1.3.0 in
    /usr/local/lib/python2.7/dist-packages (from
    testtools>=1.4.0->os-testr>=0.4.1->-r test-requirements.txt (line
    15))

    Requirement already up-to-date: unittest2>=1.0.0 in
    /usr/local/lib/python2.7/dist-packages (from
    testtools>=1.4.0->os-testr>=0.4.1->-r test-requirements.txt (line
    15))

    Requirement already up-to-date: monotonic>=0.6 in
    /usr/local/lib/python2.7/dist-packages (from
    oslo.utils>=3.16.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Requirement already up-to-date: iso8601>=0.1.11 in
    /usr/local/lib/python2.7/dist-packages (from
    oslo.utils>=3.16.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Collecting pyparsing>=2.0.1 (from
    oslo.utils>=3.16.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Downloading pyparsing-2.1.9-py2.py3-none-any.whl (55kB)

    100% \|████████████████████████████████\| 61kB 11.6MB/s

    Requirement already up-to-date: netifaces>=0.10.4 in
    /usr/local/lib/python2.7/dist-packages (from
    oslo.utils>=3.16.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Requirement already up-to-date: netaddr!=0.7.16,>=0.7.12 in
    /usr/local/lib/python2.7/dist-packages (from
    oslo.utils>=3.16.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Requirement already up-to-date: rfc3986>=0.2.0 in
    /usr/local/lib/python2.7/dist-packages (from
    oslo.config>=3.14.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Requirement already up-to-date: msgpack-python>=0.4.0 in
    /usr/local/lib/python2.7/dist-packages (from
    oslo.serialization>=1.10.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Requirement already up-to-date: wrapt>=1.7.0 in
    /usr/local/lib/python2.7/dist-packages (from
    debtcollector>=1.2.0->python-keystoneclient>=1.3.0->-r
    test-requirements.txt (line 18))

    Requirement already up-to-date: gitdb>=0.6.4 in
    /usr/local/lib/python2.7/dist-packages (from
    GitPython>=1.0.1->bandit>=0.10.1->-r test-requirements.txt (line
    21))

    Requirement already up-to-date: linecache2 in
    /usr/local/lib/python2.7/dist-packages (from
    traceback2->testtools>=1.4.0->os-testr>=0.4.1->-r
    test-requirements.txt (line 15))

    Collecting argparse (from
    unittest2>=1.0.0->testtools>=1.4.0->os-testr>=0.4.1->-r
    test-requirements.txt (line 15))

    Downloading argparse-1.4.0-py2.py3-none-any.whl

    Requirement already up-to-date: smmap>=0.8.5 in
    /usr/local/lib/python2.7/dist-packages (from
    gitdb>=0.6.4->GitPython>=1.0.1->bandit>=0.10.1->-r
    test-requirements.txt (line 21))

    Installing collected packages: coverage, nose, openstackdocstheme,
    sphinx, PyYAML, os-api-ref, mock, python-swiftclient, pyparsing,
    argparse

    Found existing installation: coverage 3.7.1

    Uninstalling coverage-3.7.1:

    Successfully uninstalled coverage-3.7.1

    Running setup.py install for coverage ... done

    Found existing installation: nose 1.3.1

    Uninstalling nose-1.3.1:

    Successfully uninstalled nose-1.3.1

    Found existing installation: Sphinx 1.1.3

    Uninstalling Sphinx-1.1.3:

    Successfully uninstalled Sphinx-1.1.3

    Found existing installation: PyYAML 3.10

    DEPRECATION: Uninstalling a distutils installed project (PyYAML) has
    been deprecated and will be removed in a future version. This is due
    to the fact that uninstalling a distutils project will only
    partially uninstall the project.

    Uninstalling PyYAML-3.10:

    Successfully uninstalled PyYAML-3.10

    Running setup.py install for PyYAML ... done

    Found existing installation: mock 1.0.1

    Uninstalling mock-1.0.1:

    Successfully uninstalled mock-1.0.1

    Found existing installation: python-swiftclient 3.0.1.dev1

    Can't uninstall 'python-swiftclient'. No files were found to
    uninstall.

    Found existing installation: pyparsing 2.1.8

    Uninstalling pyparsing-2.1.8:

    Successfully uninstalled pyparsing-2.1.8

    Found existing installation: argparse 1.2.1

    Not uninstalling argparse at /usr/lib/python2.7, as it is in the
    standard library.

    Successfully installed PyYAML-3.12 argparse-1.2.1 coverage-4.2
    mock-2.0.0 nose-1.3.7 openstackdocstheme-1.5.0 os-api-ref-1.0.0
    pyparsing-2.1.9 python-swiftclient-3.1.0 sphinx-1.2.3

    /usr/local/lib/python2.7/dist-packages/pip/\_vendor/requests/packages/urllib3/util/ssl\_.py:122:
    InsecurePlatformWarning: A true SSLContext object is not available.
    This prevents urllib3 from configuring SSL appropriately and may
    cause certain SSL connections to fail. You can upgrade to a newer
    version of Python to solve this. For more information, see
    https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.

    InsecurePlatformWarning

    swift@sha-mn-cluster-3:~/swift$

    swift@sha-mn-cluster-3:~/swift$ **sudo python setup.py develop**

    sudo: unable to resolve host sha-mn-cluster-3

    running develop

    running egg\_info

    writing pbr to swift.egg-info/pbr.json

    writing requirements to swift.egg-info/requires.txt

    writing swift.egg-info/PKG-INFO

    writing top-level names to swift.egg-info/top\_level.txt

    writing dependency\_links to swift.egg-info/dependency\_links.txt

    writing entry points to swift.egg-info/entry\_points.txt

    [pbr] Processing SOURCES.txt

    [pbr] In git context, generating filelist from git

    warning: no files found matching 'ChangeLog'

    warning: no previously-included files matching '\*.pyc' found
    anywhere in distribution

    reading manifest template 'MANIFEST.in'

    writing manifest file 'swift.egg-info/SOURCES.txt'

    running build\_ext

    Creating /usr/local/lib/python2.7/dist-packages/swift.egg-link (link
    to .)

    swift 2.9.0 is already the active version in easy-install.pth

    Installing swift-account-audit script to /usr/local/bin

    Installing swift-account-auditor script to /usr/local/bin

    Installing swift-account-info script to /usr/local/bin

    Installing swift-account-reaper script to /usr/local/bin

    Installing swift-account-replicator script to /usr/local/bin

    Installing swift-account-server script to /usr/local/bin

    Installing swift-config script to /usr/local/bin

    Installing swift-container-auditor script to /usr/local/bin

    Installing swift-container-info script to /usr/local/bin

    Installing swift-container-replicator script to /usr/local/bin

    Installing swift-container-server script to /usr/local/bin

    Installing swift-container-sync script to /usr/local/bin

    Installing swift-container-updater script to /usr/local/bin

    Installing swift-container-reconciler script to /usr/local/bin

    Installing swift-reconciler-enqueue script to /usr/local/bin

    Installing swift-dispersion-populate script to /usr/local/bin

    Installing swift-dispersion-report script to /usr/local/bin

    Installing swift-drive-audit script to /usr/local/bin

    Installing swift-form-signature script to /usr/local/bin

    Installing swift-get-nodes script to /usr/local/bin

    Installing swift-init script to /usr/local/bin

    Installing swift-object-auditor script to /usr/local/bin

    Installing swift-object-expirer script to /usr/local/bin

    Installing swift-object-info script to /usr/local/bin

    Installing swift-object-replicator script to /usr/local/bin

    Installing swift-object-reconstructor script to /usr/local/bin

    Installing swift-object-server script to /usr/local/bin

    Installing swift-object-updater script to /usr/local/bin

    Installing swift-oldies script to /usr/local/bin

    Installing swift-orphans script to /usr/local/bin

    Installing swift-proxy-server script to /usr/local/bin

    Installing swift-recon script to /usr/local/bin

    Installing swift-recon-cron script to /usr/local/bin

    Installing swift-ring-builder script to /usr/local/bin

    Installing swift-ring-builder-analyzer script to /usr/local/bin

    Installing swift-temp-url script to /usr/local/bin

    Installed /home/swift/swift

    Processing dependencies for swift==2.9.0

    Searching for cryptography==1.5

    Best match: cryptography 1.5

    Adding cryptography 1.5 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for PyECLib==1.2.0

    Best match: PyECLib 1.2.0

    Adding PyECLib 1.2.0 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for xattr==0.8.0

    Best match: xattr 0.8.0

    Adding xattr 0.8.0 to easy-install.pth file

    Installing xattr script to /usr/local/bin

    Using /usr/local/lib/python2.7/dist-packages

    Searching for six==1.10.0

    Best match: six 1.10.0

    Adding six 1.10.0 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for PasteDeploy==1.5.2

    Best match: PasteDeploy 1.5.2

    Adding PasteDeploy 1.5.2 to easy-install.pth file

    Using /usr/lib/python2.7/dist-packages

    Searching for netifaces==0.10.5

    Best match: netifaces 0.10.5

    Adding netifaces 0.10.5 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for greenlet==0.4.10

    Best match: greenlet 0.4.10

    Adding greenlet 0.4.10 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for eventlet==0.19.0

    Best match: eventlet 0.19.0

    Adding eventlet 0.19.0 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for dnspython==1.14.0

    Best match: dnspython 1.14.0

    Adding dnspython 1.14.0 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for cffi==1.8.2

    Best match: cffi 1.8.2

    Adding cffi 1.8.2 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for ipaddress==1.0.17

    Best match: ipaddress 1.0.17

    Adding ipaddress 1.0.17 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for enum34==1.1.6

    Best match: enum34 1.1.6

    Adding enum34 1.1.6 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for setuptools==27.2.0

    Best match: setuptools 27.2.0

    Adding setuptools 27.2.0 to easy-install.pth file

    Installing easy\_install-3.5 script to /usr/local/bin

    Installing easy\_install script to /usr/local/bin

    Using /usr/local/lib/python2.7/dist-packages

    Searching for pyasn1==0.1.9

    Best match: pyasn1 0.1.9

    Adding pyasn1 0.1.9 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for idna==2.1

    Best match: idna 2.1

    Adding idna 2.1 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Searching for pycparser==2.14

    Best match: pycparser 2.14

    Adding pycparser 2.14 to easy-install.pth file

    Using /usr/local/lib/python2.7/dist-packages

    Finished processing dependencies **for swift==2.9.0**

    swift@sha-mn-cluster-3:~/swift$

    swift@sha-mn-cluster-3:~/swift$ git branch

    \* (detached from 2.9.0)

    stable/mitaka

    swift@sha-mn-cluster-3:~/swift$

    **Post upgrade restart the A/C/O services on Node 3**

    swift@sha-mn-cluster-3:~/swift$ **swift-init account all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    Starting container-updater...(/etc/swift/container-server/1.conf)

    Starting container-updater...(/etc/swift/container-server/2.conf)

    Starting container-updater...(/etc/swift/container-server/3.conf)

    Starting container-updater...(/etc/swift/container-server/4.conf)

    Starting account-auditor...(/etc/swift/account-server/1.conf)

    Starting account-auditor...(/etc/swift/account-server/2.conf)

    Starting account-auditor...(/etc/swift/account-server/3.conf)

    Starting account-auditor...(/etc/swift/account-server/4.conf)

    Starting object-replicator...(/etc/swift/object-server/1.conf)

    Starting object-replicator...(/etc/swift/object-server/2.conf)

    Starting object-replicator...(/etc/swift/object-server/3.conf)

    Starting object-replicator...(/etc/swift/object-server/4.conf)

    Starting
    container-reconciler...(/etc/swift/container-reconciler.conf)

    Starting container-replicator...(/etc/swift/container-server/1.conf)

    Starting container-replicator...(/etc/swift/container-server/2.conf)

    Starting container-replicator...(/etc/swift/container-server/3.conf)

    Starting container-replicator...(/etc/swift/container-server/4.conf)

    Starting object-auditor...(/etc/swift/object-server/1.conf)

    Starting object-auditor...(/etc/swift/object-server/2.conf)

    Starting object-auditor...(/etc/swift/object-server/3.conf)

    Starting object-auditor...(/etc/swift/object-server/4.conf)

    Starting object-expirer...(/etc/swift/object-expirer.conf)

    Starting container-auditor...(/etc/swift/container-server/1.conf)

    Starting container-auditor...(/etc/swift/container-server/2.conf)

    Starting container-auditor...(/etc/swift/container-server/3.conf)

    Starting container-auditor...(/etc/swift/container-server/4.conf)

    Starting container-server...(/etc/swift/container-server/1.conf)

    Starting container-server...(/etc/swift/container-server/2.conf)

    Starting container-server...(/etc/swift/container-server/3.conf)

    Starting container-server...(/etc/swift/container-server/4.conf)

    Starting object-reconstructor...(/etc/swift/object-server/1.conf)

    Starting object-reconstructor...(/etc/swift/object-server/2.conf)

    Starting object-reconstructor...(/etc/swift/object-server/3.conf)

    Starting object-reconstructor...(/etc/swift/object-server/4.conf)

    Starting account-server...(/etc/swift/account-server/1.conf)

    Starting account-server...(/etc/swift/account-server/2.conf)

    Starting account-server...(/etc/swift/account-server/3.conf)

    Starting account-server...(/etc/swift/account-server/4.conf)

    Starting account-reaper...(/etc/swift/account-server/1.conf)

    Starting account-reaper...(/etc/swift/account-server/2.conf)

    Starting account-reaper...(/etc/swift/account-server/3.conf)

    Starting account-reaper...(/etc/swift/account-server/4.conf)

    Unable to locate config for proxy-server

    Starting account-replicator...(/etc/swift/account-server/1.conf)

    Starting account-replicator...(/etc/swift/account-server/2.conf)

    Starting account-replicator...(/etc/swift/account-server/3.conf)

    Starting account-replicator...(/etc/swift/account-server/4.conf)

    Starting object-updater...(/etc/swift/object-server/1.conf)

    Starting object-updater...(/etc/swift/object-server/2.conf)

    Starting object-updater...(/etc/swift/object-server/3.conf)

    Starting object-updater...(/etc/swift/object-server/4.conf)

    Starting object-server...(/etc/swift/object-server/1.conf)

    Starting object-server...(/etc/swift/object-server/2.conf)

    Starting object-server...(/etc/swift/object-server/3.conf)

    Starting object-server...(/etc/swift/object-server/4.conf)

    Starting container-sync...(/etc/swift/container-server/1.conf)

    Starting container-sync...(/etc/swift/container-server/2.conf)

    Starting container-sync...(/etc/swift/container-server/3.conf)

    Starting container-sync...(/etc/swift/container-server/4.conf)

    swift@sha-mn-cluster-3:~/swift$ **swift-init container all start**

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    container-updater running (30864 -
    /etc/swift/container-server/4.conf)

    container-updater running (30861 -
    /etc/swift/container-server/1.conf)

    container-updater running (30862 -
    /etc/swift/container-server/2.conf)

    container-updater running (30863 -
    /etc/swift/container-server/3.conf)

    container-updater already started...

    account-auditor running (30865 - /etc/swift/account-server/1.conf)

    account-auditor running (30866 - /etc/swift/account-server/2.conf)

    account-auditor running (30867 - /etc/swift/account-server/3.conf)

    account-auditor running (30868 - /etc/swift/account-server/4.conf)

    account-auditor already started...

    object-replicator running (30872 - /etc/swift/object-server/4.conf)

    object-replicator running (30869 - /etc/swift/object-server/1.conf)

    object-replicator running (30870 - /etc/swift/object-server/2.conf)

    object-replicator running (30871 - /etc/swift/object-server/3.conf)

    object-replicator already started...

    container-sync running (31056 - /etc/swift/container-server/1.conf)

    container-sync running (31066 - /etc/swift/container-server/3.conf)

    container-sync running (31075 - /etc/swift/container-server/4.conf)

    container-sync running (31063 - /etc/swift/container-server/2.conf)

    container-sync already started...

    container-replicator running (30885 -
    /etc/swift/container-server/4.conf)

    container-replicator running (30882 -
    /etc/swift/container-server/3.conf)

    container-replicator running (30877 -
    /etc/swift/container-server/1.conf)

    container-replicator running (30879 -
    /etc/swift/container-server/2.conf)

    container-replicator already started...

    object-auditor running (30896 - /etc/swift/object-server/2.conf)

    object-auditor running (30904 - /etc/swift/object-server/3.conf)

    object-auditor running (30890 - /etc/swift/object-server/1.conf)

    object-auditor running (30913 - /etc/swift/object-server/4.conf)

    object-auditor already started...

    object-expirer running (30915 - /etc/swift/object-expirer.conf)

    object-expirer already started...

    container-auditor running (30920 -
    /etc/swift/container-server/1.conf)

    container-auditor running (30922 -
    /etc/swift/container-server/2.conf)

    container-auditor running (30925 -
    /etc/swift/container-server/3.conf)

    container-auditor running (30927 -
    /etc/swift/container-server/4.conf)

    container-auditor already started...

    container-server running (30936 -
    /etc/swift/container-server/3.conf)

    container-server running (30937 -
    /etc/swift/container-server/4.conf)

    container-server running (30931 -
    /etc/swift/container-server/1.conf)

    container-server running (30933 -
    /etc/swift/container-server/2.conf)

    container-server already started...

    object-reconstructor running (30953 -
    /etc/swift/object-server/4.conf)

    object-reconstructor running (30946 -
    /etc/swift/object-server/1.conf)

    object-reconstructor running (30949 -
    /etc/swift/object-server/2.conf)

    object-reconstructor running (30951 -
    /etc/swift/object-server/3.conf)

    object-reconstructor already started...

    object-server running (31032 - /etc/swift/object-server/1.conf)

    object-server running (31045 - /etc/swift/object-server/3.conf)

    object-server running (31038 - /etc/swift/object-server/2.conf)

    object-server running (31053 - /etc/swift/object-server/4.conf)

    object-server already started...

    account-reaper running (30984 - /etc/swift/account-server/2.conf)

    account-reaper running (30977 - /etc/swift/account-server/1.conf)

    account-reaper running (31000 - /etc/swift/account-server/4.conf)

    account-reaper running (30992 - /etc/swift/account-server/3.conf)

    account-reaper already started...

    Unable to locate config for proxy-server

    account-replicator running (31016 -
    /etc/swift/account-server/4.conf)

    account-replicator running (31009 -
    /etc/swift/account-server/2.conf)

    account-replicator running (31005 -
    /etc/swift/account-server/1.conf)

    account-replicator running (31013 -
    /etc/swift/account-server/3.conf)

    account-replicator already started...

    object-updater running (31024 - /etc/swift/object-server/3.conf)

    object-updater running (31028 - /etc/swift/object-server/4.conf)

    object-updater running (31019 - /etc/swift/object-server/1.conf)

    object-updater running (31020 - /etc/swift/object-server/2.conf)

    object-updater already started...

    container-reconciler running (30873 -
    /etc/swift/container-reconciler.conf)

    container-reconciler already started...

    account-server running (30960 - /etc/swift/account-server/1.conf)

    account-server running (30968 - /etc/swift/account-server/2.conf)

    account-server running (30970 - /etc/swift/account-server/3.conf)

    account-server running (30972 - /etc/swift/account-server/4.conf)

    account-server already started...

    swift@sha-mn-cluster-3:~/swift$ swift-init object all start

    WARNING: Unable to modify file descriptor limit. Running as
    non-root?

    container-updater running (30864 -
    /etc/swift/container-server/4.conf)

    container-updater running (30861 -
    /etc/swift/container-server/1.conf)

    container-updater running (30862 -
    /etc/swift/container-server/2.conf)

    container-updater running (30863 -
    /etc/swift/container-server/3.conf)

    container-updater already started...

    account-auditor running (30865 - /etc/swift/account-server/1.conf)

    account-auditor running (30866 - /etc/swift/account-server/2.conf)

    account-auditor running (30867 - /etc/swift/account-server/3.conf)

    account-auditor running (30868 - /etc/swift/account-server/4.conf)

    account-auditor already started...

    object-replicator running (30872 - /etc/swift/object-server/4.conf)

    object-replicator running (30869 - /etc/swift/object-server/1.conf)

    object-replicator running (30870 - /etc/swift/object-server/2.conf)

    object-replicator running (30871 - /etc/swift/object-server/3.conf)

    object-replicator already started...

    container-sync running (31056 - /etc/swift/container-server/1.conf)

    container-sync running (31066 - /etc/swift/container-server/3.conf)

    container-sync running (31075 - /etc/swift/container-server/4.conf)

    container-sync running (31063 - /etc/swift/container-server/2.conf)

    container-sync already started...

    container-replicator running (30885 -
    /etc/swift/container-server/4.conf)

    container-replicator running (30882 -
    /etc/swift/container-server/3.conf)

    container-replicator running (30877 -
    /etc/swift/container-server/1.conf)

    container-replicator running (30879 -
    /etc/swift/container-server/2.conf)

    container-replicator already started...

    object-server running (31032 - /etc/swift/object-server/1.conf)

    object-server running (31045 - /etc/swift/object-server/3.conf)

    object-server running (31038 - /etc/swift/object-server/2.conf)

    object-server running (31053 - /etc/swift/object-server/4.conf)

    object-server already started...

    object-expirer running (30915 - /etc/swift/object-expirer.conf)

    object-expirer already started...

    container-auditor running (30920 -
    /etc/swift/container-server/1.conf)

    container-auditor running (30922 -
    /etc/swift/container-server/2.conf)

    container-auditor running (30925 -
    /etc/swift/container-server/3.conf)

    container-auditor running (30927 -
    /etc/swift/container-server/4.conf)

    container-auditor already started...

    container-server running (30936 -
    /etc/swift/container-server/3.conf)

    container-server running (30937 -
    /etc/swift/container-server/4.conf)

    container-server running (30931 -
    /etc/swift/container-server/1.conf)

    container-server running (30933 -
    /etc/swift/container-server/2.conf)

    container-server already started...

    object-reconstructor running (30953 -
    /etc/swift/object-server/4.conf)

    object-reconstructor running (30946 -
    /etc/swift/object-server/1.conf)

    object-reconstructor running (30949 -
    /etc/swift/object-server/2.conf)

    object-reconstructor running (30951 -
    /etc/swift/object-server/3.conf)

    object-reconstructor already started...

    object-auditor running (30896 - /etc/swift/object-server/2.conf)

    object-auditor running (30904 - /etc/swift/object-server/3.conf)

    object-auditor running (30890 - /etc/swift/object-server/1.conf)

    object-auditor running (30913 - /etc/swift/object-server/4.conf)

    object-auditor already started...

    account-reaper running (30984 - /etc/swift/account-server/2.conf)

    account-reaper running (30977 - /etc/swift/account-server/1.conf)

    account-reaper running (31000 - /etc/swift/account-server/4.conf)

    account-reaper running (30992 - /etc/swift/account-server/3.conf)

    account-reaper already started...

    Unable to locate config for proxy-server

    account-replicator running (31016 -
    /etc/swift/account-server/4.conf)

    account-replicator running (31009 -
    /etc/swift/account-server/2.conf)

    account-replicator running (31005 -
    /etc/swift/account-server/1.conf)

    account-replicator running (31013 -
    /etc/swift/account-server/3.conf)

    account-replicator already started...

    object-updater running (31024 - /etc/swift/object-server/3.conf)

    object-updater running (31028 - /etc/swift/object-server/4.conf)

    object-updater running (31019 - /etc/swift/object-server/1.conf)

    object-updater running (31020 - /etc/swift/object-server/2.conf)

    object-updater already started...

    container-reconciler running (30873 -
    /etc/swift/container-reconciler.conf)

    container-reconciler already started...

    account-server running (30960 - /etc/swift/account-server/1.conf)

    account-server running (30968 - /etc/swift/account-server/2.conf)

    account-server running (30970 - /etc/swift/account-server/3.conf)

    account-server running (30972 - /etc/swift/account-server/4.conf)

    account-server already started...

    swift@sha-mn-cluster-3:~/swift$

    **Check releases on other not upgraded A/C/O nodes – 2 and 4**

    swift@sha-mn-cluster-2:~/swift$ git branch

    \* stable/mitaka

    swift@sha-mn-cluster-2:~/swift$

    swift@sha-mn-cluster-4:~/swift$ git branch

    \* stable/mitaka

    swift@sha-mn-cluster-4:~/swift$

    **On Node 3 – 2.9.0 (upgraded node) : Run Tests **

    swift@sha-mn-cluster-3:~/swift$ **~/swift/.** **unittests**

    Name Stmts Miss Branch BrPart Cover

    TOTAL 24028 1405 9138 661 93%

    ----------------------------------------------------------------------------------------

    Ran 4695 tests in 122.393s

    OK

    swift@sha-mn-cluster-3:~/swift$ **~/swift/.** **functests**

    ======

    Totals

    ======

    Ran: 434 tests in 109.0000 sec.

    - Passed: 414

    - Skipped: 20

    - Expected Fail: 0

    - Unexpected Success: 0

    - Failed: 0

    Sum of execute time for each test: 107.2709 sec.

    ==============

    Worker Balance

    ==============

    - Worker 0 (434 tests) => 0:01:48.114741

    Slowest Tests:

    Test id Runtime (s)

    ------------------------------------------------------------------------
    -----------

    test.functional.tests.TestFileUTF8.testFileSizeLimit 12.016

    test.functional.tests.TestFile.testFileSizeLimit 12.015

    test.functional.test\_object.TestObject.test\_x\_delete\_at 4.278

    test.functional.tests.TestAccountNoContainers.testGetRequest 3.683

    test.functional.tests.TestContainer.testContainerExistenceCachingProblem
    3.652

    test.functional.tests.TestAccount.testContainerSerializedInfo 3.548

    test.functional.tests.TestAccountUTF8.testContainerSerializedInfo
    3.424

    test.functional.tests.TestSlo.test\_slo\_container\_listing 2.807

    test.functional.tests.TestContainerPaths.testContainerListing 2.422

    test.functional.test\_object.TestObject.test\_x\_delete\_after 2.273

    /home/swift/swift

    swift@sha-mn-cluster-3:~/swift$

    Right after upgrade Node 3 ; check data on all 3 A/C/O nodes

    **Node 3 (upgraded node)**

    swift@sha-mn-cluster-3:~/swift$ find /mnt/ -name "\*.data"

    swift@sha-mn-cluster-3:~/swift$

    **Node 2**

    swift@sha-mn-cluster-2:~/swift$ find /mnt/ -name "\*.data"

    /mnt/sdb1/2/node/sdb2/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    /mnt/sdb1/2/node/sdb2/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    swift@sha-mn-cluster-2:~/swift$

    **Node 4**

    swift@sha-mn-cluster-4:~/swift$ find /mnt/ -name "\*.data"

    /mnt/sdb1/1/node/sdb1/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    /mnt/sdb1/1/node/sdb1/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    /mnt/sdb1/2/node/sdb2/objects/310/879/4d81c811b5d605c2c5aae538d4ea0879/1474040287.93403.data

    /mnt/sdb1/4/node/sdb4/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    /mnt/sdb1/4/node/sdb4/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    swift@sha-mn-cluster-4:~/swift$

    **Node 1 ; Proxy **

    swift@sha-mn-cluster-1:~/swift$ swift stat

    Account: AUTH\_test

    Containers: 1

    Objects: 3

    Bytes: 639631360

    Containers in policy "gold": 1

    Objects in policy "gold": 3

    Bytes in policy "gold": 639631360

    X-Timestamp: 1474040151.66836

    X-Trans-Id: tx240a5451650f41929da35-0057dc36c9

    Content-Type: text/plain; charset=utf-8

    Accept-Ranges: bytes

    swift@sha-mn-cluster-1:~/swift$ swift@sha-mn-cluster-1:~/swift$
    swift stat

    **Create a container**

    swift@sha-mn-cluster-1:~/test$ swift post t

    **Upload an object**

    swift@sha-mn-cluster-1:~$ swift upload t .bashrc

    .bashrc

    **Node 3**

    swift@sha-mn-cluster-3:~$ find /mnt/ -name "\*.data"

    /mnt/sdb1/3/node/sdb3/objects/177/b54/2c74a230d6feb0d1b688eca143417b54/1474050428.69050.data

    /mnt/sdb1/4/node/sdb4/objects/177/b54/2c74a230d6feb0d1b688eca143417b54/1474050428.69050.data

    **Node 4**

    swift@sha-mn-cluster-4:~$ find /mnt/ -name "\*.data"

    /mnt/sdb1/1/node/sdb1/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    /mnt/sdb1/1/node/sdb1/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    /mnt/sdb1/2/node/sdb2/objects/310/879/4d81c811b5d605c2c5aae538d4ea0879/1474040287.93403.data

    /mnt/sdb1/2/node/sdb2/objects/177/b54/2c74a230d6feb0d1b688eca143417b54/1474050428.69050.data

    /mnt/sdb1/4/node/sdb4/objects/937/296/ea73380626493328ba32063d247dd296/1474040194.66700.data

    /mnt/sdb1/4/node/sdb4/objects/456/fef/7234d5bf02f949df13c7bdb481ad1fef/1474040227.69752.data

    swift@sha-mn-cluster-4:~$ swift-object-info
    /mnt/sdb1/2/node/sdb2/objects/177/b54/2c74a230d6feb0d1b688eca143417b54/1474050428.69050.data

    Path: /AUTH\_test/t/.bashrc

    Account: AUTH\_test

    Container: t

    **Object: .bashrc**

    Object hash: 2c74a230d6feb0d1b688eca143417b54

    Content-Type: application/octet-stream

    Timestamp: 2016-09-16T18:27:08.690500 (1474050428.69050)

    System Metadata:

    No metadata found

    User Metadata:

    X-Object-Meta-Mtime: 1472756127.960839

    Other Metadata:

    No metadata found

    ETag: 853d47e293d78766ccd8292e3e923cc0 (valid)

    Content-Length: 3846 (valid)

    Partition 177

    Hash 2c74a230d6feb0d1b688eca143417b54

    Server:Port Device 192.168.0.204:6040 sdb4

    Server:Port Device 192.168.0.204:6030 sdb3

    Server:Port Device 192.168.0.206:6020 sdb2

    Server:Port Device 192.168.0.206:6010 sdb1 [Handoff]

    Server:Port Device 192.168.0.203:6020 sdb2 [Handoff]

    Server:Port Device 192.168.0.206:6040 sdb4 [Handoff]

    curl -I -XHEAD
    "http://192.168.0.204:6040/sdb4/177/AUTH\_test/t/.bashrc" -H
    "X-Backend-Storage-Policy-Index: 0"

    curl -I -XHEAD
    "http://192.168.0.204:6030/sdb3/177/AUTH\_test/t/.bashrc" -H
    "X-Backend-Storage-Policy-Index: 0"

    curl -I -XHEAD
    "http://192.168.0.206:6020/sdb2/177/AUTH\_test/t/.bashrc" -H
    "X-Backend-Storage-Policy-Index: 0"

    curl -I -XHEAD
    "http://192.168.0.206:6010/sdb1/177/AUTH\_test/t/.bashrc" -H
    "X-Backend-Storage-Policy-Index: 0" # [Handoff]

    curl -I -XHEAD
    "http://192.168.0.203:6020/sdb2/177/AUTH\_test/t/.bashrc" -H
    "X-Backend-Storage-Policy-Index: 0" # [Handoff]

    curl -I -XHEAD
    "http://192.168.0.206:6040/sdb4/177/AUTH\_test/t/.bashrc" -H
    "X-Backend-Storage-Policy-Index: 0" # [Handoff]

    Use your own device location of servers:

    such as "export DEVICE=/srv/node"

    ssh 192.168.0.204 "ls -lah
    ${DEVICE:-/srv/node\*}/sdb4/objects/177/b54/2c74a230d6feb0d1b688eca143417b54"

    ssh 192.168.0.204 "ls -lah
    ${DEVICE:-/srv/node\*}/sdb3/objects/177/b54/2c74a230d6feb0d1b688eca143417b54"

    ssh 192.168.0.206 "ls -lah
    ${DEVICE:-/srv/node\*}/sdb2/objects/177/b54/2c74a230d6feb0d1b688eca143417b54"

    ssh 192.168.0.206 "ls -lah
    ${DEVICE:-/srv/node\*}/sdb1/objects/177/b54/2c74a230d6feb0d1b688eca143417b54"
    # [Handoff]

    ssh 192.168.0.203 "ls -lah
    ${DEVICE:-/srv/node\*}/sdb2/objects/177/b54/2c74a230d6feb0d1b688eca143417b54"
    # [Handoff]

    ssh 192.168.0.206 "ls -lah
    ${DEVICE:-/srv/node\*}/sdb4/objects/177/b54/2c74a230d6feb0d1b688eca143417b54"
    # [Handoff]

    note: \`/srv/node\*\` is used as default value of \`devices\`, the
    real value is set in the config file on each storage node.

    swift@sha-mn-cluster-4:~$

    Node 2

    swift@sha-mn-cluster-2:~/swift$ find /mnt/ -name "\*.db"

    /mnt/sdb1/2/node/sdb2/accounts/802/178/c8bcccab3ddbfdc34b08e9223f4f5178/c8bcccab3ddbfdc34b08e9223f4f5178.db

    /mnt/sdb1/2/node/sdb2/containers/926/e06/e796f02ad045f424c7b921421b016e06/e796f02ad045f424c7b921421b016e06.db

    /mnt/sdb1/2/node/sdb2/containers/98/5f7/188b6b6d3ad439fd504fb6028487e5f7/188b6b6d3ad439fd504fb6028487e5f7.db

    swift@sha-mn-cluster-2:~/swift$

    Node 3

    swift@sha-mn-cluster-3:~$ find /mnt/ -name "\*.data"

    /mnt/sdb1/3/node/sdb3/objects/177/b54/2c74a230d6feb0d1b688eca143417b54/1474050428.69050.data

    /mnt/sdb1/4/node/sdb4/objects/177/b54/2c74a230d6feb0d1b688eca143417b54/1474050428.69050.data

    Node 4

    swift@sha-mn-cluster-4:~$ find /mnt/ -name "\*.db"

    /mnt/sdb1/1/node/sdb1/containers/926/e06/e796f02ad045f424c7b921421b016e06/e796f02ad045f424c7b921421b016e06.db

    /mnt/sdb1/1/node/sdb1/containers/98/5f7/188b6b6d3ad439fd504fb6028487e5f7/188b6b6d3ad439fd504fb6028487e5f7.db

    /mnt/sdb1/4/node/sdb4/accounts/802/178/c8bcccab3ddbfdc34b08e9223f4f5178/c8bcccab3ddbfdc34b08e9223f4f5178.db
