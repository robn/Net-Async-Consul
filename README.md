[![Build Status](https://secure.travis-ci.org/robn/Net-Async-Consul.png)](http://travis-ci.org/robn/Net-Async-Consul)

# NAME

Net::Async::Consul - Make async calls to Consul via IO::Async

# SYNOPSIS

    use IO::Async::Loop;
    use Net::Async::Consul;

    my $loop = IO::Async::Loop->new;
    
    my $kv = Net::Async::Consul->kv;

    # do some blocking op to discover the current index
    $kv->get("mykey", cb => sub { 
      my ($v, $meta) = @_;
    
      # now set up a long-poll to watch a key we're interested in
      $kv->get("mykey", index => $meta->index, cb => sub {
        my ($v, $meta) = @_;
        say "mykey changed to ".$v->value;
        $loop->stop;
      });
    });
    
    # make the change
    $kv->put("mykey" => "newval");
    
    $loop->run;

# DESCRIPTION

Net::Async::Consul is a thin wrapper around [Consul](https://metacpan.org/pod/Consul) to connect it to
[Net::Async::HTTP](https://metacpan.org/pod/Net::Async::HTTP) for asynchronous operation.

It takes the same arguments and methods as [Consul](https://metacpan.org/pod/Consul) itself, so see the
documentation for that module for details. The important difference is that you
must pass the `loop` option with the loop object to API methods, the `cb`
option to the endpoint methods to enable their asynchronous mode.

There's also a `on_error` argument. If you pass in a coderef for this
argument, it will be called with a single string arg whenever something goes
wrong internally (usually a HTTP failure). Use it to safely log or cleanup
after the error.

# SUPPORT

## Bugs / Feature Requests

Please report any bugs or feature requests through the issue tracker
at [https://github.com/robn/Net-Async-Consul/issues](https://github.com/robn/Net-Async-Consul/issues).
You will be notified automatically of any progress on your issue.

## Source Code

This is open source software. The code repository is available for
public review and contribution under the terms of the license.

[https://github.com/robn/Net-Async-Consul](https://github.com/robn/Net-Async-Consul)

    git clone https://github.com/robn/Net-Async-Consul.git

# AUTHORS

- Rob N ★ <robn@robn.io>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2018 by Rob N ★.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
