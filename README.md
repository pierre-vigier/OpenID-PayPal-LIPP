# NAME

OpenID::PayPal::LIPP - Login with PayPal 

# SYNOPSIS

    use OpenID::PayPal::LIPP;

    my $lipp = OpenID::PayPal::LIPP->new(
        client_id => 'CLIENT_ID',
        client_secret => 'CLIENT_SECRET',
        account => 'ACCOUNT',
        mode => 'sandbox',
        redirect_uri => 'http://localhost/callback',
    );

    my $url = $lipp->login_url();

    # user after visiting that url, user will be redirected to http://localhost/callback with a parameter code
    # you can either directly the user info with the code, or exchange code for access token

    my $token = $pp->exchange_code( $code );

    #$token contain an access token and a refresh token
    #to get a new access token from refresh token :
    $token = $pp->refresh_token( $token->{refresh_token} );

    #to get user info, you can either give access_token, or authorization code
    my $user_info = $pp->get_user_details( access_token => $token->{access_token} );
    my $user_info = $pp->get_user_details( authorization_code => $code );

    print $user_info->email

# DESCRIPTION

OpenID::PayPal::LIPP is a simple implementation for the Login with PayPayl API.

## METHODS

Following methods are available

### new( options )

Constructor take the following required arguments:

- client\_id: client id of your paypal application
- client\_secret: client secret of your paypal application
- account: account linked to your paypal application
- redirect\_uri: redirection url registered in your paypal application, user will be redirected there after login in PayPal website
- mode: sandbox or live

You have some more control with the following optionnal argument:

- scope: arrayref of the scope you want to get access to, default is \[ 'openid', 'email' \]
- logger: coderef to a sub used to log some internal debug messages, see logger section

### login\_url( state ), state is optional

Return login url to redirect the user to, state if provided will be passed to paypal, and return to your callback url

### exchange\_code( $code, \[ $state \] )

Exchange authorization code for token, token is a hash of 2 keys, access\_token and refresh\_token.

### refresh\_token( $refresh\_token )

Allow you to refresh your access token from your refresh token, it will return a hash of 2 keys, access\_token and refresh\_token.

### get\_user\_details( authorization\_code => $code, access\_token => $access\_token )

Call that method with either the authorization code or an access token to get paypal's user info, in a hash

## LOGGER

In case there is an issue on paypal side, the module with croak, so you should use it in an eval block, or better with Try::Tiny
To get some more info, you can pass a sub to the constructor in field logger. That sub will get called with debug message within the process
of calling paypal.

    my $lipp = OpenID::PayPal::LIPP->new(
        client_id => 'CLIENT_ID',
        client_secret => 'CLIENT_SECRET',
        account => 'ACCOUNT',
        mode => 'sandbox',
        redirect_uri => 'http://localhost/callback',
        logger => sub { warn @_ }
    );

# AUTHOR

Pierre VIGIER <pierre.vigier@gmail.com>

# COPYRIGHT

Copyright 2016- Pierre VIGIER

# LICENSE

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.
