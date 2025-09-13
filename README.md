# sockperf_cli

*Disclaimer: These results are based on informal testing in a non-production environment using secondhand NICs. While I've aimed for consistency, Expect some noise in the data.*

### System Under the Test:  
Xeon ES QYFS, [Pro WS W790E-SAGE SE](https://www.asus.com/motherboards-components/motherboards/workstation/pro-ws-w790e-sage-se), yes it's Intel W790 thanks to the great community of [ASUS Pro WS W790E-SAGE SE + Intel Xeon Sapphire Rapids SPR-SP](https://forums.servethehome.com/index.php?threads/asus-pro-ws-w790e-sage-se-intel-xeon-sapphire-rapids-spr-sp.41306) and especially [@RolloZ170](https://forums.servethehome.com/index.php?members/rolloz170.7479/).  
Memory 128GB: 16GB x 8 DIMMs x 2 NUMA nodes  

#### BIOS:
- CPU C-state Disabled
- CPU P-state Disabled
- Hyper-Threading Disabled (Single LP)
- Turbo Mode Enabled 

#### DPDK
- main [3c440cdfe87a2925af1be023e66bdf0bffc423a4](https://github.com/DPDK/dpdk/commit/3c440cdfe87a2925af1be023e66bdf0bffc423a4)

#### Ubuntu 24.04.2 LTS  
- BOOT_IMAGE=/boot/vmlinuz-6.14.0-27-generic isolcpus=0-27 nohz_full=0-27 rcu_nocbs=0-27
  
#### Windows Server 2025 Datacenter Edition, Evaluation (24H2), Windows Feature Experience Pack 1000.26100.234.0  
- not tuned

*** All timing is round trip timing  
*** Payload sizes based on most frequent payload size of:
  - [CME Globex MDP 3.0](https://sample-pcaps-dl.databento.com/glbx-all/20230716/dc3-glbx-a-20230716.zip?_gl=1*1wtb1vr*_ga*NDM4NDY1NDAyLjE3NDkyOTQxMjM.*_ga_5ZP4X1MB1Q*czE3NTc4MzY1NTkkbzckZzAkdDE3NTc4MzY1NTkkajYwJGwwJGgyMDA1ODA2MTk3)  
  - [Nasdaq TotalView-ITCH 5.0](https://sample-pcaps-dl.databento.com/xnas/20230822/ny4-xnas-tvitch-a-20230822.zip?_gl=1*r45w38*_ga*NDM4NDY1NDAyLjE3NDkyOTQxMjM.*_ga_5ZP4X1MB1Q*czE3NTc4MzY1NTkkbzckZzAkdDE3NTc4MzY1NTkkajYwJGwwJGgyMDA1ODA2MTk3)  

#### Mellanox MCX4121A-ACAT ConnectX-4 Lx - Ubuntu 24.04.2 LTS
dpdk-testpmd -a 0000:16:00.0,rxq_pkt_pad_en=1,mprq_en=1 -n 8 -l 5-15 -- --forward-mode=5tswap  
[sockperf_cli](https://github.com/serge-klim/dpdk-tools/releases/tag/v0.0.3) -c [sockperf-ping-lx4.config](configs/ubuntu/sockperf-ping-lx4.config)  -v --detailed-stats --cpu-cycles-latency -l 1024  -s [payload size]  

[average: 4.45707 us. median: 4.3625 us. mode: 4.317000 us. min: 4.171 us. max: 11.003 us. per 1024 samples, packet size: 140 bytes / payload size: 98 bytes](logs/lx4/mx-lx4-2025-09-13_08-31-36.0.log#L1164)  
[average: 4.7476 us. median: 4.668 us. mode: 4.600000 us. min: 4.403 us. max: 13.082 us. per 1024 samples, packet size: 344 bytes / payload size: 302 bytes](logs/lx4/mx-lx4-2025-09-13_08-33-19.0.log#L1164)  
[average: 5.39379 us. median: 5.2515 us. mode: 5.291000 us. min: 4.812 us. max: 15.253 us. per 1024 samples, packet size: 652 bytes / payload size: 610 bytes](logs/lx4/mx-lx4-2025-09-13_08-35-02.0.log#L1164)   
[average: 6.06097 us. median: 5.969 us. mode: 5.897000,6.057000 us. min: 5.611 us. max: 11.452 us. per 1024 samples, packet size: 1342 bytes / payload size: 1300 bytes](logs/lx4/mx-lx4-2025-09-13_08-36-45.0.log#L1164)  

#### Mellanox MCX4121A-ACAT ConnectX-4 Lx - Windows Server 2025 Datacenter Edition, Evaluation. 24H2 Windows Feature Experience Pack 1000.26100.234.0
dpdk-testpmd -a 0000:16:00.0 -n 8 -l 5-15 -- --forward-mode=5tswap  
[sockperf_cli](https://github.com/serge-klim/dpdk-tools/releases/tag/v0.0.3) -c [sockperf-ping-lx4.config](configs/win/sockperf-ping-lx4.config)  -v --detailed-stats --cpu-cycles-latency -l 1024  -s [payload size]  

[average: 5.40532 us. median: 4.9505 us. mode: 4.704000 us. min: 4.373 us. max: 76.983 us. per 1024 samples, packet size: 140 bytes / payload size: 98 bytes](logs/lx4.win/mx-lx4-2025-09-13_11-21-09.0.log#L1170)  
[average: 5.50887 us. median: 5.212 us. mode: 4.902000 us. min: 4.6 us. max: 31.624 us. per 1024 samples, packet size: 344 bytes / payload size: 302 bytes](logs/lx4.win/mx-lx4-2025-09-13_11-22-52.0.log#L1170)  
[average: 5.90608 us. median: 5.5535 us. mode: 5.358000 us. min: 5 us. max: 41.227 us. per 1024 samples, packet size: 652 bytes / payload size: 610 bytes](logs/lx4.win/mx-lx4-2025-09-13_11-24-35.0.log#L1170)  
[average: 6.79114 us. median: 6.5075 us. mode: 6.244000,6.864000 us. min: 5.843 us. max: 34.657 us. per 1024 samples, packet size: 1342 bytes / payload size: 1300 bytes](logs/lx4.win/mx-lx4-2025-09-13_11-26-18.0.log#L1170)  

#### BlueField-2 MBF2H322A-AEEOT in ConnectX-6 Dx mode - Ubuntu 24.04.2 LTS
dpdk-testpmd -a 0000:40:00.0 -n 8 -l 5-15 -- --forward-mode=5tswap  
[sockperf_cli](https://github.com/serge-klim/dpdk-tools/releases/tag/v0.0.3) -c [sockperf-ping-lx4.config](configs/ubuntu/sockperf-ping-bf2.config)  -v --detailed-stats --cpu-cycles-latency -l 1024  -s [payload size]  

[average: 5.95309 us. median: 5.893 us. mode: 5.825000 us. min: 5.673 us. max: 9.89 us. per 1024 samples, packet size: 140 bytes / payload size: 98 bytes](logs/bf2/mx-bf2-2025-09-13_12-38-22.0.log#L1164)  
[average: 6.31693 us. median: 6.241 us. mode: 6.182000,6.200000,6.201000,6.231000 us. min: 5.955 us. max: 12.317 us. per 1024 samples, packet size: 344 bytes / payload size: 302 bytes ](logs/bf2/mx-bf2-2025-09-13_12-40-06.0.log#L1164)  
[average: 6.63629 us. median: 6.574 us. mode: 6.582000 us. min: 6.262 us. max: 11.984 us. per 1024 samples, packet size: 652 bytes / payload size: 610 bytes](logs/bf2/mx-bf2-2025-09-13_12-41-49.0.log#L1164)  
[average: 7.35942 us. median: 7.297 us. mode: 7.233000,7.271000,7.274000,7.423000 us. min: 6.903 us. max: 13.198 us. per 1024 samples, packet size: 1342 bytes / payload size: 1300 bytes](logs/bf2/mx-bf2-2025-09-13_12-43-33.0.log#L1164)  

#### BlueField-2 MBF2H322A-AEEOT in ConnectX-6 Dx mode - Windows Server 2025 Datacenter Edition, Evaluation. 24H2 Windows Feature Experience Pack 1000.26100.234.0 
dpdk-testpmd -a 0000:40:00.0 -n 8 -l 5-15 -- --forward-mode=5tswap    
[sockperf_cli](https://github.com/serge-klim/dpdk-tools/releases/tag/v0.0.3) -c [sockperf-ping-bf2.config](configs/win/sockperf-ping-bf2.config)  -v --detailed-stats --cpu-cycles-latency -l 1024  -s [payload size]  

[average: 6.92241 us. median: 6.3505 us. mode: 6.148000 us. min: 5.819 us. max: 52.635 us. per 1024 samples, packet size: 140 bytes / payload size: 98 bytes](logs/bf2.win/mx-bf2-2025-09-13_11-13-52.0.log#L1164)  
[average: 6.99956 us. median: 6.5975 us. mode: 6.336000,6.456000,6.491000 us. min: 6.174 us. max: 36.311 us. per 1024 samples, packet size: 344 bytes / payload size: 302 bytes](logs/bf2.win/mx-bf2-2025-09-13_11-15-35.0.log#L1164)  
[average: 7.71011 us. median: 7.002 us. mode: 6.796000,6.937000,7.222000 us. min: 6.403 us. max: 59.481 us. per 1024 samples, packet size: 652 bytes / payload size: 610 bytes](logs/bf2.win/mx-bf2-2025-09-13_11-17-18.0.log#L1164)  
[average: 8.45674 us. median: 7.8365 us. mode: 7.562000 us. min: 7.19 us. max: 49.622 us. per 1024 samples, packet size: 1342 bytes / payload size: 1300 bytes](logs/bf2.win/mx-bf2-2025-09-13_11-19-01.0.log#L1164)  

#### Xilinx Solarflare 2x10GbE SFP+ SFN8522-PLUS - Ubuntu 24.04.2 LTS
dpdk-testpmd -a 0000:94:00.1,rx_datapath=ef10,tx_datapath=ef10_simple,perf_profile=low-latency,fw_variant=ultra-low-latency -n 8 -l 5-15 -- --forward-mode=5tswap  
[sockperf_cli](https://github.com/serge-klim/dpdk-tools/releases/tag/v0.0.3) -c [sockperf-ping-bf2.config](configs/ubuntu/sockperf-ping-sf.config)  -v --detailed-stats --cpu-cycles-latency -l 1024  -s [payload size]  

[average: 3.89628 us. median: 3.849 us. mode: 3.853000 us. min: 3.689 us. max: 7.691 us. per 1024 samples, packet size: 140 bytes / payload size: 98 bytes](logs/sf/sf-2025-09-13_08-39-30.0.log#L1163)  
[average: 4.44251 us. median: 4.402 us. mode: 4.384000 us. min: 4.217 us. max: 8.375 us. per 1024 samples, packet size: 344 bytes / payload size: 302 bytes](logs/sf/sf-2025-09-13_08-41-13.0.log#L1163)  
[average: 4.87608 us. median: 4.8155 us. mode: 4.800000,4.816000 us. min: 4.576 us. max: 12.058 us. per 1024 samples, packet size: 652 bytes / payload size: 610 bytes](logs/sf/sf-2025-09-13_08-42-56.0.log#L1163)  
[average: 6.2063 us. median: 6.141 us. mode: 6.068000 us. min: 5.97 us. max: 22.287 us. per 1024 samples, packet size: 1342 bytes / payload size: 1300 bytes](logs/sf/sf-2025-09-13_08-44-38.0.log#L1163)  


#### Results of running [sockperf 3.10-32.gitf12cc1b66338](https://github.com/Mellanox/sockperf) on Ubuntu 24.04.2 LTS for the baseline: 

`numactl --cpunodebind=0 --membind=0 /opt/sockperf/bin/sockperf sr -i [server ip] --cpu-affinity=5-15`

### Mellanox MCX4121A-ACAT ConnectX-4 Lx  
```
> numactl --cpunodebind=0  /opt/sockperf/bin/sockperf pp -i 192.168.13.31 --client_ip 192.168.13.32 --increase_output_precision --full-rtt --sender-affinity=16 --receiver-affinity=17 -m 98

sockperf: == version #3.10-32.gitf12cc1b66338 == 
sockperf[CLIENT] send on:sockperf: using recvfrom() to block on socket(s)

[ 0] IP = 192.168.13.31   PORT = 11111 # UDP
sockperf: Warmup stage (sending a few dummy messages)...
sockperf: Starting test...
sockperf: Test end (interrupted by timer)
sockperf: Test ended
sockperf: [Total Run] RunTime=1.001 sec; Warm up time=400 msec; SentMessages=84937; ReceivedMessages=84936
sockperf: ========= Printing statistics for Server No: 0
sockperf: [Valid Duration] RunTime=0.550 sec; SentMessages=48937; ReceivedMessages=48937
sockperf: ====> avg-rtt=11.198 (std-dev=0.540, mean-ad=0.358, median-ad=0.295, siqr=0.214, cv=0.048, std-error=0.002, 99.0% ci=[11.192, 11.204])
sockperf: # dropped messages = 0; # duplicated messages = 0; # out-of-order messages = 0
sockperf: Summary: Round trip is 11.198 usec
sockperf: Total 48937 observations; each percentile contains 489.37 observations
sockperf: ---> <MAX> observation =   17.814
sockperf: ---> percentile 99.999 =   17.814
sockperf: ---> percentile 99.990 =   17.387
sockperf: ---> percentile 99.900 =   15.372
sockperf: ---> percentile 99.000 =   13.173
sockperf: ---> percentile 90.000 =   11.803
sockperf: ---> percentile 75.000 =   11.316
sockperf: ---> percentile 50.000 =   11.058
sockperf: ---> percentile 25.000 =   10.886
sockperf: ---> <MIN> observation =   10.260
```

### BlueField-2 MBF2H322A-AEEOT in ConnectX-6 Dx mode   
```
numactl --cpunodebind=0  /opt/sockperf/bin/sockperf pp -i 192.168.13.21 --client_ip 192.168.13.22 --increase_output_precision --full-rtt --sender-affinity=16 --receiver-affinity=17 -m 98
sockperf: == version #3.10-32.gitf12cc1b66338 == 
sockperf[CLIENT] send on:sockperf: using recvfrom() to block on socket(s)

[ 0] IP = 192.168.13.21   PORT = 11111 # UDP
sockperf: Warmup stage (sending a few dummy messages)...
sockperf: Starting test...
sockperf: Test end (interrupted by timer)
sockperf: Test ended
sockperf: [Total Run] RunTime=1.000 sec; Warm up time=400 msec; SentMessages=82991; ReceivedMessages=82990
sockperf: ========= Printing statistics for Server No: 0
sockperf: [Valid Duration] RunTime=0.550 sec; SentMessages=47935; ReceivedMessages=47935
sockperf: ====> avg-rtt=11.446 (std-dev=0.552, mean-ad=0.363, median-ad=0.303, siqr=0.216, cv=0.048, std-error=0.003, 99.0% ci=[11.440, 11.452])
sockperf: # dropped messages = 0; # duplicated messages = 0; # out-of-order messages = 0
sockperf: Summary: Round trip is 11.446 usec
sockperf: Total 47935 observations; each percentile contains 479.35 observations
sockperf: ---> <MAX> observation =   19.210
sockperf: ---> percentile 99.999 =   19.210
sockperf: ---> percentile 99.990 =   17.666
sockperf: ---> percentile 99.900 =   16.061
sockperf: ---> percentile 99.000 =   13.468
sockperf: ---> percentile 90.000 =   12.074
sockperf: ---> percentile 75.000 =   11.566
sockperf: ---> percentile 50.000 =   11.308
sockperf: ---> percentile 25.000 =   11.132
sockperf: ---> <MIN> observation =   10.390
```

### Xilinx Solarflare 2x10GbE SFP+ SFN8522-PLUS  
```
numactl --cpunodebind=0  /opt/sockperf/bin/sockperf pp -i 192.168.13.41 --client_ip 192.168.13.42 --increase_output_precision --full-rtt --sender-affinity=16 --receiver-affinity=17 -m 98
sockperf: == version #3.10-32.gitf12cc1b66338 == 
sockperf[CLIENT] send on:sockperf: using recvfrom() to block on socket(s)

[ 0] IP = 192.168.13.41   PORT = 11111 # UDP
sockperf: Warmup stage (sending a few dummy messages)...
sockperf: Starting test...
sockperf: Test end (interrupted by timer)
sockperf: Test ended
sockperf: [Total Run] RunTime=1.000 sec; Warm up time=400 msec; SentMessages=83505; ReceivedMessages=83504
sockperf: ========= Printing statistics for Server No: 0
sockperf: [Valid Duration] RunTime=0.550 sec; SentMessages=48398; ReceivedMessages=48398
sockperf: ====> avg-rtt=11.335 (std-dev=0.502, mean-ad=0.304, median-ad=0.274, siqr=0.187, cv=0.044, std-error=0.002, 99.0% ci=[11.329, 11.341])
sockperf: # dropped messages = 0; # duplicated messages = 0; # out-of-order messages = 0
sockperf: Summary: Round trip is 11.335 usec
sockperf: Total 48398 observations; each percentile contains 483.98 observations
sockperf: ---> <MAX> observation =   18.888
sockperf: ---> percentile 99.999 =   18.888
sockperf: ---> percentile 99.990 =   17.645
sockperf: ---> percentile 99.900 =   16.165
sockperf: ---> percentile 99.000 =   13.247
sockperf: ---> percentile 90.000 =   11.728
sockperf: ---> percentile 75.000 =   11.444
sockperf: ---> percentile 50.000 =   11.240
sockperf: ---> percentile 25.000 =   11.067
sockperf: ---> <MIN> observation =   10.378
```