# NAME

Dancer2::Plugin::ProbabilityRoute - plugin to define behavior with probability matching rules

# VERSION

version 0.03

# SYNOPSIS

    package myApp;
    use Dancer2;
    use Dancer2::Plugin::ProbabilityRoute;

    # a basic A/B test (50/50 chances)
    get '/test' => probability
        50 => sub {
            "A is returned for you";
        },
        50 => sub {
            "B is returned for you";
        }
    };

    1;

# DESCRIPTION

This plugin is designed to let you write a Dancer2 application with routes that
match under a given probability for a given user.

This could be used to build A/B Testing routes, for testing the website user's
behavior when exposed to version A or B of your website.

But it can be used to do more, as you can split a route into as many versions
as you like up to 100.

The decision to assign a given version of the route to a user is stable in time,
for a given user. It means a given user will always see the same version of the
route as long as they don't purge their cookies.

# METHODS

## probability\_route

Use this keyword to declare a route that is devided into multiple versions,
each them triggered for a certain percentage of users.

The sequence is important: the first declaration is the default version of
the route (if the user has no cookies).

Here is an example of a 30, 50, 20 split:

    get '/test' => probability
        30 => sub {
            "30% of users see that.";
        },
        50 => sub {
            "50% of users see that.";
        },
        20 => sub {
            "20% of users see that.";
        },
    };

To provide stability for each user, the session ID is used as a pivot, to build
a _user\_score_, which is an number between 0 and 99.

That number can also be used in regular routes or templates to create your own
conditions. See `probability_user_score` for details.

Note that the sum of all the probability\_route statements must equal 100. A
validation is made when the plugin processes all the declarations, and croaks
if it's not the case.

## probability\_user\_score

Use this keyword to fetch the current user's score used to pick wich version
of the route are chosen. It can be handy if you wish to define your own
conditional branches.

    get '/someroute' => sub {}
        my $score = probability_user_score;
        if ($score < 50) {
            do_that();
        }
        else {
            do_this();
        }
    };

# ACKNOWLEDGEMENTS

This module has been written during the
[Perl Dancer 2015](https://www.perl.dance/) conference.

[Fabrice Gabolde](https://metacpan.org/author/FGA) contributed heavily to the
design and helped me make this module so easy to write it took less than half
a day to get it into CPAN.

The second release was made thanks to the observations of
[Russell Jenkins](http://search.cpan.org/~russellj/) who suggested a better API,
allowing for a more straight-forward approach.

# AUTHOR

Alexis Sukrieh <sukria@sukria.net>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2015 by Alexis Sukrieh.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
