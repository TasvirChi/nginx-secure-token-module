# Secure token module for Nginx [![Build Status](https://travis-ci.org/borhan/nginx-secure-token-module.svg?branch=master)](https://travis-ci.org/borhan/nginx-secure-token-module)

Generates CDN tokens, either as a cookie or as a query string parameter (m3u8,mpd,f4m only).
Currently supports Akamai v2 tokens, and Amazon CloudFront tokens.
In addition, the module supports the encryption of URIs with a configured key.

## Installation

Add `--add-module` when configuring nginx:

    ./configure --add-module=$PATH_TO_SECURE_TOKEN

Requires OpenSSL.

## Configuration

### Generic token parameters

#### secure_token
* **syntax**: `secure_token type`
* **default**: `none`
* **context**: `http`, `server`, `location`

Enables token generation of the requested type, supported types are: akamai, cloudfront

#### secure_token_window
* **syntax**: `secure_token_window window`
* **default**: `86400`
* **context**: `http`, `server`, `location`

Sets the validity time of the token in seconds

#### secure_token_end_time
* **syntax**: `secure_token_end_time time`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the end time of the token, the time has to be specified in HTTP time format (e.g. Thu, 01 Jan 1970 00:00:00 GMT).
When this parameter is set, it takes precedence over secure_token_window.

#### secure_token_ip_address
* **syntax**: `secure_token_ip_address address`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the IP address that should be embedded in the token.
The parameter value can contain variables, e.g. $remote_addr/32 can be used to limit the token to the specific IP of the client.

#### secure_token_avoid_cookies
* **syntax**: `secure_token_avoid_cookies on/off`
* **default**: `on`
* **context**: `http`, `server`, `location`

When enabled the module prefers to use a query string token instead of a cookie token.
A query string token is currently supported only for the following mime types:
* application/vnd.apple.mpegurl
* application/dash+xml
Other mime types will return a cookie token.

#### secure_token_types
* **syntax**: `secure_token_types mime_type ...`
* **default**: `none`
* **context**: `http`, `server`, `location`

Defines a set of mime types that should return a token

#### secure_token_uri_filename_prefix
* **syntax**: `secure_token_uri_filename_prefix prefix`
* **default**: `none`
* **context**: `http`, `server`, `location`

Defines a set of prefixes that will be matched against the URI file name, only URIs whose file name
starts with one of the defined prefixes will return a token

#### secure_token_expires_time
* **syntax**: `secure_token_expires_time time`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the expiration time of responses that are not tokenized 
(determines the values of the Cache-Control and Expires HTTP headers)

#### secure_token_cookie_token_expires_time
* **syntax**: `secure_token_cookie_token_expires_time time`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the expiration time of responses that are tokenized with a cookie token 
(determines the values of the Cache-Control and Expires HTTP headers)

#### secure_token_query_token_expires_time
* **syntax**: `secure_token_query_token_expires_time time`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the expiration time of responses that are tokenized with a query string token 
(determines the values of the Cache-Control and Expires HTTP headers)

#### secure_token_cache_scope
* **syntax**: `secure_token_cache_scope scope`
* **default**: `public`
* **context**: `http`, `server`, `location`

Sets the cache scope (public/private) of responses that are not tokenized

#### secure_token_token_cache_scope
* **syntax**: `secure_token_token_cache_scope scope`
* **default**: `private`
* **context**: `http`, `server`, `location`

Sets the cache scope (public/private) of responses that are tokenized (query / cookie)

#### secure_token_last_modified
* **syntax**: `secure_token_last_modified time`
* **default**: `Sun, 19 Nov 2000 08:52:00 GMT`
* **context**: `http`, `server`, `location`

Sets the value of the last-modified header of responses that are not tokenized.
An empty string leaves the value of last-modified unaltered, while the string "now" sets the header to the server current time.

#### secure_token_token_last_modified
* **syntax**: `secure_token_token_last_modified time`
* **default**: `now`
* **context**: `http`, `server`, `location`

Sets the value of the last-modified header of responses that are tokenized (query / cookie)
An empty string leaves the value of last-modified unaltered, while the string "now" sets the header to the server current time.

#### secure_token_content_type_m3u8
* **syntax**: `secure_token_content_type_m3u8 type`
* **default**: `application/vnd.apple.mpegurl`
* **context**: `http`, `server`, `location`

Sets the content type that should be parsed as m3u8 for token insertion

#### secure_token_content_type_mpd
* **syntax**: `secure_token_content_type_mpd type`
* **default**: `application/dash+xml`
* **context**: `http`, `server`, `location`

Sets the content type that should be parsed as mpd for token insertion

#### secure_token_content_type_f4m
* **syntax**: `secure_token_content_type_f4m type`
* **default**: `video/f4m`
* **context**: `http`, `server`, `location`

Sets the content type that should be parsed as f4m for token insertion

### Akamai token parameters

#### secure_token_akamai_key
* **syntax**: `secure_token_akamai_key key_hex`
* **default**: `empty`
* **context**: `http`, `server`, `location`

Sets the secret key

#### secure_token_akamai_param_name
* **syntax**: `secure_token_akamai_param_name name`
* **default**: `__hdnea__`
* **context**: `http`, `server`, `location`

Sets the token parameter name (either the name of the cookie or the query string parameter)

#### secure_token_akamai_acl
* **syntax**: `secure_token_akamai_acl acl`
* **default**: `$baseuri`
* **context**: `http`, `server`, `location`

Sets the signed part of the URL (ACL).
The parameter value can contain variables.

### CloudFront token parameters

#### secure_token_cloudfront_private_key_file
* **syntax**: `secure_token_cloudfront_private_key_file filename`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the file name of the private key (PEM file)

#### secure_token_cloudfront_key_pair_id
* **syntax**: `secure_token_cloudfront_key_pair_id id`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the key pair id

#### secure_token_cloudfront_acl
* **syntax**: `secure_token_cloudfront_acl acl`
* **default**: `$baseuri`
* **context**: `http`, `server`, `location`

Sets the signed part of the URL (ACL).
The parameter value can contain variables.

### URI encryption parameters

#### secure_token_encrypt_uri
* **syntax**: `secure_token_encrypt_uri on/off`
* **default**: `off`
* **context**: `http`, `server`, `location`

Enables/disables uri encryption

#### secure_token_encrypt_uri_key
* **syntax**: `secure_token_encrypt_uri_key key_hex`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the encryption key, the key has to be 256 bits (64 hex characters)

#### secure_token_encrypt_uri_iv
* **syntax**: `secure_token_encrypt_uri_iv iv_hex`
* **default**: `none`
* **context**: `http`, `server`, `location`

Sets the encryption iv, the iv has to be 128 bits (32 hex characters)

#### secure_token_encrypt_uri_part
* **syntax**: `secure_token_encrypt_uri_part expression`
* **default**: `none`
* **context**: `http`, `server`, `location`

An expression that calculates the part of the URL that should be encrypted in regular expression locations.
For non-regular expression locations, the encrypted part is everything following the path defined on the location block.

Example 1:
```
  location /secret_param/([^/]+)/some_other_param/.* {
  	secure_token_encrypt_uri_part $1;
	...
  }
```
  In this configuration, only the value of secret_param will be encrypted/decrypted.

Example 2:  
```
  location /base/ {
    ...
  }
```
  In this configuration, everything following /base/ will be encrypted/decrypted.
  
#### secure_token_encrypt_uri_hash_size
* **syntax**: `secure_token_encrypt_uri_hash_size size`
* **default**: `8`
* **context**: `http`, `server`, `location`

The size in bytes of hash used to validate the uri after decryption, the value has to be between 0 and 16.

## Sample configurations

### HLS packaging with Akamai tokens
```
	location ~ ^/hls/p/\d+/(sp/\d+/)?serveFlavor/ {
		vod hls;

		g2o        on;

		secure_token akamai;
		secure_token_akamai_key 1234;
		secure_token_akamai_acl "$baseuri*";
		secure_token_types application/vnd.apple.mpegurl;
		
		secure_token_expires_time 100d;
		secure_token_query_token_expires_time 1h;

		more_set_headers 'Access-Control-Allow-Headers: *';
		more_set_headers 'Access-Control-Expose-Headers: Server,range,Content-Length,Content-Range';
		more_set_headers 'Access-Control-Allow-Methods: GET, HEAD, OPTIONS';
		more_set_headers 'Access-Control-Allow-Origin: *';
	}
```

### HDS packaging with CloudFront tokens
```
	location ~ ^/hds/p/\d+/(sp/\d+/)?serveFlavor/ {
		vod hds;
		vod_segment_duration 6000;
		vod_align_segments_to_key_frames on;
		vod_segment_count_policy last_rounded;

		secure_token cloudfront;
		secure_token_cloudfront_private_key_file /path/to/pem;
		secure_token_cloudfront_key_pair_id ABCDEF;
		secure_token_cloudfront_acl "$scheme://$http_host$baseuri*";
		secure_token_types video/f4m;
		
		secure_token_expires_time 100d;
		secure_token_query_token_expires_time 1h;

		more_set_headers 'Access-Control-Allow-Headers: *';
		more_set_headers 'Access-Control-Expose-Headers: Server,range,Content-Length,Content-Range';
		more_set_headers 'Access-Control-Allow-Methods: GET, HEAD, OPTIONS';
		more_set_headers 'Access-Control-Allow-Origin: *';
	}
```

### Encrypted HLS with token security on the encryption key

This configuration enables token security while having static URLs for the video segments,
this enables the caching of the segments transparently by proxies.
```
	location ~ ^/s/hls/enc/p/\d+/(sp/\d+/)?serveFlavor/ {
		vod hls;
		vod_secret_key "password$vod_filepath";

		secure_token akamai;
		secure_token_akamai_key 1234;
		secure_token_akamai_acl "$baseuri*";
		secure_token_types application/vnd.apple.mpegurl;
		
		secure_token_expires_time 100d;
		secure_token_query_token_expires_time 1h;
		
		secure_token_uri_filename_prefix index;
		secure_token_tokenize_segments off;

		akamai_token_validate on;
		akamai_token_validate_key 1234;
		akamai_token_validate_uri_filename_prefix encryption;
		akamai_token_validate_uri_filename_prefix index;
	}
```

### Adding token security on top of an existing HDS/HLS live stream
```
	location /secure-live/ {
		proxy_pass http://original.live.domain;

		secure_token akamai;
		secure_token_akamai_key 1234;
		secure_token_akamai_acl "$baseuri*";
		secure_token_types text/xml application/vnd.apple.mpegurl;		
		secure_token_content_type_f4m text/xml;
		
		secure_token_expires_time 100d;
		secure_token_query_token_expires_time 1h;

		akamai_token_validate on;
		akamai_token_validate_key 1234;
		akamai_token_validate_strip_token on;
	}
```

### URI encryption
```
	location ~ ^/hls/p/\d+/(sp/\d+/)?serveFlavor/entryId/([^/]+)/(.*) {
		vod hls;
		vod_secret_key "password$2";

		secure_token_encrypt_uri on;
		secure_token_encrypt_uri_key 000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1f;
		secure_token_encrypt_uri_iv 00000000000000000000000000000000;
		secure_token_encrypt_uri_part $3;
		secure_token_types application/vnd.apple.mpegurl;

		add_header Last-Modified "Sun, 19 Nov 2000 08:52:00 GMT";
		expires 100d;
	}
```

## Copyright & License

All code in this project is released under the [AGPLv3 license](http://www.gnu.org/licenses/agpl-3.0.html) unless a different license for a particular library is specified in the applicable library path. 

Copyright © Borhan Inc. All rights reserved.
