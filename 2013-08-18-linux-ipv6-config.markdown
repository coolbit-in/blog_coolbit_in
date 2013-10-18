---
layout: post
title: "在 linux 下设置 ipv6 地址和网关"
date: 2013-08-18 23:04
comments: true
categories: linux
---
由于管理社区服务器的问题，需要手动设置 ipv6 的地址和网关，由于以前没有使用过 ipv6，搜了一些资料，搞定了，在此稍加记录，总的来说和在 ipv4 下的思路没有什么不一样。同样也是`route`和`ip`还有`ifconfig`的使用

###查看路由
`route -A inet6` 列出当前 ipv6 路由，相当于 ipv4 的`route -n`  
`ip -6 route show`也可以做到
###添加 ip
`ip -6 addr add <ipv6address>/<prefixlength> dev <interface> `例如`ip -6 addr add 3ffe:ffff:0:f101::1/64 dev eth0`  
`ifconfig <interface> inet6 add <ipv6address>/<prefixlength>`例如`ifconfig eth0 inet6 add 3ffe:ffff:0:f101::1/64 `
###移除 ip
`ip -6 addr del <ipv6address>/<prefixlength> dev <interface> `例如`ip -6 addr del 3ffe:ffff:0:f101::1/64 dev eth0 `  
`ifconfig <interface> inet6 del <ipv6address>/<prefixlength>`例如`ifconfig eth0 inet6 del 3ffe:ffff:0:f101::1/64`  
###添加路由
`ip -6 route add <ipv6network>/<prefixlength> via <ipv6address> [dev <device>]`例如`ip -6 route add 2000::/3 via 3ffe:ffff:0:f101::1`  
`route -A inet6 add <ipv6network>/<prefixlength> gw <ipv6address> [dev <device>]`例如`route -A inet6 add 2000::/3 gw 3ffe:ffff:0:f101::1`
###移除路由
`ip -6 route del <ipv6network>/<prefixlength> via <ipv6address> [dev <device>]`例如`ip -6 route del 2000::/3 via 3ffe:ffff:0:f101::1`  
`route -A inet6 del <network>/<prefixlength> [dev <device>]`例如`route -A inet6 del 2000::/3 gw 3ffe:ffff:0:f101::1`