---
layout: post
title: "New release of clj-aws-ec2"
---

I have released version 0.2.0 of [clj-aws-ec2][]. This version contains no changes from 0.1.11. I'm just trying to adhere more closely to [semantic versioning][], having been fairly slack about it so far.

This version *does* however contain many changes since I last [mentioned it here]("/2012/11/introducing-clj-aws-ec2.html"). It can now describe, create and delete tags on resources, and create and deregister images (AMIs).

I consider this more or less "feature complete" for my current purposes. Of course, it only covers a very small fraction of the available [EC2 SDK][] but hopefully it is on the right side of the 80/20 rule. :-) I am open to feature requests&mdash;or even better [pull requests][]&mdash;for further elements of the API that you would like to see supported.

[clj-aws-ec2]: https://github.com/mrowe/clj-aws-ec2
[semantic versioning]: http://semver.org/
[EC2 SDK]: http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/index.html
[pull requests]: https://github.com/mrowe/clj-aws-ec2/fork_select