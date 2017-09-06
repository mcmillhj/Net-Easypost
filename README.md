[![Build Status](https://travis-ci.org/mcmillhj/Net-Easypost.svg?branch=master)](https://travis-ci.org/mcmillhj/Net-Easypost)
[![Coverage Status](https://coveralls.io/repos/mcmillhj/Net-Easypost/badge.svg?branch=master)](https://coveralls.io/r/mcmillhj/Net-Easypost?branch=master)
[![Cpan license](https://img.shields.io/cpan/l/Net-Easypost.svg)](https://metacpan.org/release/Net-Easypost)
[![Cpan version](https://img.shields.io/cpan/v/Net-Easypost.svg)](https://metacpan.org/release/Net-Easypost)

# NAME

Net::Easypost - Perl client for the Easypost web service

# VERSION

version 0.21

# SYNOPSIS

    use Net::Easypost;

    my $to = Net::Easypost::Address->new(
       name    => 'Johnathan Smith',
       street1 => '710 East Water Street',
       city    => 'Charlottesville',
       state   => 'VA',
       zip     => '22902',
       phone   => '(434)555-5555',
       country => 'US',
    );

    my $from = Net::Easypost::Address->new(
       name    => 'Jarrett Streebin',
       phone   => '3237078576',
       city    => 'Half Moon Bay',
       street1 => '310 Granelli Ave',
       state   => 'CA',
       zip     => '94019',
       country => 'US',
    );

    my $parcel = Net::Easypost::Parcel->new(
       length => 10.0,
       width  => 5.0,
       height => 8.0,
       weight => 10.0,
    );

    my $customs_info = Net::Easypost::CustomsInfo->new(
        customs_signer   => 'Steve Brule',
        contents_type    => 'merchandise',
        restriction_type => 'none',
        customs_certify  => 1,
        eel_ppc          => 'NOEEI 30.37(a)',
        customs_items => [
            Net::Easypost::CustomsItem->new(
                code             => '111111',
                description      => 'T-Shirt',
                quantity         => 1,
                weight           => 5,
                value            => 10,
                hs_tariff_number => '123456',
                origin_country   => 'US',
            )
        ]
    );

    my $shipment = Net::Easypost::Shipment->new(
        to_address   => $to,
        from_address => $from,
        parcel       => $parcel,
        customs_info => $customs_info,
    );

    my $ezpost = Net::Easypost->new;
    my $label = $ezpost->buy_label($shipment, ('rate' => 'lowest'));

    printf("You paid \$%0.2f for your label to %s\n", $label->rate->rate, $to);
    $label->save;
    printf "Your postage label has been saved to '" . $label->filename . "'\n";

# OVERVIEW

This is a Perl client for the postage API at [Easypost](https://www.easypost.com/docs/api). Consider this
API at beta quality mostly because some of these library calls have an inconsistent input
parameter interface which I'm not super happy about. Still, there's enough here to get
meaningful work done, and any future changes will be fairly cosmetic.

Please note! **All API errors are fatal via croak**. If you need to catch errors more gracefully, I
recommend using [Try::Tiny](https://metacpan.org/pod/Try::Tiny) in your implementation.

API key:

You must have your API key stored in an environment variable named
EASYPOST\_API\_KEY

# ATTRIBUTES

## requester

HTTP client to POST and GET

# METHODS

## verify\_address

This method attempts to validate an address. This call expects to take the same parameters
(in a hashref) or an instance of [Net::Easypost::Address](https://metacpan.org/pod/Net::Easypost::Address), namely:

- street1
- street2
- city
- state
- zip
- country

You may omit some of these attributes like city, state if you supply a zip, or
zip if you supply a city, state.

This call returns a new [Net::Easypost::Address](https://metacpan.org/pod/Net::Easypost::Address) object.

Along with the validated address, the `phone` and `name` fields will be
copied from the input parameters, if they're set.

The verification works only for addresses in US. If you pass a country
other than US (the default), a warning will be issued, but the
[Net::Easypost::Address](https://metacpan.org/pod/Net::Easypost::Address) object will be returned.

## get\_rates

This method will get postage rates between two zip codes. It takes the following input parameters:

- to => an instance of [Net::Easypost::Address](https://metacpan.org/pod/Net::Easypost::Address)
- from => an instance of [Net::Easypost::Address](https://metacpan.org/pod/Net::Easypost::Address)
- parcel => an instance of [Net::Easypost::Parcel](https://metacpan.org/pod/Net::Easypost::Parcel)

This call returns an array of [Net::Easypost::Rate](https://metacpan.org/pod/Net::Easypost::Rate) objects in an arbitrary order.

## buy\_label

This method will attempt to purchase postage and generate a shipping label.

It takes as input:

- A [Net::Easypost::Shipment](https://metacpan.org/pod/Net::Easypost::Shipment) object
- A [Net::Easypost::Rate](https://metacpan.org/pod/Net::Easypost::Rate) object

It returns a [Net::Easypost::Label](https://metacpan.org/pod/Net::Easypost::Label) object.

## get\_label

This method retrieves a label from a past purchase. It takes the label filename as its
only input parameter. It returns a [Net::Easypost::Label](https://metacpan.org/pod/Net::Easypost::Label) object.

## list\_labels

This method returns an arrayref with all past purchased label filenames. It takes no
input parameters.

# SUPPORT

Please report any bugs or feature requests to [https://github.com/mcmillhj/Net-Easypost/issues](https://github.com/mcmillhj/Net-Easypost/issues)

# SEE ALSO

- [Easypost API docs](https://www.easypost.com/docs/api)

# AUTHOR

Mark Allen <mrallen1@yahoo.com>, Hunter McMillen <mcmillhj@gmail.com>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2012 by Mark Allen.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
