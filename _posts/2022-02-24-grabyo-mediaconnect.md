---
layout: post
title: "Grabyo: Using AWS Elemental MediaConnect for the next generation of video streaming"
date: 2022-02-24 00:00:00
---

**_The following article is a blog post I wrote for Grabyo and is a copy of the published article kept here as a reference. You can find the orginal article on [Grabyo's Engineering Blog][]_**.


Live event and broadcast production in the cloud have critical aspects for their success:

![][eventBroadcastSuccessFactor]

At Grabyo, our goal to be the leaders of live events and broadcast production in the cloud requires continual innovation and adoption of services to complement our vision. Now, more than ever, the rise of remote working and utilisation of SaaS (Software-as-a-Service) solutions rather than physical hardware for event production has led to greater demand on Grabyo and its infrastructure ecosystem.

The rapid evolution of the video domain in this century (with the introduction of HD and extensions of it – QHD, 4K, 8K) has been matched with the evolution of technology for transporting and streaming video in different protocols over the internet (e.g. RTMP, HLS, RIST, SRT, Zixi, RTP, MPEG-DASH) as alternatives to transmitting videos over satellite networks or fibre connections. This evolution can be illustrated in this timeline:

![][protocolTimeline]

The usage of these protocols has validated this expansion by media streaming organisations, and their growing popularity must be accommodated for and supported in live video processing platforms, including Grabyo.

Our partnership with Amazon Web Services for its [AWS Media and Entertainment Initiative][] has provided us with the opportunity to integrate with its media-based services. For enhanced video streaming and data transmission, we have integrated with [AWS Elemental MediaConnect][] to offer us the ability to use multiple protocols. As a result, we have scalable streams in and out of the Grabyo platform. This provides fundamental enhancements to the underlying workflow in Grabyo’s architecture.


## Streaming video at Grabyo ##

### Streaming Protocols ###

Grabyo uses RTMP and HLS protocols for streaming video in (**_ingest_**) and out (**_egress_**) of the platform.

RTMP only supports streaming of FLV (Flash Video) formats and, whilst widely used, is a deprecated protocol since [Adobe’s end of life announcement for Flash at the end of 2020][]. This announcement means that RTMP is expected to fade into obsolescence over the next few years, and therefore eventual migration from this protocol is paramount.

HLS is a newer protocol developed by Apple and has widespread use in web browsers, mobile devices, and streaming media servers. It is an adaptive bitrate streaming protocol, providing multiple streams of the same video, encoded at different bit rates, collated and presented through M3U playlists. It allows videos to be seamlessly streamed over varying network conditions, without user impact, but at a variable quality that can be noticeable.

With both protocols, underlying usage of the TCP communication protocol limits the scalability of video transmission, resulting in lower bit rates and higher latency of delivery. Latency issues can impact our customers – for example, during live event/broadcast production, communication and coordination between the production team and camera operators can be delayed enough to cause observable latency effects.

### Infrastructure and scaling management ###

Ingest, and egress of videos are handled through bespoke processes we have developed, deployed and operated on Amazon EC2. These act as the gateway for videos to segue in and out of the Grabyo platform. We own and manage this workflow orchestration, routing video data, and scaling EC2 instances to handle request and performance demands. The overall maintenance and workflow coordination ownership is a layer of complexity that requires effort to support and operate effectively.

Here is a high-level architecture and end-to-end flow depicting our model:

![][grabyoIngestEgress]


## Why MediaConnect? ##

There are numerous benefits for Grabyo’s integration with MediaConnect:

- **Multiple protocol support** – Zixi, RIST, SRT and RTP. Provides the ability to ingest in one protocol and transmit in another  
- **Stream transmission in high bitrates** – up to 120 Mb/s per stream  
- **Negligible latency** – all these protocols use the UDP communication protocol, allowing transmission at extremely low latencies, less than 100 milliseconds.  
- **Serverless scaling paradigm** – The underlying infrastructure management, provisioning, scaling and distribution for performance are all handled by AWS. This results in the devolution of responsibility for existing maintenance. Given we are currently responsible for scaling infrastructure, services and coordinating data flow, these would be abstracted and handled by MediaConnect itself, which will simplify and alleviate this burden.  
- **Multiple distribution destinations for a single stream** – up to 50 destinations  
- **Stream failover support** – 2 source streams can be used for high availability of ingests  
- **API and UI based configuration** – an ability to build bespoke software by utilising an AWS provided API  
- **Ease of extensibility and maintenance** – as MediaConnect adds new features and protocols, our work to integrate and use those will be more straightforward, making adoption of enhancements easier.  
- **Integration with existing AWS account and features** – monitoring metrics, health checks, encryption, security  


## How MediaConnect is used at Grabyo ##

MediaConnect is the engine of Grabyo’s **_Pro Ingest_** and **_Pro Egress_** offering, which is now the next generation of the video streaming functionality we provide our customers for transporting video in and out of Grabyo. This enhances the features and capabilities of Grabyo and simplifies our underlying infrastructure architecture.

Our vision to disrupt and evolve the traditional methods of broadcasting and production of live events will strengthen, enabling our **_[Control Room][]_** feature to fulfil its potential. Our customers will now be able to provide multiple high-quality streams with negligible latency, providing a real-time interaction, feedback and feel, equivalent to in-person production control rooms. This is a giant leap to validate the viability of remote broadcast production over the internet via a web-based service operating in the cloud.

Cutting edge developments in the video domain can be adopted into our products more quickly – for example, SRT and RIST are under active development and will provide newer features. Some of the effects are that the latest video codecs can be applied to video streams, such as H.265. The limiting and ageing of the H.264 codec in RTMP will hinder over time and will eventually be disinvested. The utilisation of MPEG Transport Streams (MPEG-TS) in these protocols paves the way for us to add additional features in the future, including support for multiple audio tracks and reading/inserting broadcast metadata such as SCTE ad markers.

Compared to the above, our infrastructure architecture is more straightforward. Many of the technical and operational factors we had to cater for are now abstracted away into MediaConnect – Workflow orchestration, coordination and scaling are all handled out-of-the-box with MediaConnect. Our architecture and workflow now can be illustrated as below:

![][grabyoIngestEgressMediaConnect]

With this simplification, we can now channel our focus and efforts into purely Grabyo product development, confident in the AWS foundations and backbone we rely on. We are excited about the possibilities this presents and are very much looking forward to pioneering the next generation of a sophisticated cloud-based production suite!

[Grabyo's Engineering Blog]: https://about.grabyo.com/us/next-gen-video-streaming-aws/
[eventBroadcastSuccessFactor]: /images/event-broadcast-production-cloud.png
[protocolTimeline]: /images/protocol-timeline.png
[AWS Media and Entertainment Initiative]: https://about.grabyo.com/us/aws-grabyo/
[AWS Elemental MediaConnect]: https://aws.amazon.com/mediaconnect/
[Adobe’s end of life announcement for Flash at the end of 2020]: https://blog.adobe.com/en/publish/2017/07/25/adobe-flash-update.html
[grabyoIngestEgress]: /images/grabyo-ingest-egress-infrastructure.png
[Control Room]: https://about.grabyo.com/us/grabyo-producer/control-room/
[grabyoIngestEgressMediaConnect]: /images/grabyo-ingest-egress-mediaconnect-infrastructure.png