# Break the fake modularity of Jboss Fuse ESB, using Spring Boot and Cloud Foundry

Fuse  ESB and Apache Karaf came out with the promise of decomposing the monolith with it’s modularity. And it was an excellent concept, specially when in Jboss Application Server aka EAP, we were building larger than life EARs and WARs.  Fuse  ESB uses  [OSGi technology](http://www.osgi.org/what-is-osgi/), which is a set of [_specifications_](https://www.osgi.org/developer/specifications/) that define a dynamic component system for Java. These specifications enable a development model where an application is composed of several components which are packaged in _bundles_. Components communicate locally and across the network through _services_. This helped in bringing modularity to the applications co-located in a JVM. 

But the adoption of OSGi specification in mainstream products was as late as inception of Cloud Foundry and the era of containerization and microservices.

Let’s look at the brief history of  Fuse  ESB.  Fuse  was originally built by  [LogicBlaze](https://www.infoworld.com/article/2654841/applications/logicblaze-s-open-source-fuse-soa-stack-debuts.html) , and was later acquired by [IONA Technologies](https://www.zdnet.com/article/iona-buys-open-source-specialist-logicblaze/) in 2007. 

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Remembering ... 2007: &quot;IONA acquires LogicBlaze, supporters of ActiveMQ and ServiceMix ESB&quot; ::: <a href="http://t.co/u6MotUTMfa">http://t.co/u6MotUTMfa</a> <a href="https://twitter.com/hashtag/SOA?src=hash&amp;ref_src=twsrc%5Etfw">#SOA</a> <a href="https://twitter.com/hashtag/JBoss?src=hash&amp;ref_src=twsrc%5Etfw">#JBoss</a></p>&mdash; Strategy&amp;Process :: DevOps 360° ↂ IEXP DACH (@AdviseQ) <a href="https://twitter.com/AdviseQ/status/431169338486190080?ref_src=twsrc%5Etfw">February 5, 2014</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

IONA was acquired by [Progress Software](http://investors.progress.com/news-releases/news-release-details/progress-software-corporation-acquire-iona-technologies) in 2008, and the open-source group was later spun out into its own entity, FuseSource Corporation. 

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Last week, IONA shareholders voted their agreement for the acquisition of IONA Technologies by Progress Software</p>&mdash; John A. Stewart (@johncorpcomms) <a href="https://twitter.com/johncorpcomms/status/906716718?ref_src=twsrc%5Etfw">September 2, 2008</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

In June 2012, [Red Hat](https://www.redhat.com/en/about/press-releases/red-hat-to-acquire-fusesource) Inc. announced that it had acquired FuseSource from Progress Software. Following the acquisition,  Fuse ESB was rebranded as Red Hat JBoss  Fuse. The mainstream popularity of Fuse ESB was driven by this acquisition.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Red Hat acquires FuseSource (<a href="https://twitter.com/sjvn?ref_src=twsrc%5Etfw">@sjvn</a> / ZDNet) <a href="http://t.co/TGiVTsTo">http://t.co/TGiVTsTo</a> <a href="http://t.co/dy4MU3Ik">http://t.co/dy4MU3Ik</a></p>&mdash; Techmeme (@Techmeme) <a href="https://twitter.com/Techmeme/status/218433905236250624?ref_src=twsrc%5Etfw">June 28, 2012</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

On the other hand, Cloud Foundry was conceived in 2009. It was designed and developed by a small team at VMware led by Derek Collison and was originally called Project B29. The announcement of Cloud Foundry took place in  [April 2011](https://techcrunch.com/2017/06/18/cloud-foundry-makes-its-mark-on-the-enterprise/)  along with it’s initial release. 

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Cloud Foundry makes its mark on the enterprise <a href="https://t.co/oeiZe4tOTE">https://t.co/oeiZe4tOTE</a></p>&mdash; TechCrunch (@TechCrunch) <a href="https://twitter.com/TechCrunch/status/876536267261050880?ref_src=twsrc%5Etfw">June 18, 2017</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Even though history of containers dates back to  [1979](https://content.pivotal.io/infographics/moments-in-container-history), it was in 2006 when the real momentum started with Google’s process containers.  In 2008 Linux Containers was formed and by 2011 era of containerization had already begun with Cloud Foundry.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Moments In Container History <a href="http://t.co/jCUZpbxLbd">http://t.co/jCUZpbxLbd</a>  <a href="http://t.co/TNNgNAKGol">pic.twitter.com/TNNgNAKGol</a></p>&mdash; Pivotal (@pivotal) <a href="https://twitter.com/pivotal/status/547105552220684290?ref_src=twsrc%5Etfw">December 22, 2014</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

With the advent of containerization and microservices,  Fuse ESB’s promise of modularization in JVM falls short. Some of the main shortfalls of Fuse ESB are:

-   **Tight Coupling:**  Applications are tightly coupled at run-time environment, even though applications are decoupled through bundle
-   **No Isolation:**  Applications share same JVM resources; thus requires extra analysis before adding an application to ensure that JVM resources are not overly consumed by one application. This gives rise to a possibility of an application using too much resources while starving other application
-   **Noisy Neighbor:**  A problem application can bring down whole Fuse ESB environment; thus introducing run-time risk associated with other application
-   **Interdependent Lifecycle:**  If a Fuse ESB environment needs recycle, this impacts all the application in that environment

But don’t worry if you have invested a lot of hours in Apache Camel Routes and Enterprise Integration Patterns, you can still break away from  Fuse ESB monolith and make Apache Camel apps into, *'microservices'* with the help of Spring Boot and Cloud Foundry.

To do this, we will have to bootstrap camel context with Spring Boot and bring any cross bundle OSGi service calls within the context.
