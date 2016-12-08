=====================
Swift Rolling Upgrade
=====================

Overview of upgrade process
~~~~~~~~~~~~~~~~~~~~~~~~~~~
1.	Upgrade a single storage (ACO) node ('canary' node)
2.	Observe upgraded storage node for problems or anomalies
3.	Upgrade all other storage nodes (1 at a time)
4.	Upgrade a single proxy node ('canary' node)
5.	Observe upgraded proxy node for problems or anomalies
6.	Upgrade all other proxy nodes (1 at a time)

Upgrading a Storage Node
~~~~~~~~~~~~~~~~~~~~~~~~

NOTE: tested on a multi-node (VMs) Swift cluster (2 Proxy, 3 ACO)

1.	Stop all background Swift jobs with

.. code::

   $ swift-init rest stop

2.	Shutdown all Swift storage processes with

.. code::

   $ swift-init {account|container|object} shutdown --graceful

3.	Upgrade to newer version of Swift

(a) Upgrading from source code

.. code::

   $ git tag –l  # lists all the tags for the given repo
   $ git checkout <tag_to_update_to>

(b) Upgrading packages

.. code::

   $ sudo pip install –r requirements.txt --upgrade
   $ sudo pip install –r test-requirements.txt –upgrade
   $ sudo python setup.py install

4.	If needed, reboot server for kernel upgrades(if any)

.. code::

   $ sudo reboot

5.	Start all the storage service with

.. code::

   $ swift-init {account|container|object} start

6.	Start background Swift jobs with

.. code::

   $ swift-init rest start


Upgrading a Proxy Node
~~~~~~~~~~~~~~~~~~~~~~

1.	Shutdown the Swift proxy server with

.. code::

   $ swift-init proxy shutdown [ --graceful ]

2. Create ''disable_path'' file system path in proxy config, to cause
/heatlthcheck endpoint to return 503 service unavailable (for more info [3])

3.	Upgrade to newer version of Swift

(a) Upgrading from source code

.. code::

   $ git tag –l # lists all the tags for the given repo
   $ git checkout <tag_to_update_to>

(b) Upgrading packages

.. code::

   $ sudo pip install –r requirements.txt --upgrade
   $ sudo pip install –r test-requirements.txt –upgrade
   $ sudo python setup.py install

4. Update the proxy configs with changes, if any

5.	If needed, reboot server for kernel upgrades(if any)

.. code::

   $ sudo reboot

6.	Start the proxy service with

.. code::

   $ swift-init proxy start

7. Remove the ''disable_path'' file (re-enable health check)


Terminology
~~~~~~~~~~~

Control Plane
~~~~~~~~~~~~~
The control plane manages data stored across storage devices. The control
plane handles requests for user account logins, as well as CRUD (Create,
Read, Update and Delete) requests for resources such as Swift objects. In
Swift, the proxy service can be considered as the control plane as it serves
the above functionality and provides access to the underlying stored data
and storage services. If the control plane were to crash, access to the data
is lost but not the actual data.

Data Plane
~~~~~~~~~~
The data plane manages data, storage devices, and the read/write operations to
the data stored on devices. The data plane manages updating the databases, file
access I/O or filesystem tasks. In Swift, storage services (account,
container, object) can be considered as the data plane. The data plane
notifies the control plane of possible 'running out of disk space' or drive
failure scenarios. If a data plane node were to crash, data in that node may be
lost.

Zero Downtime Rolling Upgrade
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Swift services, by design, provide high availability and redundancy. This
feature allows for zero-downtime rolling upgrades. For example, during
upgrade of a single storage node, CRUD requests (made to proxy server) will
continue without any interruption of service. This zero-downtime upgrade is
possible because the other storage nodes will still be online and accessible
by the proxy server. The newly upgraded storage node will gain data consistency
with the remaining storage nodes during the next replication cycle that
occurs after being brought online.

Similarly, there would be no downtime during upgrade of a single proxy
node as the load balancer will direct requests to other proxy nodes
within the cluster. The end user will not experience any service
interruptions during this upgrade process.

The key to high availability (zero downtime) during Swift upgrades is:
(1) having multiple storage nodes and multiple proxy nodes, and
(2) performing upgrades one node at a time.

References
~~~~~~~~~~
[1] https://www.swiftstack.com/blog/2013/12/20/upgrade-openstack-swift-no-downtime/

[2] https://www.blueboxcloud.com/resources/user-resources/upgrading-openstack-a-best-practices-guide

[3] https://github.com/openstack/swift/blob/master/etc/proxy-server.conf-sample#L408
