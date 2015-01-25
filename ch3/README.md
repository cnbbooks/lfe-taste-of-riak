# Chapter 3 - Object Modeling

As a developer, you may be nonplussed to see that Secondary Indexes (2i) don't work with the Bitcask backend. You may also be wondering how to work with “sets” of data.

In this chapter, we'll cover how to deal with both, and in the next chapter we'll introduce some important distributed systems concepts such as
[siblings](http://docs.basho.com/riak/latest/theory/concepts/Vector-Clocks/#Siblings),
[eventual consistency](http://docs.basho.com/riak/latest/theory/concepts/Eventual-Consistency/), and
[sibling resolution](http://docs.basho.com/riak/latest/theory/concepts/Vector-Clocks/#Siblings)
that you'll need to know to become a pro Riak developer.

For the remainder of the tutorials, we will be implementing pieces of an internal messaging app. This app, codenamed “msgy” (we couldn't afford all the vowels) will provide “a way to incentivize long-tail watercooler networking for employees.” In short, it will allow us to post little snippets of text (msgs) to each other at work without all the hassle of email, while we block traditional social media.

At the data level, we only have a few objects to work with:

* ``user`` -- A user object, containing a ``user-name``, a ``full-name``, and an ``email`` address.
* ``msg`` -- A message that a user has sent, containing ``text``, ``sender`` and ``recipient`` addresses, and a ``created`` date.
* ``timeline`` -- A list of ``msgs``, containing an ``owner`` and a ``msg-type``. The type can be one of two things:
  * ``inbox``: A user's inbox of personal messages, or a group's public msg list.
  * ``sent``: A list of a user's sent messages.

The ``timeline`` objects could easily be implemented with a 2i query across the ``msg`` bucket, but in this chapter we'll show how to do it using only key/value operations.

