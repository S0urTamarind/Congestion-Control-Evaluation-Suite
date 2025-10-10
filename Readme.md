# Questions

- \[SECTION 5.1] RFC 5166 SECTION 2.1 mentions different ways of calculating Throughput, Latency and Packet Drop Rate (Router-Based, Flow-Based, User-Based), which one is suitable for a CCA eval tool?

- \[SECTION 5.1] RFC 5166 SECTION 2.2 mentions response time and minimum oscillations as a metric, what would be a suitable test to report these metrics on?

- Should number of packets dropped in a time frame be reported? (Packet drop rate is already being measured)

- Should one end device send multiple flows? Or multiple devices send one flow each? - [Short Flows](#short-flows)

- Should fairness be measured across flows or devices? (if a device sends multiple flows) - [Fairness within the Proposed Congestion Control Algorithm](#fairness-within-the-proposed-congestion-control-algorithm)

- What metrics should be used to measure fairness between flows with the same CCA and flows with different CCA? - [Existing General Purpose CC Algorithms](#existing-general-purpose-cc-algorithms)
  \[Jain’s Fairness, Product Measure, Epsilon Fairness, Max-Min Fairness, Minimum potential delay fairness, HARM fairness]

- What is a suitable value for epsilon in epsilon fairness? - [Fairness within the Proposed Congestion Control Algorithm](#fairness-within-the-proposed-congestion-control-algorithm)

- Should fairness be calculated vs time? - [Fairness within the Proposed Congestion Control Algorithm](#fairness-within-the-proposed-congestion-control-algorithm)

- Max-Min and Minimum Potential Delay Fairness are not CCA fairness metrics but are throughput allocation fairness metrics. Can we report them to show whether a throughput allocation resulted from a CCA and thus by extension a CCA metric. - [Fairness within the Proposed Congestion Control Algorithm](#fairness-within-the-proposed-congestion-control-algorithm)

- How long should a short flow last? - [Short Flows](#short-flows), [Short and Long Flows](short-and-long-flows)

- When should short flows start? - [Short Flows](#short-flows), [Short and Long Flows](short-and-long-flows)

- How many short and long flows should we consider? - [Short Flows](#short-flows), [Short and Long Flows](short-and-long-flows)

- Should the CCA in each (short and long) flow be configurable? Or a pair (1 for short + 1 for long) - [Short Flows](#short-flows), [Short and Long Flows](short-and-long-flows)

- How to go about testing Real-time congestion control? Apparently, RTCP is not implemented in the Linux kernel yet. - [Real-Time-Congestion-Control](#real-time-congestion-control)

- \[SECTION 5.2.1, SECTION 5.2.3] What CCA should we run on QUIC?

---

- \[SECTION 6.2] How should the CCA eval tool handle tunneling(VPN) and ECN working together?

---

- \[SECTION 7.2] Should this be a specific test ? Or we could see the value of packet drop ever goes above 10% ?

- Should we run the same tests but with varying delay or make a new testbed for this ? - [Link Configurations](#link-configurations)

- What metrics should we report for IoT environments (apart from CPU cycles and number of control packets) - [Link Configurations](#link-configurations)

- \[SECTION 7.4] What topologies should we use for IoT environments?

- Should we consider different satellite environments? If not what should be the default value ? - [Link Configurations](#link-configurations)

- What is a suitable packet reordering probability? Should we run the tests for a range of probabilities (0.1 - 0.3) ? - [Link Configurations](#link-configurations)

- What is a suitable packet reordering distance? Should we run the tests for a range of probabilities (12 - 45) ? - [Link Configurations](#link-configurations)

- What is a suitable packet reordering delay? - [Link Configurations](#link-configurations)

- \[SECTION 7.10] What topology should we use for MPTCP ?

- \[SECTION 7.11] Do we run the same tests for Data Center networks or make a new testbed or do both ? What topology should we use for Data Centers ?

# Test Configurations

## CC Algorithm
The CC Algorithm to be tested. (Mandatory)

## Link Configurations
Latency, Bandwidth, and Packet Drop rate depends on the environment such as: 

| Environment            | Bandwidth | RTT     | Packet Drop Rate | Packet Reordering Probability | Packet Reordering Distance |
| ---------------------- | --------- | ------- | ---------------- | ----------------------------- | -------------------------- |
| Wired Path             | 1 Gbps    | 1 ms    | 0 %              |                               |                            |
| Wireless Path          | 1 Gbps    | 12 ms   | 0.0477 %         |                               |                            |
| High Delay / Satellite |           | 500 ms  |                  |                               |                            |
| Data Center            | 10 Gbps   | 0.25 ms | 0 %              |                               |                            |
| IOT                    |           |         |                  |                               |                            |

*The values mentioned above are per link.*

These are preset values, and can be configured by the user if necessary.

## Queue Management Algorithms
Default is FQ_CoDel, but can be configured by the user.

## Number of End Devices
Some tests have multiple end devices on either side of the bottleneck. The default is 2, but can be configured by the user.

# Metrics
## Performance Metrics
### cwnd vs Time 
 - Congestion Window as a function of time.

### Sending Rate vs Time
 - Sending Rate as a function of time.
 - Same as cwnd vs Time during low Congestion.

### Throughput vs Time
 - Router-based: Measured as the aggregate utilization of a link.
 - Flow-based: Measured by the transfer times of individual connections.
 - User-based: Measured by metrics like user wait times or application-specific utility functions

### ssthresh vs Time
 - slow start threshold as a function of time.

### Latency vs Time
 - Router-based: Focusing on Queueing delay over time.
 - Flow-based: Per-packet transfer time.
 - User-based: Total per-packet delay seen by an application including any time spent waiting at the sender and potential delays from retransmitting lost packets

### Number of packet drops vs time
 - Packet loss/Mark rate: raw rate of packets lost or marks by ECN.
 - Congestion event rate: groups one or more lost/marked packets within a single roundtrip into a congestion event.
 - Burst metrics for RTP:
    - burst density: the fraction of packets in bursts
    - gap density: the fraction of packets in the gaps between bursts
    - burst duration: the mean duration of bursts in seconds
    - gap duration: the mean duration of gaps in seconds

### Flow completion Time vs Time
 - The total duration from the moment the first packet of a data flow is sent to the moment the last packet is received

### Qlength vs Time
 - This measuers the number of packets waiting in a buffer (queue) at any given point in time, indicating the level of network congestion.

### Qdelay v​​s Time
 - This measures the time a packet spends waiting in a queue before being processed, illustrating the latency introduced by buffering over time.

### QCapacity vs Time
 - This shows the maximum size or packet-holding capacity of the queue over time.

## Fairness Metrics
### Jain's Fairness vs Time
 - It measures the fairness of resource allocation among a number of users.
 - The index ranges from 0 to 1, where 1 represents a perfectly fair allocation in which all users receive the same share.
 - For n users, where x_i is the throughput for the i-th connection, the index is calculated.

![](./assets/Jain's.png)


### Product Measure vs Time 
 - Product of individual connection throughputs to evaluate fairness.
 - This measure is particularly sensitive to segregation, as the entire product becomes zero if any single connection receives zero throughput.

### Epsilon Fairness
 - It is Based on the worst-case ratio between flow rates
 - A rate allocation is considered epsilon-fair if the ratio of the minimum throughput to the maximum throughput is at least 1−ϵ.

### HARM Index 
 - Measueres the negative impact a new Congestion Control Algorithm (CCA) has on the performance of existing, deployed CCAs.
 - Harm is measured on a scale from 0 (harmless) to 1 (maximally harmful).
 - Compare a flow's performance when running alone (solo performance, x) to its performance with a competitor (y).

### Max-Min Fairness
 - This criterion aims to make the smallest throughput rate as large as possible. After this is achieved, the next-smallest rate is made as large as possible, and so on.
 - This approach gives "absolute priority to the smallest flows"

### Minimum Potential Delay Fairness
 - This metric is considered a model for TCP's behavior and acts as a compromise between max-min and proportional fairness.
 - An allocation meets this standard if it minimizes the sum of the inverse of each flow's throughput
 - This is equivalent to minimizing the average download time if all flows were transferring equal-sized files.
# Test Cases

## Single Algorithm Behavior
All the tests below will use only the new CC algorithm. Values such as delay and bandwidth, which qdisc to use, etc will depend on the environment (which is an input parameter).

### Protection against Congestion Collapse
![](./assets/CongestionCollapse.drawio.png)

This test is to see whether the sender will backoff when experiencing high packet drop rates (above 30% according to RFC 3714). Initially, there won’t be any packet drop for a few seconds and then the packer drop is set to 30% in the bottleneck.

Metrics (for sender): 
- cwnd vs Time
- Throughput vs Time
- Sending rate vs Time
- ssthresh vs Time
    
### Protection against Bufferbloat
![](./assets/LowBandwidth.drawio.png)

This test is to see if the new CC algorithm can solve the problem of Bufferbloat without the help of AQMs. A FIFO queue should be used everywhere. Low bandwidth of bottleneck will cause the queues to fill up.

Metrics (for sender):
- Latency vs Time
- Throughput vs Time
- Flow completion time
- ssthresh vs Time
- cwnd vs Time
- Sending rate vs Time
    
Metrics (for router) : 
- Qlength vs Time
- Qdelay v​​s Time
- QCapacity vs Time 

### Protection against High Packet Loss
![](./assets/LowBandwidth.drawio.png)

This test is to see if the new CC algorithm reduces its sending rate when facing High Packet Loss. Similar to the above test, the only difference is that AQMs can be used now. 

Metrics (for sender) : 
- Latency vs Time
- Throughput vs Time
- Flow completion time
- ssthresh vs Time
- cwnd vs Time
- Sending rate vs Time
    
Metrics (for router) : 
- Qlength vs Time
- Qdelay v​​s Time
- QCapacity vs Time

### Fairness Within the Proposed Congestion Control Algorithm
![](./assets/MultipleEndDevices.drawio.png)

This test is to see if the new CC algorithm can be fair to each other. The number of end devices can be set.

Metric (fairness): 
- Jain’s fairness
- Product measure
- Epsilon fairness
- [Harm index](https://www.irtf.org/anrp/IETF109-ANRP-Ware.pdf)
    
Metrics (for sender): 
- Latency vs Time
- Throughput vs Time
- Flow completion time
- ssthresh vs Time
- cwnd vs Time
- Sending rate vs Time
    
### Short Flows
![](./assets/MultipleEndDevices.drawio.png)

One primary sender will be a long flow, while the other will join later as a short flow (i.e. flows that terminate while in the “slow start” phase).

Metric (fairness) - btw short and long flows: 
- Jain’s fairness
- Product measure
- Epsilon fairness
- [Harm index](https://www.irtf.org/anrp/IETF109-ANRP-Ware.pdf) 
    
Metrics (for sender):  
- Latency vs Time
- Throughput vs Time
- Flow completion time
- ssthresh vs Time
- cwnd vs Time
- Sending rate vs Time
    
Metrics (for router) : 
- Qlength vs Time
- Qdelay v​​s Time
- QCapacity vs Time

## Mixed Algorithm Behavior

### Existing General Purpose CC Algorithms
![](./assets/MultipleEndDevices.drawio.png)

Similar to the “Fairness within Proposed CC Algorithm” test. The only difference is to use end devices with different CC algorithms (such as Reno, Cubic, BBR, etc) along with the new CC algorithm. 

Metric (fairness):  
- [Harm index](https://www.irtf.org/anrp/IETF109-ANRP-Ware.pdf)
    
Metrics (for sender): 
- Latency vs Time
- Throughput vs Time
- Flow completion time
- ssthresh vs Time
- cwnd vs Time
- Sending rate vs Time
    
Metrics (for router): 
- Qlength vs Time
- Qdelay v​​s Time
- QCapacity vs Time 
    
### Real-Time Congestion Control
![](./assets/MultipleEndDevices.drawio.png) 

Similar to the above test, but one of the sender runs a real-time congestion control algorithm.

### Short and Long Flows
![](./assets/MultipleEndDevices.drawio.png)

Same as the “Short Flows” test, but this time the short and long flows should be the new CC algorithm and existing CC algorithms and vice versa.

Metric (fairness): 
- [Harm index](https://www.irtf.org/anrp/IETF109-ANRP-Ware.pdf) 
    
Metrics (for sender):  
- Latency vs Time
- Throughput vs Time
- Flow completion time
- ssthresh vs Time
- cwnd vs Time
- Sending rate vs Time
    
Metrics (for router) : 
- Qlength vs Time
- Qdelay v​​s Time
- QCapacity vs Time
