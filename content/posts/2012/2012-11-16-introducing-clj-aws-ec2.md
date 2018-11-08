---
layout: post
title: Introducing clj-aws-ec2
date: "2012-11-16"
url: "/2012/11/introducing-clj-aws-ec2.html"
---

We use Amazon's [AWS][] quite heavily at work, and part of my job
involves building internal tools that wrap the public AWS API to
provide customised internal services.

I am building some of these tools in [Clojure][], and I needed a way
to call the Amazon API. Amazon provide a [Java SDK][] so it's a fairly
simple matter to wrap this in Clojure. In fact James Reeves had
already done so for the [S3 API][]. So I took his good work and
adapted it to work with the EC2 components of the API:

[https://github.com/mrowe/clj-aws-ec2](https://github.com/mrowe/clj-aws-ec2)

The library tries to stay true to Amazon's official Java SDK, but with
an idiomatic Clojure flavour. In particular, it accepts and returns
pure Clojure data structures (seqs of maps mostly). For example:

{{< highlight clojure >}}
user=> (require '[aws.sdk.ec2 :as ec2])
user=> (def cred {:access-key "..." :secret-key "..."})
user=> (ec2/describe-instances cred (ec2/instance-id-filter "i-b3385c89"))

({:instances
    ({:id "i-b3385c89",
      :state {:name "running",
              :code 272},
      :type "t1.micro",
      :placement {:availability-zone "ap-southeast-2a",
                  :group-name "",
                  :tenancy "default"}, 
      :tags {:node-name "tockle",
             :name "mrowe/tockle",
             :environment "mrowe"},
      :image "ami-df8611e5",
      :launch-time #<Date Tue Nov 13 08:23:09 EST 2012>}),
  :group-names (),
  :groups ({:id "sg-338f1909", :name "quicklaunch-1"})})

{{< /highlight >}}

This is still a work in progress. So far, you can describe instances
and images, and stop and start EBS-backed instances. I plan to work on
adding create/terminate instances next.

[AWS]: http://aws.amazon.com/
[Clojure]: http://clojure.org/
[Java SDK]: http://aws.amazon.com/sdkforjava/
[S3 API]: https://github.com/weavejester/clj-aws-s3

__UPDATE__: I just released [v0.1.6] which includes `run_instance` and
`terminate_instance` support.

[v0.1.6]: https://github.com/mrowe/clj-aws-ec2/tree/0.1.6
