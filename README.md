# Distributed, Highly Available, FIFO Queue
Yesterday, we had a short discussion regarding our current Q mechanisms,
with the asertion that queues cannot be infinate and that they are not
scalable.

That got me thinking that it can't be that hard, can it. So I thought I'd
have a go at making a highly-available, distributed Q that might scale,
though I doubt if it will ever be capable of infinate length.

The following is designed with Amazon's AWS infrastructure in mind.  At the
moment the Q is scalable within a single Region, though is highly-available
within that Region as the queue data is held on multiple Data servers in
different Availability Zones.



## Architecture
* Central Q server (possibly, in a Master/Slave multiple)
  * The Q being a linked list with each member holding a reference to a Q data
  handler server (the amount of memory available to each member of the
  cluster will be the Q length limiter).
  * Each Q server in the cluster will need to be in the same VPC (and possibly
  within the same subnet).
* Multiple distributed Q data handlers with network access to the central Q
  server and each other.
  * The Q data handlers are also the Q incoming servers, though sitting
    behind an Elastic Load Balancer (which will handle the SSL termination
    and  distribution of Load). Only the ELB should be accessible from
    *outside* the private networks.
* Multiple, distributed as necessary, Q processor servers with network access
  to the Central Q server and the Data servers.

### A picture is worth a thousand cache hits
Here Q is the central Q server(s), D is the data servers, fronted by an
ELB, P is the Q processor and A is the Application.
![dhaq high level architecture](https://raw.githubusercontent.com/ccdale/dhaqari/master/qarchitectur.png
"dhaq high level architecture")

[modeline]: # ( vim: set fenc=utf-8 spell spl=en tw=76 ft=markdown: )
