=============
Swift Upgrade
=============
Manual upgrade steps : Storage node
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Tested on a multi-node(VM) Swift cluster (2 Proxy , 3 ACO)
Steps – Upgrade a single storage (ACO) node first

1.	Stop all background Swift jobs with

.. code::

   $ swift-init rest stop

2.	Shutdown all Swift storage processes with

.. code::

   $ swift-init {account|container|object} shutdown --graceful

3.	Upgrade all system packages and Swift code

-update source code

.. code::

   $ git tag –l # lists all the tags for the given repo
   $ git checkout <tag_to_update_to>

-upgrading the packages

.. code::

   $ sudo pip install –r requirements.txt --upgrade
   $ sudo pip install –r test-requirements.txt –upgrade
   $ sudo python setup.py install

4.	If needed, reboot server

.. code::

   $ sudo reboot

5.	Start the storage service with

.. code::

   $ swift-init {account|container|object} start

6.	Start background Swift jobs with

.. code::

   $ swift-init rest start

Upgrading all of the other storage nodes
-	Repeat the above steps for each node

Manual upgrade steps : Proxy node
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.	Shutdown the Swift- Proxy server with

.. code::

   $ swift-init proxy shutdown [ --graceful ]

2. Create ''disable_path''- file system path in proxy config, to cause
/heatlthcheck endpoint to return 503 service unavailable

3.	Upgrade all system packages and Swift code

-update source code

.. code::

   $ git tag –l # lists all the tags for the given repo
   $ git checkout <tag_to_update_to>

-upgrading the packages

.. code::

   $ sudo pip install –r requirements.txt --upgrade
   $ sudo pip install –r test-requirements.txt –upgrade
   $ sudo python setup.py install

4. Update the proxy configs with changes, if any

5.	If needed, reboot server

.. code::

   $ sudo reboot

6.	Start the proxy service with

.. code::

   $ swift-init proxy start

7. Remove the ''disable_path'' file


Terminology
~~~~~~~~~~~

Control Plane:
~~~~~~~~~~~~~~
A software layer that manages data stored across storage devices. Control
plane handles requests for user account logins, as well as CRUD (Create,
Read, Update and Delete) requests for resources such as Swift - objects. In
Swift, proxy service can be considered as Control plane as it serves
the above functionalities and provides access to the underlying stored data
and storage services. If the Control plane were to crash, access to the data
is lost but not the actual data.

Data Plane:
~~~~~~~~~~~
A software layer that manages data, storage devices, read/write operations to
the data stored on devices. Data plane manages updating the databases, file
access- I/O or file system tasks. In Swift, strorage services - Account,
Container and Object services can be considered as Data plane. The Data plane
notifies the Control plane of possible 'running out of disk space' or drive
failure etc. scenarios. If a Data plane were to crash, data in that node is
lost.

Zero downtime Rolling upgrade
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Swift service by desgin provides zero downtime rolling upgrade as in say
during strorage node upgrade CRUD requests to access the data through the
proxy-server continue without any delay or change as we are upgrading one
node at a time and the data can be accessed with the remaining nodes and the
new node will be updated with the data during the next update cycle. Similary
during the proxy-node upgrade, load balancer will direct the CRUD requests
to other proxy nodes and the user will not notice any difference in their
requests being handled.

Reference
~~~~~~~~~

https://www.swiftstack.com/blog/2013/12/20/upgrade-openstack-swift-no-downtime/
https://www.blueboxcloud.com/resources/user-resources/upgrading-openstack-a-best-practices-guide
