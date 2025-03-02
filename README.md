# openshift-python-wrapper
Pypi: [openshift-python-wrapper](https://pypi.org/project/openshift-python-wrapper)  
A python wrapper for [openshift-restclient-python](https://github.com/openshift/openshift-restclient-python) with support for RedHat Container Virtualization. ([Openshift Virtualization](https://www.openshift.com/learn/topics/virtualization))  
Docs: [openshift-python-wrapper docs](https://openshift-python-wrapper.readthedocs.io/en/latest/)

## Installation
From source:
```bash
git clone https://github.com/RedHatQE/openshift-python-wrapper.git
cd openshift-python-wrapper
python setup.py install --user
```
From pypi:
```bash
pip install openshift-python-wrapper --user
```

## Release new version
### requirements:
* gh [github cli](https://github.com/cli/cli/releases/)
```bash
gh auth login # Follow login instructions
```
* [npm](https://docs.npmjs.com/about-npm)
```bash
sudo npm install github-release-notes -g
```
* export GREN_GITHUB_TOKEN=< TOKEN >
* Run ./scripts/release.sh providing source branch and target version (must be executed from main branch)
### usage:
```bash
./scripts/release.sh main v1.5.5
```

## docs
Hosted on readthedocs.io
```

## PR dependency
For PR dependency we use [dpulls](https://www.dpulls.com/)  
To make PR depends on other PR add `depends on #<PR NUMBER>` in the PR description.

## Examples
### Client
```python
client = DynamicClient(client=kubernetes.config.new_client_from_config())
```
The examples given below are relevant to all resources. For simplicity we will use the resource - Namespace.
### Import
Import Namespace:
```python
from resources.namespace import Namespace
```
### Create
Create a Namespace:
```python
ns = Namespace(name="namespace-example-1")
ns.create()
```
Will return ``True`` if creation succeeded.

We can also use the ``with`` statement which ensures automatic clean-up of the code executed:
```python
with Namespace(name="namespace-example-2") as ns:
    yield ns
```
``teardown=False`` -  Disables clean-up after execution.
### Wait
Wait for Namespace to be in status ``Active``:
```python
ns.wait_for_status(status=Namespace.Status.ACTIVE, timeout=120)
```
Will raise a ``TimeoutExpiredError`` if Namespace is not in the desired status.
### Delete
Delete the Namespace
```python
ns.delete()
```
Will return ``False`` if not found.
### Exists
Checks if Namespace exists on the server:
```python
ns.exists
```
Will return ``None`` if not found.
### Get
Query to get Pods (resource) in the connected cluster with label of ``label_example=example``. Returns a ``generator`` of the resource - ``pod``
```python
for pod in Pod.get(dyn_client=client, label_selector="label_example=example")):
    pod.log()
```
We can also get the name of the Node that the ``pod`` is running on:
```python
pod.node.name
```
### VM
Start:
```python
with VirtualMachine(
    name="vm-example",
    namespace="namespace-example",
    node_selector="worker-node-example",
) as vm:
    vm.start()
```
Stop:
```python
vm.stop()
```
Restart:
```python
vm.restart()
```
Get VMI:
```python
test_vmi = vm.vmi
```
After having a VMI, we can wait until VMI is in running state:
```python
test_vmi.wait_until_running()
```
Will raise ``TimeoutExpiredError`` if VMI failed to run.

Then, we can get the Pod that is in Running state and execute a command on it:
```python
command_output = test_vmi.virt_launcher_pod.execute(command="command-example")
```
If no Pod was found, will raise ``ResourceNotFoundError``.

## Code check
We use pre-commit for code check.
```bash
pre-commit install
```
