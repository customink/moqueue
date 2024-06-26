= Moqueue
Moqueue is a library for mocking the various objects that make up the ruby AMQP[http://github.com/tmm1/amqp] library. It allows you to use the AMQP library naturally and test your code easily without running an AMQP broker. If you want a higher level of control, you can use your favorite mocking and stubbing library to modify individual calls to MQ.queue and the like so that they return Moqueue's mock up versions. If you want to go all-in, you can tell Moqueue to overload the MQ and AMQP. This allows you to use MQ and AMQP as normal, while Moqueue works behind the scenes to wire everything together.

= Getting started

  require "moqueue"
  overload_amqp

  mq = AMQP::Channel.new
  => #<AMQP::Channel:0x00000100dd4f18>

  queue = mq.queue("mocktacular")
  => #<Moqueue::MockQueue:0x1194550 @name="mocktacular">

  topic = mq.topic("lolz")
  => #<Moqueue::MockExchange:0x11913dc @topic="lolz">

  queue.bind(topic, :key=> "cats.*")
  => #<Moqueue::MockQueue:0x1194550 @name="mocktacular">

  queue.subscribe {|header, msg| puts [header.routing_key, msg]}
  => nil

  topic.publish("eatin ur foodz", :key => "cats.inUrFridge")
  # cats.inUrFridge
  # eatin ur foodz

Note that in this example, we didn't have to deal with <tt>AMQP.start</tt> or <tt>EM.run</tt>. This should be ample evidence that you should run higher level tests without any mocks or stubs so you can be sure everything works with real MQ objects. With that said, <tt>#overload_amqp</tt> does overload the <tt>AMQP.start</tt> method, so you can use Moqueue for mid-level testing if desired. Have a look at the spec/examples directory to see Moqueue running some of AMQP's examples in overload mode for more demonstration of this.

= Custom Rspec Matchers
For Test::Unit users, Moqueue's default syntax should be a good fit with <tt>assert()</tt>:
  assert(queue.received_message?("eatin ur foodz"))
Rspec users will probably want something a bit more natural language-y. You got it:
  queue.should have_received("a message")
  queue.should have_ack_for("a different message")

= What's Working? What's Not?
As you can tell from the example above, quite a bit is working. This includes direct exchanges where you call <tt>#publish</tt> and <tt>#subscribe</tt> on the same queue, acknowledgements, topic exchanges, and fanout exchanges.

What's not working:
* RPC exchanges.
* The routing key matching algorithm works for common cases, including "*" and "#" wildcards in the binding key. If you need anything more complicated than that, Moqueue is not guaranteed to do the right thing.
* Receiving acks when using topic exchanges works only if you subscribe before publishing.

There are some things that Moqueue may never be able to do. As one prominent example, for queues that are configured to expect acknowledgements (the <tt>:ack=>true</tt> option), the behavior on shutdown is not emulated correctly.

== Hacking
Moqueue is at a stage where it "works for me." That said, there may be methods or method signatures that aren't correct/ aren't supported. If this happens to you, fork me and send a pull request when you're done. Patches and feedback welcome.

== Moar
I wrote an introductory post on my blog, it's probably the best source of high-level discussion right now. Visit: http://kallistec.com/2009/06/21/introducing-moqueue/

If you prefer code over drivel, look at the specs under spec/examples. There you'll find some of the examples from the amqp library running completely Moqueue-ified; the basic_usage_spec.rb shows some lower-level use.

As always, you're invited to git yer fork on if you want to work on any of these. If you find a bug that you can't source or want to send love or hate mail, you can contact me directly at dan@kallistec.com
