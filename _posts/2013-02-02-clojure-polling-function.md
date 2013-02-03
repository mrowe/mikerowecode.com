---
layout: post
title: A simple polling function in Clojure
---

One of my projects at work is to build an internal web service around [AWS][] to support our internal tooling. (This led to the development my [clj-aws-ec2][] library).

The web service needs "integration" tests that exercise its RESTful API to manipulate AWS resources (i.e. create instances, add tags, etc.). This sort of testing is fraught for many reasons and should be kept to a minimum, but it does provide a bit of an assurance that the service will actually respond to its published interface when deployed.

One of the reasons this sort of testing is fraught is that it depends on an external service that is beyond our control (i.e. AWS). Many things can go wrong when talking to AWS, and everything takes time. So my test needs to invoke the service to perform an action, then wait until the expected state is achieved (or a timer elapses causing the test to fail). What I'd like to be able to write is something like:

{% highlight clojure %}
(deftest ^:integration instance-lifecycle
  (testing "create instance"
 
    (def result (POST "/instances" (with-principal {:name "rea-ec2-tests/int-test-micro", :instance-type "t1.micro"})))
    (has-status result 200)
 
    (let [id (first (:body result))]
      (prn (str "Created instance " id))
 
      (testing "get instance"
        (has-status (GET (str "/instances/" id)) 200)
        (is (wait-for-instance-state id "running")))
 
      (testing "stop instance"
        (has-status (PUT (str "/instances/" id "/stop")) 200)
        (is (wait-for-instance-state id "stopped")))
 
      (testing "start instance"
        (has-status (PUT (str "/instances/" id "/start")) 200)
        (is (wait-for-instance-state id "running")))
 
      (testing "delete instance"
        (has-status (DELETE (str "/instances/" id)) 200)
        (is (wait-for-instance-state id "terminated"))))))
{% endhighlight %}

But how do you write a polling loop in Clojure? A bit of clicking around on Google led me to a function written by [Chas Emerick][] for his [bandalore][] library:

{% highlight clojure %}
;; https://github.com/cemerick/bandalore/blob/master/src/main/clojure/cemerick/bandalore.clj#L124
(defn polling-receive
  [client queue-url & {:keys [period max-wait]
                       :or {period 500
                            max-wait 5000}
                       :as receive-opts}]
  (let [waiting (atom 0)
        receive-opts (mapcat identity receive-opts)
        message-seq (fn message-seq []
                      (lazy-seq
                        (if-let [msgs (seq (apply receive client queue-url receive-opts))]
                          (do
                            (reset! waiting 0)
                            (concat msgs (message-seq)))
                          (do
                            (when (<= (swap! waiting + period) max-wait)
                              (Thread/sleep period)
                              (message-seq))))))]
    (message-seq)))
{% endhighlight %}

That seems pretty close! I generalised it a bit to remove dependencies on Chas's messaging routines and just take a predicate function:

<script src="https://gist.github.com/4689005.js"></script>

Finally, a couple of helper functions to tie it all together and enable the tests to be written as above:

{% highlight clojure %}
(defn get-instance-state [id] (:state (:body (GET (str "/instances/" id)))))
(defn wait-for-instance-state [id state] (wait-for #(= (get-instance-state id) state)))
{% endhighlight %}

There's a couple of improvements that could be made to `wait-for`, the most obvious being to use a "wall clock" for the timeout. The current implementation will actually wait for `timeout + (time-to-evaluate-predicate * number-of-invocations)` which is probably not what you want, especially when the predicate could take a non-trivial amount of time to evaluate because it is invoking an external service.

Comments and improvements welcome!

__UPDATE__: My colleague [Eric Entzel][] pointed out that there is no need to use an atom to store and update the "waiting" counter, its state can just be passed around with function invocations (and recursion). The above gist has been simplified to reflect this observation.

__UPDATE__: Even better, when I went to implement the "wall clock" timeout, I realised there is no need to maintain any state at all, since the absolute timeout time can be calculated up front and compared to the system clock on each evaluation. Gist updated again.

[AWS]: http://aws.amazon.com/
[clj-aws-ec2]: https://github.com/mrowe/clj-aws-ec2
[Chas Emerick]: http://cemerick.com/
[bandalore]: https://github.com/cemerick/bandalore
[Eric Entzel]: http://www.ubermac.net
