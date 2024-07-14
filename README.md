

# What happens when a pod runs - namespace 

```
apiVersion: v1
kind: Pod
metadata:
  name: shared-namespace
spec:
  containers:
    - name: p1
      image: busybox
      command: ['/bin/sh', '-c', 'sleep 10000']
    - name: p2
      image: nginx
```
Commands 

```
ip netns list

```
### how to check pause container 
```
kubectl run nginx --image=nginx
lsns | grep nginx
```
copy the process IP from above and run 
```
lsns -p <pid>

```

### check the network namespace (this gives list of all network namespaces)
`ls -lt /var/run/netns`

### exec into the namespace or into the pod to see the IP links

```
ip netns exec <namespace> ip link
kubectl exec -it shared-namespace -- ip addr #run this on controlplane
```
Now you will see `eth@9` -> after `@` there will be a number and you can then search its corresponding link on the node using 
`ip link | grep -A1 ^9`
you will be able to see the same network namespace after link
These are the veth pairs or based on the CNI 





===========================


1. **List all network interfaces on the host**:

    ```bash
    ip link show
    ```

2. **List network namespaces**:

    ```bash
    sudo ip netns list
    ```

3. **Inspect network interfaces within a specific namespace**:

    Replace `<namespace>` with the actual namespace ID (e.g., `cni-6bbf75c3-3284-407a-5869-90772afb5472`).

    ```bash
    sudo ip netns exec <namespace> ip link
    ```

4. **Find veth pairs on the host**:

    ```bash
    ip link show | grep cali
    ```

5. **Verify the veth pair connection**:

    Replace `<interface>` with the actual veth interface name (e.g., `caliede2c6f02d9`).

    ```bash
    sudo ethtool -S <interface>
    ```

### Example Workflow

1. **List all interfaces**:

    ```bash
    ip link show
    ```

2. **List network namespaces**:

    ```bash
    sudo ip netns list
    ```

3. **Inspect network interfaces within the namespace `cni-6bbf75c3-3284-407a-5869-90772afb5472`**:

    ```bash
    sudo ip netns exec cni-6bbf75c3-3284-407a-5869-90772afb5472 ip link
    ```

4. **Find veth pairs on the host**:

    ```bash
    ip link show | grep cali
    ```

5. **Verify the veth pair connection for interface `caliede2c6f02d9`**:

    ```bash
    sudo ethtool -S caliede2c6f02d9
    ```

These commands will help you identify the network interfaces and veth pairs created by the CNI plugin, and inspect the network configuration for your Pods.
