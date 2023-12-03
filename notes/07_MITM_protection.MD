# Protection against MITM attacks
-   to detect ARP spoofing exist tools like XArp, which will also show the attacker device
    -   it will detect the attacker based on changed IP address for the router

-   It is possible to find suspicious behavior with wireshark
    -   are there many ARP requests broadcasted? 
    -   inside analyze option from main top bar, it is possible to see warnings
        -   are there more ips for one MAC configured?

-   To not allow APR poisoning, it is possible to setup ip addresses manually (static)
    -   only for small networks, where devices are not changing.

-   If connected to public wifi connect only to https pages. Tools like firefox: plugin https everywhere. It works only for websites which has https. But still some data can be seen by attacker (DNS spoofing still possible, visited domains still visible).

- VPN is making the connection fully secured even for HTTP, since VPM creates encrypted tunned from server to device. But the provider can see and scan everything. So to be still secured it is still better to stay on HTTPS sites.