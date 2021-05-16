## AWS Load Balancer
Load balancers are used to forward internet traffic to different downstream server instances.

LBs sends health check requests to check if downstream instances are running ok.

### Types of LB
It's recommended to use the newer generation
- Classic Load Balancer (v1 - old generation) - 2009
- Application Load Balancer (v2 - new generation) - 2016
- Network Load Balancer (v2 - new generation) - 2017

There are internal (private) and external (public) ELBs

#### Classic LB (Deprecated)
1. One classic LB per application

#### Application LB (Layer 7)
1. Load balancing
    - to multiple HTTP applications across machines (target groups)
    - to multiple applications on the same machine (e.g. containers)
    - based on route in URL
    - based on hostname in URL
2. Great for micro services and container-based application
3. Has a port mapping feature to redirect to dynamic port

#### Network Load Balancer (Layer 4)
1. Load balancing
    - Forward TCP traffic to instances
    - Handle millions of request per seconds
    - Support for static IP or elastic IP
    - Less latency ~100 ms (vs. 400 ms for ALB)
    
2. Mostly used for extreme performance and should not be the default LB

### Good to know
- ALB for HTTP / HTTPS & Websocket
- NLB for TCP
- CLB and ALB support SSL certificates and provide SSL termination
- All LBs can do health check
- ALB can route on based on hostname / path
- ALB is a great fit with ECS (Docker)
- All LBs have static host name, do not resolve and user underlying IP (changes)
- LBs can scale but not instantaneously
- NLB directly sees the client IP
- If LB cannot connect to application, check security groups

