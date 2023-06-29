# Module 7 - Zero-trust access control using Network Policy Recommender

Reduce the attack surface of the application by implementing a zero-trust security policy.

By default, all traffic is allowed between the pods in a cluster. First, let's test connectivity between application components and across namespaces. All of these tests should succeed as there are no policies in place.

a. Test connectivity between ```centos``` pod to the ```nginx``` pods within the same namespace. The expected result is `HTTP/1.1 200 OK`.

   ```bash
   kubectl -n dev exec -t centos -- sh -c 'curl -m3 -sI http://nginx-svc 2>/dev/null | grep HTTP'
   ```

b. Test connectivity across namespaces ```default/centos``` and ```dev/nginx```. The expected result is `HTTP/1.1 200 OK`.

   ```bash
   kubectl exec -t centos -- sh -c 'curl -m3 -sI http://nginx-svc.dev 2>/dev/null | grep HTTP'
   ```

c. Test connectivity from ```dev``` namespace and ```default``` namespace to the Internet. The expected result is `HTTP/1.1 200 OK`.

   ```bash
   kubectl -n dev exec -t centos -- sh -c 'curl -m3 -sI https://www.google.com 2>/dev/null | grep HTTP'
   ```

   ```bash
   kubectl exec -t centos -- sh -c 'curl -m3 -sI https://www.google.com 2>/dev/null | grep HTTP'
   ```

Use the Security Policy Recommender to quickly create a security policy restricting ingress traffic from the ```netshoot``` pod in ```dev``` namespace to the vulnerable ```nginx``` pod

We recommend that you create a global default deny policy after you complete writing policy for the traffic that you want to allow. Use the stage policy feature to get your allowed traffic working as expected, then lock down the cluster to block unwanted traffic.

1. Create a staged global default deny policy. It will shows all the traffic that would be blocked if it were converted into a deny.

   ```yaml
   kubectl apply -f - <<-EOF
   apiVersion: projectcalico.org/v3
   kind: StagedGlobalNetworkPolicy
   metadata:
     name: default-deny
   spec:
     order: 2000
     selector: "projectcalico.org/namespace in {'dev','default'}"
     types:
     - Ingress
     - Egress
   EOF
   ```

   You should be able to view the potential affect of the staged default-deny policy if you navigate to the Dashboard view in the Enterprise Manager UI and look at the Packets by Policy histogram.

   The staged policy does not affect the traffic directly but allows you to view the policy impact if it were to be enforced. You can see the deny traffic in staged policy.

2. Create other network policies to individually allow the traffic shown as blocked in step 1, until no connections are denied.
  
   First, create the policy tiers. Tiers are a hierarchical construct used to group policies and enforce higher precedence policies that cannot be circumvented by other teams. As you will learn in this tutorial, tiers have built-in features that support workload microsegmentation.

   ```yaml
   kubectl apply -f - <<-EOF   
   apiVersion: projectcalico.org/v3
   kind: Tier
   metadata:
     name: security
   spec:
     order: 300
   EOF
   ```

   Now apply network policies to your application with explicit allow and deny control.

   ```yaml
   kubectl apply -f - <<-EOF   
   apiVersion: projectcalico.org/v3
   kind: NetworkPolicy
   metadata:
     name: default.centos
     namespace: dev
   spec:
     tier: default
     order: 800
     selector: app == "centos"
     egress:
     - action: Allow
       protocol: UDP
       destination:
         selector: k8s-app == "kube-dns"
         namespaceSelector: kubernetes.io/metadata.name == "kube-system" 
         ports:
         - '53'
     - action: Allow
       protocol: TCP
       destination:
         selector: app == "nginx"
     types:
       - Egress
   EOF
   ```

   Test connectivity with policies in place.

   a. The only connections between the components within namespace ```dev``` are from ```centos``` to ```nginx```, which should be allowed as configured by the policy. The expected result is `HTTP/1.1 200 OK`.

   ```bash
   kubectl -n dev exec -t centos -- sh -c 'curl -m3 -sI http://nginx-svc 2>/dev/null | grep HTTP'
   ```

   b. Test connectivity from namespace ```dev``` and ```default``` to the Internet. The expected result is `command terminated with exit code 1`.

   ```bash
   kubectl -n dev exec -t centos -- sh -c 'curl -m3 -sI http://www.google.com 2>/dev/null | grep HTTP'
   ```

   ```bash
   kubectl -n default exec -t centos -- sh -c 'curl -m3 -sI http://www.google.com 2>/dev/null | grep HTTP'
   ```

   c. As per the default deny, egress traffic from the ```centos``` pod in ```default``` namespace is also blocked so it can't connect to anything in the dev namespace.

   ```bash
   kubectl exec -t centos -- sh -c 'curl -m3 -sI http://nginx-svc.dev 2>/dev/null | grep HTTP'
   ```

   d. Deploy the following global network policy in the security tier for allowing DNS access to all endpoints. In this way you don't need to create a rule allowing DNS traffic for every policy.

   ```yaml
   kubectl apply -f - <<-EOF
   apiVersion: projectcalico.org/v3
   kind: GlobalNetworkPolicy
   metadata:
     name: security.allow-kube-dns
   spec:
     tier: security
     order: 200
     selector: all()
     types:
     - Egress    
     egress:
       - action: Allow
         protocol: UDP
         source: {}
         destination:
           selector: k8s-app == "kube-dns"
           ports:
           - '53'
       - action: Pass
   EOF
   ```

3. Use the Calico Cloud GUI to enforce the default-deny staged policy. After enforcing a staged policy, it takes effect immediatelly. The default-deny policy will start to actually deny traffic.

[:arrow_right: Module 8 - Enabling End to End Encryption with WireGuard](module-8-encryption.md) <br>

[:arrow_left: Module 6 - Runtime security with IDS/IPS using Deep Packet Inspection](module-6-runtimesec.md)

[:leftwards_arrow_with_hook: Back to Main](../README.md)
