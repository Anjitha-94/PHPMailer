# DGL 104: App Foundation
 ## Community code Project  The Selected Issue for solving
 Issue: Already having the token, send mail using ms office oauth2 authentication #2813

 https://github.com/PHPMailer/PHPMailer/issues/2813


 The code for the given issue

 ```
 <?php

class OAuthCredentialEncoder implements \PHPMailer\PHPMailer\OAuthTokenProvider {
    private $userEmail;
    private $token;

    public function __construct($userEmail, $token) {
        $this->userEmail = $userEmail;
        $this->token = $token;
    }

    public function getEncodedCredentials() {
        $credentialString = implode("\001", [
            "user={$this->userEmail}",
            "auth=Bearer {$this->token}",
            ""
        ]);

        return base64_encode($credentialString);
    }
}
```


This PHP code snippet defines a class named OAuthCredentialEncoder that implements the OAuthTokenProvider interface from the PHPMailer\PHPMailer namespace. The purpose of this class is to facilitate OAuth2 authentication with an SMTP server for sending emails through PHPMailer, by encoding OAuth credentials in a specific format. Below is a detailed explanation of each component of the class:

## Class Definition

class OAuthCredentialEncoder implements \PHPMailer\PHPMailer\OAuthTokenProvider: This line declares a new class named OAuthCredentialEncoder that implements the OAuthTokenProvider interface from PHPMailer. Implementing this interface requires the class to define certain methods that PHPMailer can call to obtain OAuth authentication credentials.

## Properties

* private $userEmail;: A private property that stores the user's email address. Being private, it can only be accessed within this class.
* private $token;: Another private property that holds the OAuth token used for authentication. This token is typically provided by the OAuth2 authorization server.
### Constructor

public function __construct($userEmail, $token) { ... }: The constructor method is executed when a new instance of OAuthCredentialEncoder is created. It takes two parameters: $userEmail, which is the user's email address, and $token, which is the OAuth token. These parameters are then assigned to the class's properties.
### Methods

public function getEncodedCredentials() { ... }: This public method is defined as per the contract of the OAuthTokenProvider interface. It's responsible for returning the OAuth credentials in a base64-encoded format that PHPMailer can use for SMTP authentication. 
* The method: Constructs a credential string by combining the user email and OAuth token with a specific delimiter ("\001"). The format used is "user={userEmail}\001auth=Bearer {token}\001\001". This format is a requirement for some SMTP servers that support OAuth2 authentication.
Encodes the constructed string in base64 and returns it. Base64 encoding is often used in email authentication to ensure safe transmission of data over networks.
### Usage and Functionality

The OAuthCredentialEncoder class is specifically designed for use with PHPMailer, a popular library for sending emails in PHP. When PHPMailer is configured to use SMTP with OAuth2 authentication, it requires an instance of a class that implements the OAuthTokenProvider interface. This interface should provide the necessary authentication credentials in the expected format.


By instantiating the OAuthCredentialEncoder class with the user's email and OAuth token, and passing it to PHPMailer's setOAuth method, developers can seamlessly integrate OAuth2 SMTP authentication into their email sending process. This method of authentication is more secure and preferred for services that support it, such as Google's Gmail SMTP server, compared to traditional username and password authentication.


In summary, the OAuthCredentialEncoder class serves as a bridge between PHPMailer and an SMTP server that requires OAuth2 authentication, enabling developers to send emails securely using PHPMailer with OAuth2 tokens.



## Use this code for  in your mailer

```
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\OAuth;

require 'path/to/PHPMailer/src/PHPMailer.php';
require 'path/to/PHPMailer/src/OAuth.php';
// Assuming the OAuthCredentialEncoder class is defined in the same script or autoloaded

// Instantiate your OAuthCredentialEncoder with user email and token
$encoder = new OAuthCredentialEncoder('user@example.com', 'your_access_token_here');

// Create a new PHPMailer instance
$mail = new PHPMailer();

// Configure PHPMailer to use SMTP and set your SMTP options here
$mail->isSMTP();
$mail->Host = 'smtp.example.com'; // Set the SMTP server to send through
$mail->SMTPAuth = true; // Enable SMTP authentication
$mail->SMTPSecure = PHPMailer::ENCRYPTION_STARTTLS;
$mail->Port = 587; // TCP port to connect to

// Set the OAuth
$mail->AuthType = 'XOAUTH2';
$mail->setOAuth(new OAuth([
    'provider' => $encoder,
    'clientId' => 'YOUR_CLIENT_ID',
    'clientSecret' => 'YOUR_CLIENT_SECRET',
    'refreshToken' => 'YOUR_REFRESH_TOKEN',
    'userName' => 'user@example.com',
]));

// Recipients
$mail->setFrom('from@example.com', 'Mailer');
$mail->addAddress('recipient@example.com', 'Joe User'); // Add a recipient

// Content
$mail->isHTML(true); // Set email format to HTML
$mail->Subject = 'Here is the subject';
$mail->Body    = 'This is the HTML message body <b>in bold!</b>';
$mail->AltBody = 'This is the body in plain text for non-HTML mail clients';

// Send the email
try {
    $mail->send();
    echo 'Message has been sent';
} catch (Exception $e) {
    echo "Message could not be sent. Mailer Error: {$mail->ErrorInfo}";
}
```
# PHPMailer with OAuth2 Authentication Guide

This guide explains how to send an email using PHPMailer with OAuth2 for SMTP authentication, utilizing a custom `OAuthCredentialEncoder` class to manage the OAuth2 process.

## Preliminaries

### Namespaces

The script imports classes from the PHPMailer library using PHP's `use` statement, making the `PHPMailer`, `SMTP`, and `Exception` classes available without full namespace references.

### Autoloader

Includes the Composer autoloader with `require 'vendor/autoload.php';` to load the PHPMailer library and any other Composer-managed dependencies.

## Requirements

### OAuthCredentialEncoder

Assumes the presence of an `OAuthCredentialEncoder` class in 'OAuthCredentialEncoder.php', which is required for encoding the user's email and OAuth2 token for SMTP authentication.

## Main Components

### OAuthCredentialEncoder Initialization

Creates an instance of `OAuthCredentialEncoder` with the user's email and OAuth2 token, used later to generate base64-encoded credentials for SMTP server authentication.

### PHPMailer Setup

- Initializes a new `PHPMailer` instance with exception handling enabled to catch and manage exceptions.
- Configures PHPMailer to use SMTP, specifying the SMTP host, enabling SMTP authentication, and setting the authentication type to 'XOAUTH2'.
- Sets the OAuth2 credentials using `setOAuth` on the PHPMailer instance, via an anonymous class implementing the `OAuthTokenProvider` interface, wrapping the encoded credentials from `OAuthCredentialEncoder`.

### Email Composition

- Uses `setFrom` to specify the sender's email and name.
- Adds a recipient with `addAddress`, specifying their email and name.
- Sets the email format to HTML with `isHTML(true)`.
- Defines the email's subject, HTML body, and plain text body with `Subject`, `Body`, and `AltBody`.

### Sending the Email

- Sends the email with `send()`, echoing a success message upon completion.
- Catches and displays errors with a try-catch block in case of sending failures.

## Exception Handling

Employs a try-catch block around email sending operations to handle exceptions gracefully, such as connectivity issues or authentication failures.

## Important Notes

- Replace placeholder values like 'your.email@example.com', 'your_oauth2_token_here', and 'smtp.example.com' with actual data.
- The `SMTPDebug` property can be enabled for troubleshooting to provide detailed debug output.

This script demonstrates a secure method to send emails using PHPMailer with OAuth2 authentication, avoiding the need to store or transmit passwords in plain text.




